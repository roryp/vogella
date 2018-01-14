## Part 1 – Basic

Create Maven project and JUnit tests that do the following:
Using JavaDB (Derby) ,create an application that

1. Cleans the database by deleting all rows
2. inserts “Student” records and then retrieves the records and asserts that they are correct

#### Maven dependency

```xml
<dependency>
	<groupId>org.apache.derby</groupId>
	<artifactId>derbyclient</artifactId>
	<version>10.10.2.0</version>
</dependency>
```



## Part 2 – Advanced

Use a [DAO Design Pattern](https://www.tutorialspoint.com/design_pattern/data_access_object_pattern.htm) to implement database multi-tenancy and file based data storage

 

# Solution

<https://github.com/roryp/StudentDB>