Table of Contents

- [1. Overview](#1. Overview)
- [2. Dependency Injection](#2. Dependency Injection)
- [3. Tooling](#3. Tooling)
- [4. Spring Data JPA](#4. Spring Data JPA)
- [5. Exercise - Spring DI Java Config](#5. Exercise - Spring DI Java Config)
- [6. Exercise - Spring DI Bean Config](#6. Exercise - Spring DI Bean Config)
- [7. Exercise - Spring DI Autowire Beans](#7. Exercise - Spring DI Autowire Beans)
- [8. Exercise - Spring DI Qualifier](#8. Exercise - Spring DI Qualifier)
- [9. Exercise - Spring Data JPA](#9. Exercise - Spring Data JPA)
- [10. Exercise - Spring Data Rest](#10. Exercise - Spring Data Rest)
- [11. Exercise - Creating an UI Frontend](#11. Exercise - Creating an UI Frontend)
- [12. Exercise - Install and use NPM](#12. Exercise - Install and use NPM)
- [13. Exercise - Configure Webpack and use Babel](#13. Exercise - Configure Webpack and use Babel)
- [14. Exercise - Using rest.js](#14. Exercise - Using rest.js)
- [15. Exercise - Creating a UI frontend with react](#15. Exercise - Creating a UI frontend with react)

## 1. Overview

The Spring Framework is a very comprehensive framework.

Spring consists of several modules for different use cases.

See [Spring modules overview](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/overview.html#overview-modules).

The fundamental functionality provided by the Spring Container is dependency injection. Spring provides a light-weight container, e.g. the Spring core container, for dependency injection (DI).

This container lets you inject required objects into other objects. This results in a design in which the Java classes are not hardly coupled. The injection in Spring is either done via setter, field or constructor injection.

These classes which are managed by Spring must conform to the Java bean standard.

In the context of Spring classes are also referred to as beans or as spring beans.

The Spring core container:

- Handles the configuration, generally based on annotations or on an XML file (XMLBeanFactory)
- Manages the selected Java classes via the BeanFactory

The core container uses the so-called bean factory to create new objects. New objects are generally created as Singletons if not specified differently.

## 2. Dependency Injection

Java components / classes should be as independent as possible to other Java classes. This increases the possibility to reuse these classes and to test them independently of other classes(Unit Testing). To decouple Java components from other Java components the dependency to a certain other class should get injected into them rather that the class itself creates / finds this object.

A class *A* has a dependency to class *B* if class uses class *B* as a variable.

If dependency injection is used then the class *B* is given to class *A* via

- The constructor of the class *A* - this is then called construction injection
- A setter - this is then called setter injection

The general concept of dependency injection is called Inversion of Control. A class should not configure itself but should be configured from outside.

A design based on independent classes / components increases the re-usability and possibility to test the software. For example, if a class A expects a Dao (Data Access object) for receiving the data from a database you can easily create another test object which mocks the database connection and inject this object into A to test A without having an actual database connection.

A software design based on dependency injection is possible with standard Java.

Spring just simplifies the use of dependency injection by providing a standard way of providing the configuration and by managing the reference to the created objects.

## 3. Tooling

Pivotal itself provides the Spring Tool Suite (STS), which can be downloaded here: <https://spring.io/tools/sts/all>

![sts download](http://www.vogella.com/tutorials/Spring/img/xsts-download.png.pagespeed.ic.sBb5mSVYN5.webp)

## 4. Spring Data JPA

Spring Data JPA provides the possibility to omit all the boilerplate code, which has to be written for DAO classes for domain types (entities). So rather than implementing data access methods, e.g., CRUD methods, for different domain types over and over again, only a repository interface has to be defined and the concrete implementation is generated at application startup automatically by Spring.

### 4.1. Common Spring Data JPA repository implementations

Provided interfaces for repositories are:

![spring data repositories](http://www.vogella.com/tutorials/Spring/img/xspring-data-repositories.png.pagespeed.ic.jSrgulAinq.webp)

| Type                                     | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| `Repository<T, ID extends Serializable>` | Marker interface for all Repository implementations |
| `CrudRepository<T, ID extends Serializable>` | Provides commonly used CRUD operations for a domain type <T> |
| `PagingAndSortingRepository<T, ID extends Serializable>` | Adds paging and sorting capabilities to the CrudRepository |
| `JpaRepository<T, ID extends Serializable>` | Adds possibility for batch processing of JPA calls. |

### 4.2. Custom Spring Data JPA repository query methods

Besides the given default repositories` methods, also custom methods can be specified in a custom repository interface.

Spring Data JPA is able to parse method names, tries to guess its purpose and tries to generate a proper implementation.

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    List<Todo> getBySummary(String summary);
}
```

The `findBySummary` method will find a list of todo objects by summary.

Interchangeable query verbs are:

- find
- get
- read

| !    | So `getBySummary` or `readBySummary` would deliver the same query results as `findBySummary`. |
| ---- | ---------------------------------------- |
|      |                                          |

| !    | Besides that another query verb is `count`, which returns the count of a query rather than the resulting objects. |
| ---- | ---------------------------------------- |
|      |                                          |

Also the method’s return type is interchangeable, so the actual query is also determined by the return type.

| Type                            | Description                              |
| ------------------------------- | ---------------------------------------- |
| Plain Type                      | Returns the plain type or `null` in case nothing is found |
| `Optional<T>` (Guava or Java 8) | Returns an Optional<T> containing the actual object or an empty optional |
| `List<T>`                       | Returns a `List<T>` of found objects or an empty list in case nothing is found |
| `Stream<T>`                     | Returns a `Stream<T>` of found objects or an empty stream in case nothing is found |

Properties for a query can also be chained.

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    List<Todo> findBySummaryOrDescription(String summary, String description);
}
```

| !    | The name of the parameters are irrelevant, but the order of the parameters must fit to the parameters mentioned in the method’s name. |
| ---- | ---------------------------------------- |
|      |                                          |

### 4.3. Creating async queries with @Async

By using the `@org.springframework.scheduling.annotation.Async` annotation it is possible to return a `Future<T>`or a Java 8 `CompletableFuture<T>`.

```java
public interface TodoRepository extends CrudRepository<Todo, Long> {

    @Async
    CompletableFuture<List<Todo>> findBySummaryAsyncJava8(String summary);

    @Async
    Future<List<Todo>> findBySummaryAsyncBeforeJava8(String summary);
}
```

### 4.4. Using @Query for special SQL queries

The query DSL derived from method signatures is really powerful and easy to handle. But if some query cannot be expressed by the method signature the `org.springframework.data.jpa.repository.Query` annotation can be used.

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    @Query("Select t from Todo t where t.summary like 'a%'")
    List<Todo> findAllTodosWithSummariesBeginningWithA();
}
```

### 4.5. Using the EntityManager in Spring Data JPA

Sometimes even the `@Query` annotation is not sufficient for enhanced queries.

Therefore it is possible to provide a real implementation for a repository interface, even without implementing the repository interface.

Because the naming convention is the only thing that really counts, e.g., the `TodoRepository` class implementation would be called `TodoRepositoryImpl`.

| !    | `Impl` is the default postfix, which can also be changed by using`@EnableJpaRepositories(repositoryImplementationPostfix="Helper")` in the `Config` class |
| ---- | ---------------------------------------- |
|      |                                          |

## 5. Exercise - Spring DI Java Config

In this exercise a minimal Spring application is created.

### 5.1. Create a Spring Project

Create a new project, via the New ▸ Spring Starter menu entry.

![new menu spring starter](http://www.vogella.com/tutorials/Spring/img/xnew-menu-spring-starter.png.pagespeed.ic.Ekbd0e_nXD.webp)

Use the following input for the wizard:

![new spring boot project wizard](http://www.vogella.com/tutorials/Spring/img/xnew-spring-boot-project-wizard.png.pagespeed.ic.oQblT5Ih1o.webp)

Just press **Next** and **Finish** in order to create a simple project.

The project looks like the following:

![first di spring project](http://www.vogella.com/tutorials/Spring/img/xfirst-di-spring-project.png.pagespeed.ic.RQ6oATYxJa.webp)

Add a new ITodo.java interface, which should be available as bean in the Spring DI container.

```java
package com.vogella.spring.first.di;

import java.util.Date;

public interface ITodo {

    long getId();

    String getSummary();

    void setSummary(String summary);

    boolean isDone();

    void setDone(boolean isDone);

    Date getDueDate();

    void setDueDate(Date dueDate);

    ITodo copy();

}
```

The implementation of the `ITodo` interface should look like this:

```java
package com.vogella.spring.first.di;

import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Todo implements ITodo {

    public final long id;
    private String summary;
    private Boolean done;
    private Date dueDate;

    public Todo() {
        this(-1);
    }

    public Todo(long i) {
        this(i, "");
    }

    public Todo(long i, String summary) {
        this.id = i;
        this.summary = summary;
    }

    @Override
    public long getId() {
        return id;
    }

    @Override
    public String getSummary() {
        return summary;
    }

    @Override
    public void setSummary(String summary) {
        this.summary = summary;
    }

    @Override
    public boolean isDone() {
        return done;
    }

    @Override
    public void setDone(boolean isDone) {
        this.done = isDone;
    }

    @Override
    public Date getDueDate() {
        return dueDate;
    }

    @Override
    public void setDueDate(Date dueDate) {
        this.dueDate = dueDate;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + (int) (id ^ (id >>> 32));
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Todo other = (Todo) obj;
        if (id != other.id)
            return false;
        return true;
    }

    @Override
    public String toString() {
        return "Todo [id=" + id + ", summary=" + summary + "]";
    }

    @Override
    public Todo copy() {
        Todo todo = new Todo(id, summary);
        todo.setDone(this.isDone());
        todo.setDueDate(this.getDueDate());
        return todo;
    }
}
```

There are several ways to populate an`ITodo` implementation, but the easiest one is to use a Java configuration by using the `@Configuration` and `@ComponentScan` annotation.

Create a new *com.vogella.spring.first.di.config* package in the **src/main/java source folder.

Create a Config.java class inside the new package.

```java
package com.vogella.spring.first.di.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {
}
```

This `Config` class let’s spring automatically scan the whole *com.vogella* package for classes, which should be available in Spring’s DI container.

Change the Application.java class to the following:

```java
package com.vogella.spring.first.di;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.vogella.spring.first.di.config.Config;


public class Application {

        public static void main(String[] args) {
                AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
                ITodo todo = context.getBean(ITodo.class);
                System.out.println(todo);
                context.close();
        }
}
```

### 5.2. Validate

Run the `Application` class and see that an implementation of the `ITodo` interface is found and provided by the `AnnotationConfigApplicationContext`.

Besides the console output of the Spring framework this should be printed to the console:

```
Todo [id=-1, summary=]
```

## 6. Exercise - Spring DI Bean Config

### 6.1. Target

In the previous Exercise - "Spring DI Java Config" , the Todo was created automatically by using the `@Component`annotation.

But since the Spring DI Container does not contain any objects, which are expected as constructor parameter in the `Todo` implementation the no arg constructor is always used.

### 6.2. Using a custom constructor for Components/Beans

In order to explicitly create a `Todo` instance a method in the `Config` class annotated with the `@Bean` annotation can be used.

```java
package com.vogella.spring.first.di.config;

import java.util.Date;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

import com.vogella.spring.first.di.ITodo;
import com.vogella.spring.first.di.Todo;


@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {

        @Bean
        public ITodo getTodo() {
            ITodo todo = new Todo(0, "Spring DI");
            todo.setDone(false);
            todo.setDueDate(new Date());
            return todo;
        }
}
```

### 6.3. Validate 1

Now run the `Application` class again and try to fix the error.

```java
Exception in thread "main" org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type [com.vogella.ITodo] is defined: expected single matching bean but found 2: todo,getTodo
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:366)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:332)
    at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1066)
    at com.vogella.Application.main(Application.java:12)
```

### 6.4. Fixing the NoUniqueBeanDefinitionException

Due to the `@Component` annotation on the `Todo` and the new `@Bean` method two Components/Beans are provided and Spring cannot decide, which one to choose.

This ambiguity has to be handled. Therefore either the `@Component` annotation on the `Todo` has to be removed again or the `@Primary` can be added.

```java
package com.vogella.spring.first.di.config;

import java.util.Date;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

import com.vogella.spring.first.di.ITodo;
import com.vogella.spring.first.di.Todo;


@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {

        @Bean
        @Primary
        public ITodo getTodo() {
            ITodo todo = new Todo(0, "Spring DI");
            todo.setDone(false);
            todo.setDueDate(new Date());
            return todo;
        }
}
```

### 6.5. Validate 2

Run the `Application` class and see that an implementation of the `ITodo` interface is found and provided by the `AnnotationConfigApplicationContext`.

Besides the console output of the Spring framework this should be printed to the console:

```
Todo [id=0, summary=Spring DI]
```

## 7. Exercise - Spring DI Autowire Beans

### 7.1. Target

Let the beans get their dependencies themselves. Until now only the no arg constructor and an explicitly created `Todo`where used.

But Beans themselves can also get their dependencies from Spring’s DI Container.

### 7.2. Using the @Autowired Annotation

The `Todo` 's constructor can also be annotated with the `@Autowired` annotation.

By doing this and readding the `@Component` annotation to the `Todo` Spring will try to use the constructor with parameters, which are available in the Spring DI container.

```java
package com.vogella.spring.first.di;

import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Todo implements ITodo {

    public final long id;
    private String summary;

    private Boolean done;

    private Date dueDate;

    public Todo() {
        this(-1);
    }

    public Todo(long i) {
        this(i, "");
    }

    @Autowired
    public Todo(long i, String summary) {
        this.id = i;
        this.summary = summary;
    }

    @Override
    public long getId() {
        return id;
    }

    @Override
    public String getSummary() {
        return summary;
    }

    @Override
    public void setSummary(String summary) {
        this.summary = summary;
    }

    @Override
    public boolean isDone() {
        return done;
    }

    @Autowired
    @Override
    public void setDone(boolean isDone) {
        this.done = isDone;
    }

    @Override
    public Date getDueDate() {
        return dueDate;
    }

    @Autowired
    @Override
    public void setDueDate(Date dueDate) {
        this.dueDate = dueDate;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + (int) (id ^ (id >>> 32));
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Todo other = (Todo) obj;
        if (id != other.id)
            return false;
        return true;
    }

    @Override
    public String toString() {
        return "Todo [id=" + id + ", summary=" + summary + "]";
    }

    @Override
    public Todo copy() {
        Todo todo = new Todo(id, summary);
        todo.setDone(isDone());
        todo.setDueDate(getDueDate());

        return todo;
    }
}
```

Now all constructor parameters have to be available so that `@Autowired` can work properly.

Optionally also the setter methods, e.g., dueDate and done, can be provided.

```java
package com.vogella.spring.first.di.config;

import java.util.Date;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {

    @Bean
    public Long getId() {
        return Long.valueOf(0);
    }

    @Bean
    public String getSummary() {
        return "Spring DI";
    }

    @Bean
    public Boolean isDone() {
        return Boolean.FALSE;
    }

    @Bean
    public Date getDueDate() {
        return new Date();
    }
}
```

### 7.3. Validate

With this approach the `Todo` instance will be created by Spring again, but rather with the `@Autowired` constructor than the no arg constructor.

The `@Autowired` annotation is also used for some of the setter methods.

| !    | Setter injection should always be preferred, since classes that use field injection usually cannot be tested properly. |
| ---- | ---------------------------------------- |
|      |                                          |

## 8. Exercise - Spring DI Qualifier

### 8.1. Target

In the previous exercise - "Spring DI Bean Config" an ambiguous Bean declaration has been resolved by using `@Primary`.

But `@Primary` is very limited and can only be used once for a Bean.

What if one would like to decide, which Bean of the same type should be used under certain conditions?

### 8.2. Adding a description to the ITodo

The `ITodo` interface now should also provide a description for additional information about the todo.

```java
package com.vogella.spring.first.di;

import java.util.Date;

public interface ITodo {

    long getId();

    String getSummary();

    void setSummary(String summary);

    void setDescription(String description);

    String getDescription();

    boolean isDone();

    void setDone(boolean isDone);

    Date getDueDate();

    void setDueDate(Date dueDate);

    ITodo copy();

}
```

The `Todo` implementation must implement the description getter and setter.

The description should also be autowired and therefore the setter is annotated with `@Autowired`.

In order to see the description in the sample application it is added to the overridden `toString()` method.

```java
package com.vogella.spring.first.di;

import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Todo implements ITodo {

    public final long id;

    private String summary;

    private String description;

    private Boolean done;

    private Date dueDate;

    public Todo() {
        this(-1);
    }

    public Todo(long i) {
        this(i, "");
    }

    @Autowired
    public Todo(long i, String summary) {
        this.id = i;
        this.summary = summary;
    }

    @Override
    public long getId() {
        return id;
    }

    @Override
    public String getSummary() {
        return summary;
    }

    @Override
    public void setSummary(String summary) {
        this.summary = summary;
    }

    @Override
    public String getDescription() {
        return description;
    }

    @Autowired
    @Override
    public void setDescription(String description) {
        this.description = description;
    }

    @Override
    public boolean isDone() {
        return done;
    }

    @Autowired
    @Override
    public void setDone(boolean isDone) {
        this.done = isDone;
    }

    @Override
    public Date getDueDate() {
        return dueDate;
    }

    @Autowired
    @Override
    public void setDueDate(Date dueDate) {
        this.dueDate = dueDate;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + (int) (id ^ (id >>> 32));
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Todo other = (Todo) obj;
        if (id != other.id)
            return false;
        return true;
    }

    @Override
    public String toString() {
        return "Todo [id=" + id + ", summary=" + summary + ", description=" + description + "]";
    }

    @Override
    public Todo copy() {
        Todo todo = new Todo(id, summary);
        todo.setDone(isDone());
        todo.setDueDate(getDueDate());
        todo.setDescription(getDescription());

        return todo;
    }
}
```

### 8.3. Validate 1

Run the application and see its output.

```
Todo [id=0, summary=Spring DI, description=Spring DI]
```

So the String bean has been used for both summary and description, since both methods expect a String.

### 8.4. Adding an additional Bean for the description of a Todo

To get a real description an additional Bean has to be added to the *Config.java* class.

```java
package com.vogella.spring.first.di.config;

import java.util.Date;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {

    @Bean
    public Long getId() {
        return Long.valueOf(0);
    }

    @Bean
    public String getSummary() {
        return "Spring DI";
    }

    @Bean
    public String getDescription() {
        return "Dependency in Spring is a powerful feauture for inversion of control.";
    }

    @Bean
    public Boolean isDone() {
        return Boolean.FALSE;
    }

    @Bean
    public Date getDueDate() {
        return new Date();
    }
}
```

When running the application this again would result in an `NoUniqueBeanDefinitionException`.

```
Caused by: org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type [java.lang.String] is defined: expected single matching bean but found 2: getDescription,getSummary
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1126)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1014)
    at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:813)
    at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:741)
    ... 14 more
```

### 8.5. Using the @Qualifier Annotation

The qualifier annotation can be used to qualify a Bean.

So the summary Bean can be qualified with the word "summary" and the description can be qualified with the word "description".

```java
package com.vogella.spring.first.di.config;

import java.util.Date;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = { "com.vogella.spring.first.di" })
public class Config {

    @Bean
    public Long getId() {
        return Long.valueOf(0);
    }

    @Bean
    @Qualifier("summary")
    public String getSummary() {
        return "Spring DI";
    }

    @Bean
    @Qualifier("description")
    public String getDescription() {
        return "Dependency injection in Spring is a powerful feauture for inversion of control.";
    }

    @Bean
    public Boolean isDone() {
        return Boolean.FALSE;
    }

    @Bean
    public Date getDueDate() {
        return new Date();
    }
}
```

To address a certain qualified Bean the `@Qualifier` annotation can either be used on class, parameter, method or field level.

```java
package com.vogella.spring.first.di;

import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class Todo implements ITodo {

    public final long id;

    private String summary;

    private String description;

    private Boolean done;

    private Date dueDate;

    public Todo() {
        this(-1);
    }

    public Todo(long i) {
        this(i, "");
    }

    @Autowired
    public Todo(long i, @Qualifier("summary") String summary) {
        this.id = i;
        this.summary = summary;
    }

    @Override
    public long getId() {
        return id;
    }

    @Override
    public String getSummary() {
        return summary;
    }

    @Override
    public void setSummary(String summary) {
        this.summary = summary;
    }

    @Override
    public String getDescription() {
        return description;
    }

    @Autowired
    @Qualifier("description")
    @Override
    public void setDescription(String description) {
        this.description = description;
    }

    @Override
    public boolean isDone() {
        return done;
    }

    @Autowired
    @Override
    public void setDone(boolean isDone) {
        this.done = isDone;
    }

    @Override
    public Date getDueDate() {
        return dueDate;
    }

    @Autowired
    @Override
    public void setDueDate(Date dueDate) {
        this.dueDate = dueDate;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + (int) (id ^ (id >>> 32));
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Todo other = (Todo) obj;
        if (id != other.id)
            return false;
        return true;
    }

    @Override
    public String toString() {
        return "Todo [id=" + id + ", summary=" + summary + ", description=" + description + "]";
    }

    @Override
    public Todo copy() {
        Todo todo = new Todo(id, summary);
        todo.setDone(isDone());
        todo.setDueDate(getDueDate());
        todo.setDescription(getDescription());

        return todo;
    }
}
```

In this case the summary parameter in the constructor is annotated with `@Qualifier("summary")` and the `setDescription` method is annotated with `@Qualifier("description")`.

### 8.6. Validate 2

Running the application should result in the following output.

```
Todo [id=0, summary=Spring DI, description=Dependency injection in Spring is a powerful feauture for inversion of control.]
```

## 9. Exercise - Spring Data JPA

### 9.1. Target

This exercise should show how to use Spring Data JPA with an H2 database.

### 9.2. Create a Spring Data JPA Project

Create a spring starter project, via the New ▸ Spring Starter menu entry.

![new menu spring starter](http://www.vogella.com/tutorials/Spring/img/xnew-menu-spring-starter.png.pagespeed.ic.Ekbd0e_nXD.webp)

Use the following input for the wizard:

![new spring boot project wizard jpa](http://www.vogella.com/tutorials/Spring/img/xnew-spring-boot-project-wizard-jpa.png.pagespeed.ic.TcUxLlzQu9.webp)

Now press **Next** and choose *Lombok*, *JPA* and *H2*.

![spring data jpa modules](http://www.vogella.com/tutorials/Spring/img/xspring-data-jpa-modules.png.pagespeed.ic.4k5NWXKOf1.webp)

Then press **Finish** in order to create a Spring Data JPA project.

### 9.3. Creating a JPA Entity

This time the `Todo` should be stored in a H2 database, therefore the `Todo` must make use of JPA annotations, like `@Entity`, `@Id` and `@GeneratedValue`.

In order to save time and avoid writing boilerplate code the `@Data`annotation is used to generate getters and setters for the `Todo` entity.

```java
package com.vogella.spring.jpa;

import java.util.Date;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

import lombok.Data;

@Entity
@Data
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    private String summary;

    private String description;

    private Boolean done;

    private Date dueDate;

    public Todo() {
    }

    public Todo(String summary) {
        this.summary = summary;
    }

    public void setId(long id) {
        // The id should not be set by clients, there this method is not
        // generated by lombok
    }

    public Todo copy() {
        Todo todo = new Todo(summary);
        todo.setDone(getDone());
        todo.setDueDate(getDueDate());
        todo.setDescription(getDescription());

        return todo;
    }
}
```

### 9.4. Creating a DAO / Repository

When using Spring Data JPA it is not necessary to write all the boilerplate code, which usually has to be written to provide common CRUD functionality in a DAO.

For common CRUD functionality the `CrudRepository` interface has to be extended and everything else is provided automatically then.

```java
package com.vogella.spring.jpa.repository;

import org.springframework.data.repository.CrudRepository;

import com.vogella.spring.jpa.Todo;

public interface TodoRepository extends CrudRepository<Todo, Long> {

}
```

The `CrudRepository` interface contains several commonly used DAO methods, which will be available automatically for interacting with persisted `Todo` objects.

![crud repository methods](http://www.vogella.com/tutorials/Spring/img/xcrud-repository-methods.png.pagespeed.ic.zwR1RRT09q.webp)

### 9.5. Validate

To work with the `TodoRepository` a `CommandLineRunner` can be used.

```java
package com.vogella.spring.jpa;

import java.util.Date;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import com.vogella.spring.jpa.repository.TodoRepository;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner jpaSample(TodoRepository todoRepo) {
        return (args) -> {

            // save 2 todos in the H2 database
            todoRepo.save(new Todo("Test"));

            Todo todo = new Todo("Detailed test");
            todo.setDueDate(new Date());
            todo.setDescription("Detailed description");
            todoRepo.save(todo);

            // query for all todos in the H2 database and print them
            todoRepo.findAll().forEach(System.out::println);
        };
    }
}
```

Running the application should result in the following output.

```
Todo [id=1, summary=Test, description=null]
Todo [id=2, summary=Detailed test, description=Detailed description]
```

### 9.6. Using the Spring Data JPA Query DSL

For repository interfaces it is possible to create query like method names, which are parsed by Spring.

See [Custom Spring Data JPA repository query methods](#4.2. Custom Spring Data JPA repository query methods) for further information.

#### 9.6.1. Exercises Spring Data JPA Query DSL

Please add methods for the following queries:

1. Query Todos by summary
2. Query just one optional Todo by summary and due date
3. Query a stream of Todos by summary or description
4. Count the amout of Todos in the database table
5. Count the amout of Todos with a certain summary
6. Write a custom query for all todos in the year 2016
7. Get available Todos by summary asynchronously

Please do not look at the solutions beforehand and try it on your own.

#### 9.6.2. Solutions for Spring Data JPA Query DSL exercises

##### Solution: Query Todos by summary

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    List<Todo> getBySummary(String summary);
}
```

##### Solution: Query just one optional Todo by summary and due date

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    Optional<Todo> findBySummaryAndDueDate(String summary, Date date);
}
```

##### Solution: Query a stream of Todos by summary or description

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    Stream<Todo> readBySummaryOrDescription(String summary, String description);
}
```

##### Solution: Count the amount of Todos in the database table

The `org.springframework.data.repository.CrudRepository<T, ID>` already offers a `count()` method, which can be used for this use case.

##### Solution: Count the amount of Todos with a certain summary

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    long countBySummary(String summary);
}
```

##### Solution: Write a custom query for all todos in the year 2016

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    @Query("Select t from Todo t where t.dueDate BETWEEN '2016-01-01' AND '2016-12-31'")
    List<Todo> getTodosOf2016();
}
```

##### Solution: Get available Todos by summary asynchronously

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {

    @Async
    CompletableFuture<List<Todo>> findAsyncJava8BySummary(String summary);

    @Async
    Future<List<Todo>> findAsyncBeforeJava8BySummary(String summary);
}
```

## 10. Exercise - Spring Data Rest

### 10.1. Target

This exercise should show how to use Spring Data Rest with an H2 database.

### 10.2. Create a Spring Data Rest Project

Create a spring starter project, via the New ▸ Spring Starter menu entry.

![new menu spring starter](http://www.vogella.com/tutorials/Spring/img/xnew-menu-spring-starter.png.pagespeed.ic.Ekbd0e_nXD.webp)

Use the following input for the wizard:

![new spring boot project wizard data rest](http://www.vogella.com/tutorials/Spring/img/xnew-spring-boot-project-wizard-data-rest.png.pagespeed.ic.NXx1EmI5XK.webp)

Now press **Next** and choose *Lombok*, *JPA*, *H2*, *Thymeleaf*, *Rest Repositories* and *HATEOAS*.

![spring data rest boot modules](http://www.vogella.com/tutorials/Spring/img/xspring-data-rest-boot-modules.png.pagespeed.ic.dJXX2XXtbr.webp)

Then press **Finish** in order to create a Spring Data Rest project.

### 10.3. Reusing Todo and TodoRepository

The `Todo` and `TodoRepository` can be reused from the previous [Exercise - Spring Data JPA](#9. Exercise - Spring Data JPA).

The `TodoRepository` needs to have the `@RepositoryRestResource` annotation.

```java
package com.vogella.spring.data.rest;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

@RepositoryRestResource
public interface TodoRepository extends JpaRepository<Todo, Long> {
}
```

The `@RepositoryRestResource` annotation will create restful endpoints for

```
http://localhost:8080/todoes
```

### 10.4. Validate

Add some todos to the repo and query them via rest endpoint.

Again a `CommandLineRunner` can be used for this.

But this time the data won’t be received from the `TodoRepository` directly, but by using the Spring `RestTemplate`.

```java
package com.vogella.spring.data.rest;

import java.util.Date;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner jpaSample(TodoRepository todoRepo) {
        return (args) -> {

            // save 2 todos in the H2 database
            todoRepo.save(new Todo("Test"));

            Todo todo = new Todo("Detailed test");
            Date date = new Date();
            todo.setDueDate(date);
            todo.setDescription("Detailed description");
            todoRepo.save(todo);

            RestTemplate restTemplate = new RestTemplate();
            Todo firstTodo = restTemplate.getForObject("http://localhost:8080/todoes/1", Todo.class);
            Todo secondTodo = restTemplate.getForObject("http://localhost:8080/todoes/2", Todo.class);

            System.out.println(firstTodo);
            System.out.println(secondTodo);
        };
    }
}
```

Running the application should result in the following output.

```
Todo [id=1, summary=Test, description=null]
Todo [id=2, summary=Detailed test, description=Detailed description]
```

### 10.5. Configure the rest endpoint path

Spring is able to guess a name for the rest endpoint, as it has been done with the `/todoes` endpoint.

But in some situations a different endpoint is desired, e.g., *todos* or *tasks* rather then *todoes*.

Therefore the `@RepositoryRestResource` annotation can have additional parameters.

```java
package com.vogella.spring.data.rest;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

@RepositoryRestResource(collectionResourceRel="tasks", path="tasks")
public interface TodoRepository extends JpaRepository<Todo, Long> {
}
```

And then also the `Application` class has to be slightly modified, so that the URLs are

```
http://localhost:8080/tasks/1 and http://localhost:8080/tasks/2
```

now.

```java
package com.vogella.spring.data.rest;

import java.util.Date;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner jpaSample(TodoRepository todoRepo) {
        return (args) -> {

            // save 2 todos in the H2 database
            todoRepo.save(new Todo("Test"));

            Todo todo = new Todo("Detailed test");
            Date date = new Date();
            todo.setDueDate(date);
            todo.setDescription("Detailed description");
            todoRepo.save(todo);

            RestTemplate restTemplate = new RestTemplate();
            Todo firstTodo = restTemplate.getForObject("http://localhost:8080/tasks/1", Todo.class);
            Todo secondTodo = restTemplate.getForObject("http://localhost:8080/tasks/2", Todo.class);

            System.out.println(firstTodo);
            System.out.println(secondTodo);
        };
    }
}
```

### 10.6. Adding new Todos

The provided rest API can also handle post or put requests.

The following snippet can be added at the end of the `CommandLineRunner` from the previous section.

```java
// Create a new Todo, which should be added
Todo newTodo = new Todo("New Todo");
newTodo.setDescription("Todo added by rest API");
newTodo.setDone(true);

ResponseEntity<Todo> postForEntity = restTemplate.postForEntity("http://localhost:8080/tasks/", newTodo, Todo.class);
System.out.println(postForEntity);
```

The output of this snippet should look like this:

```
<201 Created,Todo [id=0, summary=New Todo, description=Todo added by rest API],{Server=[Apache-Coyote/1.1], Location=[http://localhost:8080/tasks/3], Content-Type=[application/json;charset=UTF-8], Transfer-Encoding=[chunked], Date=[Tue, 31 May 2016 16:48:47 GMT]}>

```

| !    | The *Location* in the output tells where to find the newly added `Todo`. This can be found by either using the `RestTemplate` again or by using curl:`curl http://localhost:8080/tasks/3` |
| ---- | ---------------------------------------- |
|      |                                          |

## 11. Exercise - Creating an UI Frontend

### 11.1. Target

In this exercise a UI frontend is created. Later on different JavaScript frameworks like Rest and React will be used.

### 11.2. Change the root URI for the rest API

Since the root URI, e.g.,

```
http://localhost:8080/
```

of a web application should show an UI to the user rather than json or xml, the root URI for the rest API should be changed.

This can be done by using the `spring.data.rest.base-path` property in the *application.properties*.

```
spring.data.rest.base-path=/api
```

The *application.properties* file is usually located in *src/main/resources/application.properties*.

| !    | Now the URLs, which were used in the `CommandLineRunner` from previous exercises have to be adjusted. |
| ---- | ---------------------------------------- |
|      |                                          |

### 11.3. Creating an index.html single page app

For the UI an index.html file should be created, which contains the single page app powered by [React](https://facebook.github.io/react/).

For now the *index.html* file should look like this:

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Vogella Spring Training
    <meta charset="UTF-8"/>
</head>
<body>

    <div id="content">Hello Spring!</div>

</body>
</html>
```

The *index.html* file has to be located in *src/main/resources/templates*.

![index html location](http://www.vogella.com/tutorials/Spring/img/xindex-html-location.png.pagespeed.ic.PkxacNmnYn.webp)

### 11.4. Creating a @Controller for the UI

To let the root URI point to this *index.html* file, the mapping to the *index* has to be defined.

```java
package com.vogella.spring.data.rest;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class RootUriController {

    @RequestMapping(value = "/")
    public String index() {
        return "index";
    }
}
```

### 11.5. Validate

Start the Spring Application, open up the

```
http://localhost:8080/
```

in a browser and see whether *Hello Spring!* is shown.

## 12. Exercise - Install and use NPM

### 12.1. Target

NPM is a build tool to resolve JavaScript dependencies, which should be used in a web project.

### 12.2. Install NPM

NPM is shipped together with NodeJS, which can be downloaded here: <https://nodejs.org/en/download/>

On this the proper operating system can be chosen.

![install node](http://www.vogella.com/tutorials/Spring/img/xinstall-node.png.pagespeed.ic.Af7kanXXlw.webp)

To check the installation type `npm -v` in the command line.

![npm version check cmd line](http://www.vogella.com/tutorials/Spring/img/xnpm-version-check-cmd-line.png.pagespeed.ic.HkUoRNK277.webp)

### 12.3. Generating a package.json file

A *package.json* file is used to store the meta data of a JavaScript project. It is pretty similar to a pom.xml or build.gradle file.

Having NPM properly installed it helps creating such a *package.json* file.

Just go to the */com.vogella.spring.data.rest/src/main/resources/static* folder from previous exercises in the command line, type `npm init` and give proper input, for example what is shown below.

![npm init cmd line](http://www.vogella.com/tutorials/Spring/img/xnpm-init-cmd-line.png.pagespeed.ic.YLt5HMGKL7.webp)

### 12.4. Adding dependencies to the project

Dependencies can be added by using the `npm install {your-desired-js-package}` command, e.g., `npm install react --save`.

![npm install react](http://www.vogella.com/tutorials/Spring/img/xnpm-install-react.png.pagespeed.ic.YXo5jZFaMZ.webp)

| !    | `--save` adds the dependency to the `dependency` closure in the *package.json* file. |
| ---- | ---------------------------------------- |
|      |                                          |

| !    | `--save-dev` adds the dependency to the `devDependencies` closure in the *package.json* file, which is used for dependencies during development. |
| ---- | ---------------------------------------- |
|      |                                          |

The following packages should be added:

|      | react (use --save)                   |
| ---- | ------------------------------------ |
|      | react-dom (use --save)               |
|      | rest (use --save)                    |
|      | babel-core (use --save-dev)          |
|      | babel-loader (use --save-dev)        |
|      | babel-preset-es2015 (use --save-dev) |
|      | babel-preset-react (use --save-dev)  |
|      | webpack (use --save-dev)             |

After adding all these packages the contents of the *package.json* file should look similar to this:

```json
{
  "name": "npm-sample",
  "version": "1.0.0",
  "description": "Creating a sample package.json file",
  "main": "app.js",
  "keywords": [
    "spring",
    "npm",
    "react",
    "rest"
  ],
  "author": "Simon Scholz",
  "license": " EPL-1.0",
  "dependencies": {
    "react": "^15.1.0", 
    "react-dom": "^15.1.0", 
    "rest": "^1.3.2" 
  },
  "devDependencies": {
    "babel-core": "^6.9.1", 
    "babel-loader": "^6.2.4", 
    "babel-preset-es2015": "^6.9.0", 
    "babel-preset-react": "^6.5.0", 
    "webpack": "^1.13.1" 
  }
}
```

| !    | The *node_modules* folder, which is created besides the *package.json* file should not be checked into version control. So it’s likely added to a *.gitignore* file. |
| ---- | ---------------------------------------- |
|      |                                          |

## 13. Exercise - Configure Webpack and use Babel

### 13.1. Target

Webpack is a tool to assemble several different JavaScript packages into one build artifact, e.g., *build/bundle.js* file. Babel can be used to transpile ES6 to ES5 or even react’s jsx to JavaScript, so one can write new code, but still run it in older browsers.

### 13.2. Create a simple JavaScript file

Inside the */src/main/resources/static* directory a simple *app.js* JavaScript file should be provided.

```
document.write("Hello first JavaScript sample!");
```

This *app.js* script can be built by Webpack.

### 13.3. Configure Webpack

Webpack can be configured by a *webpack.config.js*, which basically also just exposes a configuration closure as module.

```json
module.exports = {
    entry : './app.js',
    cache : true,
    debug : true,
    output : {
        path : __dirname,
        filename : './build/bundle.js'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel',
                exclude: /node_modules/,
                query: {
                    cacheDirectory: true,
                    presets: ['es2015', 'react']
                }
            }
        ]
    }
};
```

The previously defined *app.js* script is know being transpiled by Babel if necessary and then packaged into a *bundle.js*file as output.

### 13.4. Using Gradle to run Webpack

Create a *build.gradle* file inside the */src/main/resources/static* directory of the project.

```groovy
buildscript {
    repositories {
        jcenter()
        maven {
             url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "com.moowork.gradle:gradle-node-plugin:0.12"
    }
}

apply plugin: 'com.moowork.node'

task webpack(type: NodeTask, dependsOn: 'npmInstall') {
  script = project.file('./node_modules/webpack/bin/webpack.js')
}
```

Afterwards go into the *static* directory in the command line and run the `gradle webpack` task.

This will build the a *bundle.js* script file, which has been specified as output in the *webpack.config.js* configuration.

### 13.5. Using the built bundle.js script

In order to make use of the built *bundle.js* file a `<script>` tag has to be added.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Vogella Spring Training
    <meta charset="UTF-8"/>
</head>
<body>

    <div id="content"></div>

    <script src="build/bundle.js"></script>


</body>
</html>
```

### 13.6. Validate

Start the Spring application, go to

```
http://localhost:8080
```

and see wheter the input equals *Hello first JavaScript sample!*

| !    | The big benefit here is that the *bundle.js* script can be referenced by the HTML page and under the hood the *bundle.js* script can always be regenerated by using Webpack. |
| ---- | ---------------------------------------- |
|      |                                          |

## 14. Exercise - Using rest.js

### 14.1. Target

In this exercise rest.js, which is a minimalistic rest client for JavaScript, should be used to query the rest endpoints.

### 14.2. Configure the rest.js

In rest.js several different interceptors and converter can be configured.

For this example the hal+json mime type is used. <https://github.com/vogellacompany/codeexamples-javaweb> To configure a rest.js instance a *restclient.js* script is created inside the *static* directory.

```javascript
'use strict';

var rest = require('rest');
var defaultRequest = require('rest/interceptor/defaultRequest');
var mime = require('rest/interceptor/mime');
var errorCode = require('rest/interceptor/errorCode');
var baseRegistry = require('rest/mime/registry');

var registry = baseRegistry.child();

registry.register('application/hal+json', require('rest/mime/type/application/hal'));

module.exports = rest
        .wrap(mime, { registry: registry })
        .wrap(errorCode)
        .wrap(defaultRequest, { headers: { 'Accept': 'application/hal+json' }});
```

This *restclient.js* script can then be obtained and used in the *app.js* like this:

```javascript
const client = require('./restclient'); 

client({path: '/api/tasks/1'}).then(response => { 
    console.log('response: ', response);
    document.write("response: " + response + " summary: " + response.summary);
}, error => { 
    document.write("error: " + error);
});
```

## 15. Exercise - Creating a UI frontend with react

### 15.1. Target

In this exercise a UI frontend with react is created.

### 15.2. Using rest.js react to show a list of Todos

In order to make use of react these packages are required:

- const React = require('react');
- const ReactDOM = require('react-dom');

With the `React.Component` class web components can be defined, which are rendered.

```javascript
'use strict';

const React = require('react');
const ReactDOM = require('react-dom');
const client = require('./restclient');

class App extends React.Component {

    constructor(props) {
        super(props);
        this.state = {tasks: []};
    }

    componentDidMount() {
        client({method: 'GET', path: '/api/tasks'}).done(response => {
            this.setState({tasks: response.entity._embedded.tasks});
        });
    }

    render() {
        return (
            <TaskList tasks={this.state.tasks}/>
        )
    }
}

class TaskList extends React.Component{
    render() {
        var tasks = this.props.tasks.map(task =>
            <Task key={task._links.self.href} task={task}/>
        );
        return (
            <table>
                <tr>
                    <th>Summary</th>
                    <th>Description</th>
                </tr>
                {tasks}
            </table>
        )
    }
}

class Task extends React.Component{
    render() {
        return (
            <tr>
                <td>{this.props.task.summary}</td>
                <td>{this.props.task.description}</td>
            </tr>
        )
    }
}

ReactDOM.render(
    <App />,
    document.getElementById('content')
)
```

When the *app.js* script has been changed, Webpack needs to be run again so that a new *bundle.js* file is generated.

### 15.3. Validate

Run the Spring application and see the table of Todos.

![react table](http://www.vogella.com/tutorials/Spring/img/xreact-table.png.pagespeed.ic.ExE_8Y7bx8.webp)
