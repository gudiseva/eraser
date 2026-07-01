<p><a target="_blank" href="https://app.eraser.io/workspace/lYktFLUGAL62AqrW6S1t" id="edit-in-eraser-github-link"><img alt="Edit in Eraser" src="https://firebasestorage.googleapis.com/v0/b/second-petal-295822.appspot.com/o/images%2Fgithub%2FOpen%20in%20Eraser.svg?alt=media&amp;token=968381c8-a7e7-472a-8ed6-4a6626da5501"></a></p>



# Interview Topics (as provided by client)
- [ ] Rest API call
- [ ] Java 21 features
- [ ] Sql indexes, methods to get fast retrievals
- [ ] Functional interface
- [ ] Hashmap working
- [ ] Data structure
- [ ] Class and objects


#### Functional Interface
- contains one and **only one abstract method**
- can contain many default methods -> introduced in Java 8
- can contain many static methods -> introduced in Java 8
- Annotation -> @FunctionalInterface
- used in Lambda Functions -> only 1 function is allowed in lambda
- 


# **Phase 1: Java 8 (1 week)**
This is the most important jump.

Learn:

- Lambda Expressions
- Functional Interfaces
- Streams API
- Method References
- Optional
- Date & Time API (`java.time` )


Example:

Java 7:

`for(Employee e : employees) {
 if(e.getSalary() > 100000)
 result.add(e);
} ` 



Java 8:

`employees.stream()
 .filter(e -> e.getSalary() > 100000)
 .toList();` 



Most modern Java code uses Streams heavily.



## Base64 encoding and decoding
1. Base64 is an encoding scheme that converts binary data into text format so that encoded textual  data can be easily transported over network un-corrupted and without any data loss.
2. The basic encoding means no line feeds are added to the output and the output is mapped to a set of characters in A-Za-z0-9+/


![image.png](/.eraser/lYktFLUGAL62AqrW6S1t___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___image_VjYVriqm95iXbpeWS_13M.png "image.png")



## JShell
- Similar to IDLE of Python and REPL of Javascript
- Line by line code can be executed in Java


![image.png](/.eraser/lYktFLUGAL62AqrW6S1t___bPZQNaXvlNf0VqwMkPQWuX4sdKb2___image_q9BFVWR_7jrm2GUcGZGRM.png "image.png")





# **Phase 2: Java 9–17 (2 weeks)**
Focus on:

### **var**
`var name = "Nag";` 

### **Records**
`public record Employee(
 int id,
 String name
) {}` 

No getters/setters required.

### **Text Blocks**
`String sql = """
SELECT *
FROM EMPLOYEE
""";` 

### **Switch Expressions**
`String result =
 switch(status) {
 case "A" -> "Active";
 case "I" -> "Inactive";
 default -> "Unknown";
 };` 



#### Java 7 : Switch Statement
- Until Java 7, only Integers could be used in switch case


#### Java 8 : Switch Statement
- In Java 8, Strings and enums were introduced in case value


#### Java 12 : Switch Expressions
1. You can return values from a switch block and hence switch statements became switch expresssions
2. You can have multiple values in a case label
3. You can return value from a switch expression through the arrow operator or through the "break" keyword.


#### Switch expression features
> Pattern matching
Null cases



### **Sealed Classes**
Useful in domain modelling.



# **Phase 3: Java 17–21 (1 week)**
Learn:

### **Virtual Threads (Huge)**
`try(var executor =
 Executors.newVirtualThreadPerTaskExecutor()) {
}` 

This is one of the biggest Java innovations in recent years.

### **Pattern Matching**
`if(obj instanceof String s) {
 System.out.println(s);
}` 

### **Structured Concurrency**
(Understand conceptually)



# **Phase 4: Spring Boot (3 weeks)**
This is where most of your effort should go.

Learn:

### **REST APIs**
`@RestController
public class CustomerController {
}` 

### **Dependency Injection**
`@Service
@Repository
@Component` 

### **Spring Data JPA**
`public interface CustomerRepository
extends JpaRepository<Customer, Long> {
}` 

### **Exception Handling**
`@ControllerAdvice` 

### **Validation**
`@NotNull
@Email` 

### **Security Basics**
- JWT
- OAuth2
- Keycloak


# **Phase 5: Spring Batch (1 week)**
This requirement specifically mentions Spring Batch.

Since you already know ETL:

| **ETL Concept** | **Spring Batch** |
| ----- | ----- |
| Source | Reader |
| Transformation | Processor |
| Target | Writer |
| Workflow | Job |
| Step | Step |
You should become productive very quickly.



# **Phase 6: Build a Migration Project (2 weeks)**
This will help in interviews.

Create:

**Informatica/Pentaho → Spring Batch Migration Demo**

Example:

CSV → Transform → PostgreSQL

Features:

- Spring Boot
- Spring Batch
- REST API
- Docker
- Java 21
Push to GitHub.

This aligns directly with the Sagarsoft requirement.





<!--- Eraser file: https://app.eraser.io/workspace/lYktFLUGAL62AqrW6S1t --->