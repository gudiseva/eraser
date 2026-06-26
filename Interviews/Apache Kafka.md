<p><a target="_blank" href="https://app.eraser.io/workspace/y36760BTrcmtGcY8YCP9" id="edit-in-eraser-github-link"><img alt="Edit in Eraser" src="https://firebasestorage.googleapis.com/v0/b/second-petal-295822.appspot.com/o/images%2Fgithub%2FOpen%20in%20Eraser.svg?alt=media&amp;token=968381c8-a7e7-472a-8ed6-4a6626da5501"></a></p>



# Apache Kafka — Architect's Concept Review + Diagrams
_Compiled for Enterprise Architect interview prep. Structured along the Learning Journal "Apache Kafka for Absolute Beginners" course but pitched at architect depth. Includes six diagrams written in_ **_Eraser diagram-as-code_**_._

## How to use the diagrams in eraser.io
Each diagram below sits in a fenced code block tagged as Eraser diagram-as-code. To bring one into Eraser: create a new diagram in your Eraser file, switch to the **code** pane, and paste the block (everything inside the fence). Eraser auto-lays-out and styles it. The `// comments` are harmless and can stay. If you prefer, paste a block straight into [DiagramGPT](https://www.eraser.io/diagramgpt) and choose _flowchart_.

---

## 1. What Kafka is, and why it exists
**One line:** Apache Kafka is a distributed, partitioned, replicated **commit log** you read and write as a real-time stream. The log _is_ the data structure; ordering, replay, fan-out, and retention all fall out of that design.

**The problem it solves:** point-to-point integration scales as O(N²) connections. Kafka inverts this into a publish/subscribe backbone — producers write once to a topic, any number of consumers read independently. You move from a mesh of bespoke pipes to a hub. This is the strongest architect framing: Kafka is the central nervous system that decouples producers from consumers in time, volume, and schema.

**Three things a traditional MQ does not give you:**

1. **Replay** — messages persist for a retention window, so a new or recovered consumer can re-read history.
2. **Multiple independent consumers** — each group has its own offset; fan-out is free.
3. **Horizontal scale via partitioning** — throughput scales with partition count, not vertically.
### Diagram — Kafka as a decoupling hub
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---r4_ITlERmrebxyN0q0YyD---id---QQ5_--YNwptll-Ge6hNZA.png "Flowchart")



---

## 2. Core abstractions
| Concept | What it is |
| ----- | ----- |
| Topic | A named stream of records; a logical category. |
| Partition | The unit of parallelism and ordering; a topic is split into 1..N partitions. |
| Offset | <p>A monotonically increasing ID of a record </p><p>_within a partition_</p><p>. Ordering is guaranteed only within a partition.</p> |
| Record | Key, value, timestamp, headers. The key drives partitioning. |
| Broker | A single Kafka server; a cluster is many brokers. |
| Producer | Writes records to topics. |
| Consumer | Reads records, tracks position via committed offsets. |
| Consumer group | A set of consumers that collaboratively read a topic; each partition is read by exactly one consumer in the group. |


**The single most important sentence:** _ordering is per-partition, and the key determines the partition._ Almost every Kafka design question reduces to choosing the key so related events land on the same partition (and stay ordered) while still spreading load.

### Diagram — Anatomy of a topic
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---e1XahVB6a3ovMP7mZFYjs---id---ElZYTgUDaNfTaiL5BUuF2.png "Flowchart")



---

## 3. Architecture and the cluster
**Brokers and partitions.** Each partition has one **leader** broker and zero or more **follower** replicas. All reads and writes for a partition go through the leader; followers replicate for fault tolerance. Partitions and leadership are spread across brokers for balance.

**Replication and ISR (In-Sync Replicas).** Replication factor 3 means three copies of each partition. The **ISR** is the subset of replicas currently caught up with the leader. On leader failure, a new leader is elected **from the ISR** — a stale follower is not eligible, preventing data loss (unless you enable unclean leader election, which trades durability for availability).

**Controller.** One broker acts as controller for leader election and partition state.

**ZooKeeper vs KRaft (a current-events question).** Classic Kafka kept cluster metadata in ZooKeeper. Modern Kafka replaces it with **KRaft** (an internal Raft metadata quorum) — fewer moving parts, faster failover, larger partition scale. The course teaches ZooKeeper; flag that production has moved to KRaft.

**Write path.** Producer → partition leader appends to its log → ISR followers fetch and append → once enough replicas acknowledge (governed by `acks`), the write is committed. Kafka leans on the OS page cache, sequential disk I/O, and zero-copy reads (`sendfile`) for throughput.

### Diagram — Replication and ISR
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---aKcwB063TyOEP7eR_J7RT---id---abUDccUeSmrp-nS11pKjz.png "Flowchart")



---

## 4. The producer
**Partitioning logic.** With a key → `partition = hash(key) % numPartitions` (same key → same partition → ordered). No key → sticky/round-robin for balance. Custom partitioners allowed.

`**acks**` **— the durability dial:**

- `acks=0`  — fire and forget. Lowest latency, possible loss (metrics).
- `acks=1`  — leader acknowledges before replication. Loss possible if leader dies before followers catch up.
- `acks=all`  (`-1` ) — leader waits for all ISR. Strongest durability. Pair with `min.insync.replicas=2`  (on RF=3) so a write fails rather than silently under-replicating.
**Idempotent producer** (`enable.idempotence=true`): producer ID + per-partition sequence numbers make retries safe (no duplicates). Default in current versions; prerequisite for transactions.

**Transactions / exactly-once (EOS):** write to multiple partitions atomically and commit consumer offsets in the same transaction (read-process-write). Real, but scoped to **within-Kafka** processing — it does not make an external DB call idempotent.

**Other knobs:** `batch.size` + `linger.ms` (batching), `compression.type` (lz4/zstd/snappy), `retries`, `max.in.flight.requests.per.connection` (≤5 with idempotence to preserve ordering).

---

## 5. The consumer
**Consumer groups and parallelism.** Within a group, **each partition is assigned to exactly one consumer**. So max useful parallelism in a group = partition count; more consumers than partitions → idle consumers. Partition count is a capacity decision made early.

**Offsets and commit strategy.** Offsets are stored in the internal `__consumer_offsets` topic. Auto-commit is convenient but can lose or duplicate on failure; manual `commitSync`/`commitAsync` gives control. Commit after processing → at-least-once; before → at-most-once.

**Rebalancing.** When a consumer joins/leaves, the group coordinator reassigns partitions; a stop-the-world rebalance pauses consumption. Mitigations: cooperative/incremental rebalancing and static membership (`group.instance.id`).

**Consumer lag** = log-end-offset − committed-offset. The key health metric — rising lag means consumers can't keep up.

### Diagram — Consumer group assignment
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---n7KeQ41yY7fpKaBiJzWAK---id---gCtZKMHZ7I7eqt_tdYP50.png "Flowchart")



A second group (say "analytics") would read the same partitions with its own independent offsets — free fan-out.

---

## 6. Delivery semantics — the commit-order intuition
The guarantee is decided by **when you commit the offset relative to processing**:

- **At-most-once** — commit _before_ processing. Crash in between → record lost. Never duplicates.
- **At-least-once** — commit _after_ processing. Crash in between → reprocessed on restart. Never loses; may duplicate. _The practical default._
- **Exactly-once** — wrap process-and-commit in one transaction (+ idempotent producer). No loss, no duplicates, **within Kafka**. For external systems, achieve effectively-once via idempotent/upsert writes on a key.
### Diagram — Delivery semantics
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---ejpqZrGLjoJ-_Pnvg0HyY---id---u0MKXVGcXLVC2utDFu-ju.png "Flowchart")



---

## 7. Retention and compaction — events vs state
**Time/size retention** (`retention.ms`, `retention.bytes`) deletes old segments. Default for event streams.

**Log compaction** (`cleanup.policy=compact`) retains at least the latest value per key — turning a topic into a changelog / materialized view (e.g., current state of each customer). `__consumer_offsets` itself is compacted.

The architect payoff: a compacted topic is effectively an event-sourced table you can replay to rebuild state — the foundation of CDC pipelines and Kafka Streams state stores. Logs are split into **segments**; retention and compaction operate at segment granularity.

### Diagram — Retention vs compaction
![Flowchart](/.eraser/y36760BTrcmtGcY8YCP9___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___---diagram---HxQdyVsZq2Eut-ARr4uPU---id---0n5c1O-tLBplv6cWn5wsW.png "Flowchart")



---

## 8. Kafka Connect — the integration architect's tool
A framework for streaming data in and out of Kafka without writing producer/consumer code. **Source** connectors pull into Kafka; **sink** connectors push out (JDBC, S3, Elasticsearch, etc.).

- Runs in **distributed mode** with workers; scales and rebalances tasks like a consumer group.
- **Single Message Transforms (SMT)** for lightweight in-flight changes (mask, route, rename); heavy transformation belongs in Streams/ksqlDB.
- **CDC via Debezium** (source connectors reading DB transaction logs) is the canonical pattern for getting database changes into Kafka — directly relevant to financial-services integration.
- Pairs with **Schema Registry** to enforce contracts at the connector boundary.
**Connect vs NiFi:** NiFi excels at complex routing, flow management, and edge ingest with a visual model and back-pressure; Connect excels at high-throughput, schema-governed, horizontally-scaled pipelines tied to the log. Complementary — many shops run NiFi at the edge feeding Kafka.

---

## 9. Schema management — Avro and Schema Registry
Because producers and consumers are decoupled, the **schema is the contract**. Schema Registry stores Avro/Protobuf/JSON-Schema definitions, versions them, and enforces compatibility (BACKWARD, FORWARD, FULL). Producers validate on write; consumers fetch the schema by the ID embedded in the message. Backward compatibility lets consumers upgrade on their own timeline — essential across many teams.

---

## 10. Stream processing — Kafka Streams and ksqlDB
**Kafka Streams** is a client library (not a separate cluster) for stateful processing in Java/Scala. **KStream** (event stream) vs **KTable** (changelog / latest-value view) mirror the compaction idea. Operations: map, filter, join (stream-stream, stream-table), windowed aggregations. **State stores** are backed by changelog topics → fault-tolerant local state, exactly-once via transactions. Scales by running more instances (partition-based parallelism).

**ksqlDB** is a SQL layer over Kafka Streams. **vs Spark/Flink:** Streams is lightweight and embedded — great for per-service logic; Flink/Spark are full engines for heavier cross-source analytics. Don't claim Streams replaces Flink.

---

## 11. Operations, security, sizing
**Partition sizing** is driven by target throughput and desired consumer parallelism: estimate per-partition ceiling, divide target by it, round up, leave headroom. Too few = limited parallelism; too many = more open files and slower rebalances/elections.

**Replication:** RF=3 with `min.insync.replicas=2` is the standard durable production config. Spread replicas across racks/AZs (`broker.rack`).

**Security (four layers):** (1) TLS in transit; (2) authentication via SASL (SCRAM, GSSAPI/Kerberos, OAUTHBEARER) or mTLS; (3) authorization via ACLs; (4) encryption at rest (disk/volume — not native to Kafka).

**Monitoring:** consumer lag, under-replicated partitions (target 0), ISR shrink/expand, request latency, broker disk/CPU/network. JMX → Prometheus/Grafana; Cruise Control for rebalancing; AKHQ/Kafka Manager for inspection. **Managed:** Confluent Cloud, AWS MSK — you still own topic design, partitioning, and schema governance.

---

## 12. Design patterns to name unprompted
- **Event-driven microservices** — services emit events, others react.
- **Event sourcing + CQRS** — the log is the source of truth; compacted topics / KTables build read models.
- **CDC (Debezium → Kafka)** — turn DB changes into a stream without dual-writes.
- **Outbox pattern** — write business data + an outbox row in one DB transaction, then CDC the outbox to Kafka. Solves the dual-write problem between a DB and Kafka. (Strong answer to "how do you avoid losing events when the DB commit succeeds but the Kafka write fails?")
- **Dead Letter Queue (DLQ)** — route un-processable records aside instead of blocking the partition.
- **Topic-per-entity vs topic-per-event-type** — a real granularity trade-off.
---

## 13. Rapid-fire Q&A
**Ordering guarantee?** Per-partition only; same key hashes to the same partition and is read in offset order. No global ordering — by design, to enable scale.

**Scale consumers?** Add consumers to a group up to the partition count; beyond that they idle. Partition count caps parallelism, so plan it early.

**at-least-once vs exactly-once?** At-least-once = commit after processing, tolerate duplicates with idempotent downstream writes. Exactly-once = idempotent producer + transactions for read-process-write within Kafka; effectively-once externally via upserts.

**ISR and why care?** In-sync replicas caught up with the leader; leaders are elected only from the ISR, preventing data loss. `min.insync.replicas` blocks a write unless enough replicas have it.

**acks settings?** 0 = none (fast, lossy), 1 = leader only, all = full ISR (durable). Durable default: `acks=all` + RF=3 + `min.insync.replicas=2`.

**Prevent loss end to end?** Producer `acks=all` + idempotence; RF=3, `min.insync.replicas=2`, unclean leader election off; consumer commits after success; downstream idempotency; outbox for DB↔Kafka atomicity.

**Compaction vs retention?** Retention deletes by age/size; compaction keeps the latest value per key → a changelog/materialized view. Compaction for state/CDC topics, retention for event streams.

**ZooKeeper or KRaft?** Course teaches ZooKeeper; modern Kafka uses KRaft (built-in Raft quorum) for simpler ops and faster failover.

**Kafka vs RabbitMQ?** Kafka = durable, replayable, log-based, high-throughput, multiple independent consumers, scale via partitions. MQ = broker tracks per-message delivery/ack, usually deletes on consume, richer per-message routing, less replay. Kafka for event streaming/replay/volume; MQ for complex routing and per-message workflow.

**Why so fast?** Sequential disk writes, OS page cache, zero-copy reads, batching, compression, partition-level parallelism.

**Consumer lag?** Latest offset minus committed offset; rising lag is the early warning of capacity or downstream slowness.

**Poison message?** DLQ topic + bounded retries; never let one bad record block a partition.

---

## 14. Closing framing for the interview
When it goes architectural, reason in **trade-offs and governance**, not knobs:

- Decoupling and the O(N²) → hub argument.
- Partitioning as the lever balancing ordering vs parallelism.
- Durability as a tunable spectrum (`acks` , RF, ISR) chosen per use case.
- Schema Registry + compatibility as the organizational contract that lets teams move independently.
- Kafka as the spine; Connect/NiFi at the edges; Streams/Flink for processing — right tool per layer.
- Reliability patterns (outbox, idempotency, DLQ) that assume distributed systems fail.
Fluent in internals, but reasoning in trade-offs and enterprise impact — that's the architect answer.

---

_Good luck today, NAG._





## Introduction
Apache Kafka is a distributed streaming platform.

- create real-time data streams
- process real-time data streams


Apache Kafka is a highy scalable, and distributed platform for creating and processing streams in real-time.



## Pub Sub Architecture
Publisher -> Broker -> Subscriber / Consumer



<!--- Eraser file: https://app.eraser.io/workspace/y36760BTrcmtGcY8YCP9 --->