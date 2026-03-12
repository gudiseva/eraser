<p><a target="_blank" href="https://app.eraser.io/workspace/ELrUkD3XiECyUNC22bZw" id="edit-in-eraser-github-link"><img alt="Edit in Eraser" src="https://firebasestorage.googleapis.com/v0/b/second-petal-295822.appspot.com/o/images%2Fgithub%2FOpen%20in%20Eraser.svg?alt=media&amp;token=968381c8-a7e7-472a-8ed6-4a6626da5501"></a></p>



# Introduction
- Events, not things
- Processing events in real-time
- Remember events
- Schema
- Difference between Apache Kafka and Confluent Kafka
## Data Streaming Platform
- Stream
- Stream processing
- Governance
- Connect


# Topics
- Kafka uses a log to store sequence of data items (messages or events)
- They are added to the end
- Messages already put are not changed
- Logs are known as topics.
- These are immutable.  You can forget, but not overwrite.


- Sequence of immutable messages in the topic
- There can be thousands of topics
- Topics are schemaless, they are bytes
- AVRO, Protocol Buffers, JSONs, etc
- Since data is immutable, read from first topic and write to the second topic with filter.
- Kafka is not a queue.  It's a log.  The difference is if you read from queue, it is gone.  But, it a log, you can read it, someone else can read it.


**Kafka** has a:

1. Message
2. Key
3. Timestamp
4. Headers
5. Topic
6. Partition
7. Offset
- Key is like a sensor Id, e.g. in IoT.  Key is not mandatory, but is used.
- Key should be used carefully to distribute data across the nodes efficiently in a cluster.
- Timestamp is published by the producer at the time when the mesage was produced.  If not, Kafka would assign the timestamp.
- Headers are lightweight map of key value pairs
- Offset starts with 0 and increments by 1 for each message that is produced.


### Log retention and compaction
Log **retention**: delete old data or trim logs by size

Log **compaction**: keep only the latest value per key

- Data retention by default is 7 days.  It can be retained forever.






<!--- Eraser file: https://app.eraser.io/workspace/ELrUkD3XiECyUNC22bZw --->