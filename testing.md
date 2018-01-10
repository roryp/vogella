Table of Contents

- [1. The purpose of software tests](http://www.vogella.com/tutorials/JUnit/article.html#testintroduction)
- [2. Testing terminology](http://www.vogella.com/tutorials/JUnit/article.html#testingterminology)
- [3. Using JUnit](http://www.vogella.com/tutorials/JUnit/article.html#junittesting)
- [4. Using JUnit 4](http://www.vogella.com/tutorials/JUnit/article.html#usingjuni4)
- [5. Eclipse support for JUnit 4](http://www.vogella.com/tutorials/JUnit/article.html#eclipse-support-for-junit-4)
- [6. Installation of JUnit](http://www.vogella.com/tutorials/JUnit/article.html#installation-of-junit)
- [7. Setting Eclipse up for using JUnits static imports](http://www.vogella.com/tutorials/JUnit/article.html#setting-eclipse-up-for-using-junits-static-imports)
- [8. Exercise: Using JUnit](http://www.vogella.com/tutorials/JUnit/article.html#exercise-using-junit)
- [9. Mocking](http://www.vogella.com/tutorials/JUnit/article.html#mockingframeworks)
- [10. Overview of JUnit 5](http://www.vogella.com/tutorials/JUnit/article.html#junit5)
- [11. Comparison of annotations between JUnit 4 and 5](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_annotations)
- [12. About this website](http://www.vogella.com/tutorials/JUnit/article.html#about-this-website)
- [13. JUnit Resources](http://www.vogella.com/tutorials/JUnit/article.html#junit_links)
- [Appendix A: Copyright and License](http://www.vogella.com/tutorials/JUnit/article.html#copyright-and-license)

> This tutorial explains unit testing with JUnit 4.x and JUnit5. It explains the creation of JUnit tests. It also covers the usage of the Eclipse IDE for developing software tests.

## [1. The purpose of software tests](http://www.vogella.com/tutorials/JUnit/article.html#testintroduction)

### [1.1. What are software tests?](http://www.vogella.com/tutorials/JUnit/article.html#whataresoftwaretests)

A software test is a piece of software, which executes another piece of software. It validates if that code results in the expected state (state testing) or executes the expected sequence of events (behavior testing).

### [1.2. Why are software tests helpful?](http://www.vogella.com/tutorials/JUnit/article.html#whytesting)

Software unit tests help the developer to verify that the logic of a piece of the program is correct.

Running tests automatically helps to identify software regressions introduced by changes in the source code. Having a high test coverage of your code allows you to continue developing features without having to perform lots of manual tests.

## [2. Testing terminology](http://www.vogella.com/tutorials/JUnit/article.html#testingterminology)

### [2.1. Code (or application) under test](http://www.vogella.com/tutorials/JUnit/article.html#code-or-application-under-test)

The code which is tested is typically called the *code under test*. If you are testing an application, this is called the *application under test*.

### [2.2. Test fixture](http://www.vogella.com/tutorials/JUnit/article.html#junit_testfixture)

A *test fixture* is a fixed state in code which is tested used as input for a test. Another way to describe this is a test precondition.

For example, a test fixture might be a a fixed string, which is used as input for a method. The test would validate if the method behaves correctly with this input.

### [2.3. Unit tests and unit testing](http://www.vogella.com/tutorials/JUnit/article.html#junit_intro)

A *unit test* is a piece of code written by a developer that executes a specific functionality in the code to be tested and asserts a certain behavior or state.

The percentage of code which is tested by unit tests is typically called *test coverage*.

A unit test targets a small unit of code, e.g., a method or a class. External dependencies should be removed from unit tests, e.g., by replacing the dependency with a test implementation or a (mock) object created by a test framework.

Unit tests are not suitable for testing complex user interface or component interaction. For this, you should develop integration tests.

### [2.4. Integration tests](http://www.vogella.com/tutorials/JUnit/article.html#junit_organization)

An *integration test* aims to test the behavior of a component or the integration between a set of components. The term *functional test* is sometimes used as synonym for integration test. Integration tests check that the whole system works as intended, therefore they are reducing the need for intensive manual tests.

These kinds of tests allow you to translate your user stories into a test suite. The test would resemble an expected user interaction with the application.

### [2.5. Performance tests](http://www.vogella.com/tutorials/JUnit/article.html#junit_performancetests)

Performance tests are used to benchmark software components repeatedly. Their purpose is to ensure that the code under test runs fast enough even if it’s under high load.

### [2.6. Behavior vs. state testing](http://www.vogella.com/tutorials/JUnit/article.html#behaviorvsstatetesting)

A test is a behavior test (also called interaction test) if it checks if certain methods were called with the correct input parameters. A behavior test does not validate the result of a method call.

State testing is about validating the result. Behavior testing is about testing the behavior of the application under test.

If you are testing algorithms or system functionality, in most cases you may want to test state and not interactions. A typical test setup uses mocks or stubs of related classes to abstract the interactions with these other classes away Afterwards you test the state or the behavior depending on your need.

### [2.7. Testing frameworks for Java](http://www.vogella.com/tutorials/JUnit/article.html#testing-frameworks-for-java)

There are several testing frameworks available for Java. The most popular ones are JUnit and TestNG

This description focuses on JUnit. It covers both JUnit 4.x and JUnit 5.

### [2.8. Where should the test be located?](http://www.vogella.com/tutorials/JUnit/article.html#junit_testorganization)

Typical, unit tests are created in a separate project or separate source folder to keep the test code separate from the real code. The standard convention from the Maven and Gradle build tools is to use:

- src/main/java - for Java classes
- src/test/java - for test classes

### [2.9. Which part of the software should be tested?](http://www.vogella.com/tutorials/JUnit/article.html#junit_whattotest)

What should be tested is a highly controversial topic. Some developers believe every statement in your code should be tested.

In any case you should write software tests for the critical and complex parts of your application. If you introduce new features a solid test suite also protects you against regression in existing code.

In general it it safe to ignore trivial code. For example, it is typical useless to write tests for getter and setter methods which simply assign values to fields. Writing tests for these statements is time consuming and pointless, as you would be testing the Java virtual machine. The JVM itself already has test cases for this. If you are developing end user applications you are safe to assume that a field assignment works in Java.

If you start developing tests for an existing code base without any tests, it is good practice to start writing tests for code in which most of the errors happened in the past. This way you can focus on the critical parts of your application.

## [3. Using JUnit](http://www.vogella.com/tutorials/JUnit/article.html#junittesting)

### [3.1. The JUnit framework](http://www.vogella.com/tutorials/JUnit/article.html#unittesting_junit)

[JUnit](http://junit.org/) is a test framework which uses annotations to identify methods that specify a test. JUnit is an open source project hosted at [Github](https://github.com/junit-team/junit).

### [3.2. How to define a test in JUnit?](http://www.vogella.com/tutorials/JUnit/article.html#unittesting_junit_test)

A JUnit *test* is a method contained in a class which is only used for testing. This is called a *Test class*. To define that a certain method is a test method, annotate it with the `@Test` annotation.

This method executes the code under test. You use an *assert* method, provided by JUnit or another assert framework, to check an expected result versus the actual result. These method calls are typically called *asserts* or *assert statements*.

You should provide meaningful messages in assert statements. That makes it easier for the user to identify and fix the problem. This is especially true if someone looks at the problem, who did not write the code under test or the test code.

### [3.3. Example JUnit test](http://www.vogella.com/tutorials/JUnit/article.html#unittesting_junitexample)

The following code shows a JUnit test using the JUnit 5 version. This test assumes that the `MyClass` class exists and has a `multiply(int, int)` method.

```
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

public class MyTests {

    @Test
    public void multiplicationOfZeroIntegersShouldReturnZero() {
        MyClass tester = new MyClass(); // MyClass is tested

        // assert statements
        assertEquals(0, tester.multiply(10, 0), "10 x 0 must be 0");
        assertEquals(0, tester.multiply(0, 10), "0 x 10 must be 0");
        assertEquals(0, tester.multiply(0, 0), "0 x 0 must be 0");
    }
}
```

### [3.4. JUnit naming conventions](http://www.vogella.com/tutorials/JUnit/article.html#junit_namingconventions)

There are several potential naming conventions for JUnit tests. A widely-used solution for classes is to use the "Test" suffix at the end of test classes names.

As a general rule, a test name should explain what the test does. If that is done correctly, reading the actual implementation can be avoided.

One possible convention is to use the "should" in the test method name. For example, "ordersShouldBeCreated" or "menuShouldGetActive". This gives a hint what should happen if the test method is executed.

Another approach is to use "Given[ExplainYourInput]When[WhatIsDone]Then[ExpectedResult]" for the display name of the test method.

### [3.5. JUnit naming conventions for Maven](http://www.vogella.com/tutorials/JUnit/article.html#junit_namingconventions_maven)

If you are using the Maven build system, you should use the "Test" suffix for test classes. The Maven build system (via its surfire plug-in) automatically includes such classes in its test scope.

### [3.6. Run your test from the command line](http://www.vogella.com/tutorials/JUnit/article.html#juniteclipse_code)

You can also run your JUnit tests outside our IDE via standard Java code. Build systems like Apache Maven or Gradle in combination with a Continuous Integration Server (like Jenkins) can be used to execute tests automatically on a regular basis.

The `org.junit.runner.JUnitCore` class provides the `runClasses()` method. This method allows you to run one or several tests classes. As a return parameter you receive an object of the type `org.junit.runner.Result`. This object can be used to retrieve information about the tests.

The following class demonstrates how to run the `MyClassTest`. This class executes your test class and write potential failures to the console.

```
package de.vogella.junit.first;

import org.junit.runner.JUnitCore;
import org.junit.runner.Result;
import org.junit.runner.notification.Failure;

public class MyTestRunner {
  public static void main(String[] args) {
    Result result = JUnitCore.runClasses(MyClassTest.class);
    for (Failure failure : result.getFailures()) {
      System.out.println(failure.toString());
    }
  }
}
```

This class can be executed like any other Java program on the command line. You only need to add the JUnit library JAR file to the classpath.

### [3.7. Test execution order](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_executionorder)

JUnit assumes that all test methods can be executed in an arbitrary order. Well-written test code should not assume any order, i.e., tests should not depend on other tests.

As of JUnit 4.11 the default is to use a deterministic, but not predictable, order for the execution of the tests.

You can use an annotation to define that the test methods are sorted by method name, in lexicographic order. To activate this feature, annotate your test class with the `@FixMethodOrder(MethodSorters.NAME_ASCENDING)` annotation. You can also explicitely set the default by using the `MethodSorters.DEFAULT` parameter in this annotation. You can also use `MethodSorters.JVM` which uses the JVM defaults, which may vary from run to run.

## [4. Using JUnit 4](http://www.vogella.com/tutorials/JUnit/article.html#usingjuni4)

### [4.1. Defining test methods](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_annotations)

JUnit uses annotations to mark methods as test methods and to configure them. The following table gives an overview of the most important annotations in JUnit for the 4.x and 5.x versions. All these annotations can be used on methods.

| JUnit 4                                | Description                              |
| -------------------------------------- | ---------------------------------------- |
| `import org.junit.*`                   | Import statement for using the following annotations. |
| `@Test`                                | Identifies a method as a test method.    |
| `@Before`                              | Executed before each test. It is used to prepare the test environment (e.g., read input data, initialize the class). |
| `@After`                               | Executed after each test. It is used to cleanup the test environment (e.g., delete temporary data, restore defaults). It can also save memory by cleaning up expensive memory structures. |
| `@BeforeClass`                         | Executed once, before the start of all tests. It is used to perform time intensive activities, for example, to connect to a database. Methods marked with this annotation need to be defined as `static` to work with JUnit. |
| `@AfterClass`                          | Executed once, after all tests have been finished. It is used to perform clean-up activities, for example, to disconnect from a database. Methods annotated with this annotation need to be defined as `static` to work with JUnit. |
| `@Ignore` or `@Ignore("Why disabled")` | Marks that the test should be disabled. This is useful when the underlying code has been changed and the test case has not yet been adapted. Or if the execution time of this test is too long to be included. It is best practice to provide the optional description, why the test is disabled. |
| `@Test (expected = Exception.class)`   | Fails if the method does not throw the named exception. |
| `@Test(timeout=100)`                   | Fails if the method takes longer than 100 milliseconds. |

### [4.2. Assert statements](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_asserts)

JUnit provides static methods to test for certain conditions via the `Assert` class. These *assert statements* typically start with `assert`. They allow you to specify the error message, the expected and the actual result. An *assertion method*compares the actual value returned by a test to the expected value. It throws an `AssertionException` if the comparison fails.

The following table gives an overview of these methods. Parameters in [] brackets are optional and of type String.

| Statement                                | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| fail([message])                          | Let the method fail. Might be used to check that a certain part of the code is not reached or to have a failing test before the test code is implemented. The message parameter is optional. |
| assertTrue([message,] boolean condition) | Checks that the boolean condition is true. |
| assertFalse([message,] boolean condition) | Checks that the boolean condition is false. |
| assertEquals([message,] expected, actual) | Tests that two values are the same. Note: for arrays the reference is checked not the content of the arrays. |
| assertEquals([message,] expected, actual, tolerance) | Test that float or double values match. The tolerance is the number of decimals which must be the same. |
| assertNull([message,] object)            | Checks that the object is null.          |
| assertNotNull([message,] object)         | Checks that the object is not null.      |
| assertSame([message,] expected, actual)  | Checks that both variables refer to the same object. |
| assertNotSame([message,] expected, actual) | Checks that both variables refer to different objects. |

### [4.3. JUnit test suites](http://www.vogella.com/tutorials/JUnit/article.html#juniteclipse_testsuite)

If you have several test classes, you can combine them into a test suite. Running a test suite executes all test classes in that suite in the specified order. A test suite can also contain other test suites.

The following example code demonstrates the usage of a test suite. It contains two test classes (MyClassTest and MySecondClassTest). If you want to add another test class, you can add it to the `@Suite.SuiteClasses` statement.

```
package com.vogella.junit.first;

import org.junit.runner.RunWith;
import org.junit.runners.Suite;
import org.junit.runners.Suite.SuiteClasses;

@RunWith(Suite.class)
@SuiteClasses({
        MyClassTest.class,
        MySecondClassTest.class })

public class AllTests {

}
```

### [4.4. Disabling tests](http://www.vogella.com/tutorials/JUnit/article.html#disabling-tests)

The @Ignore annotation allow to statically ignore a test. Alternatively you can use `Assume.assumeFalse` or `Assume.assumeTrue` to define a condition for the test. `Assume.assumeFalse` marks the test as invalid, if its condition evaluates to true. `Assume.assumeTrue` evaluates the test as invalid if its condition evaluates to false. For example, the following disables a test on Linux:

```
Assume.assumeFalse(System.getProperty("os.name").contains("Linux"));
```

### [4.5. Parameterized test](http://www.vogella.com/tutorials/JUnit/article.html#junitadvanced)

JUnit allows you to use parameters in a tests class. This class can contain **one** test method and this method is executed with the different parameters provided.

You mark a test class as a parameterized test with the `@RunWith(Parameterized.class)` annotation.

Such a test class must contain a static method annotated with the `@Parameters` annotation. That method generates and returns a collection of arrays. Each item in this collection is used as parameter for the test method.

You can use the `@Parameter` annotation on public fields to get the test values injected in the test.

The following code shows an example for a parameterized test. It tests the `multiply()` method of the `MyClass` class which is included as inner class for the purpose of this example.

```
package testing;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;

import java.util.Arrays;
import java.util.Collection;

import static org.junit.Assert.assertEquals;
import static org.junit.runners.Parameterized.*;

@RunWith(Parameterized.class)
public class ParameterizedTestFields {

    // fields used together with @Parameter must be public
    @Parameter(0)
    public int m1;
    @Parameter(1)
    public int m2;
    @Parameter(2)
    public int result;


    // creates the test data
    @Parameters
    public static Collection<Object[]> data() {
        Object[][] data = new Object[][] { { 1 , 2, 2 }, { 5, 3, 15 }, { 121, 4, 484 } };
        return Arrays.asList(data);
    }


    @Test
    public void testMultiplyException() {
        MyClass tester = new MyClass();
        assertEquals("Result", result, tester.multiply(m1, m2));
    }


    // class to be tested
    class MyClass {
        public int multiply(int i, int j) {
            return i *j;
        }
    }

}
```

Alternatively to using the `@Parameter` annotation you can use a constructor in which you store the values for each test. The number of elements in each array provided by the method annotated with `@Parameters` must correspond to the number of parameters in the constructor of the class. The class is created for each parameter and the test values are passed via the constructor to the class.

```
package de.vogella.junit.first;

import static org.junit.Assert.assertEquals;

import java.util.Arrays;
import java.util.Collection;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;

@RunWith(Parameterized.class)
public class ParameterizedTestUsingConstructor {

    private int m1;
    private int m2;

    public ParameterizedTestUsingConstructor(int p1, int p2) {
        m1 = p1;
        m2 = p2;
    }

    // creates the test data
    @Parameters
    public static Collection<Object[]> data() {
        Object[][] data = new Object[][] { { 1 , 2 }, { 5, 3 }, { 121, 4 } };
        return Arrays.asList(data);
    }


    @Test
    public void testMultiplyException() {
        MyClass tester = new MyClass();
        assertEquals("Result", m1 * m2, tester.multiply(m1, m2));
    }


    // class to be tested
    class MyClass {
        public int multiply(int i, int j) {
            return i *j;
        }
    }

}
```

If you run this test class, the test method is executed with each defined parameter. In the above example the test method is executed three times.

A more flexible and easier to write approach is provided by the JUnitParams from <https://github.com/Pragmatists/JUnitParams>.

### [4.6. JUnit Rules](http://www.vogella.com/tutorials/JUnit/article.html#junitadvanced_rules)

Via JUnit rules you can add behavior to each tests in a test class. You can annotate fields of type `TestRule` with the `@Rule` annotation. You can create objects which can be used and configured in your test methods. This adds more flexibility to your tests. You could, for example, specify which exception message you expect during the execution of your test code.

```
package de.vogella.junit.first;

import org.junit.Rule;
import org.junit.Test;
import org.junit.rules.ExpectedException;

public class RuleExceptionTesterExample {

  @Rule
  public ExpectedException exception = ExpectedException.none();

  @Test
  public void throwsIllegalArgumentExceptionIfIconIsNull() {
    exception.expect(IllegalArgumentException.class);
    exception.expectMessage("Negative value not allowed");
    ClassToBeTested t = new ClassToBeTested();
    t.methodToBeTest(-1);
  }
}
```

JUnit already provides several useful rule implementations. For example, the `TemporaryFolder` class allows to setup files and folders which are automatically removed after each test run.

The following code shows an example for the usage of the `TemporaryFolder` implementation.

```
package de.vogella.junit.first;

import static org.junit.Assert.assertTrue;

import java.io.File;
import java.io.IOException;

import org.junit.Rule;
import org.junit.Test;
import org.junit.rules.TemporaryFolder;

public class RuleTester {

  @Rule
  public TemporaryFolder folder = new TemporaryFolder();

  @Test
  public void testUsingTempFolder() throws IOException {
    File createdFolder = folder.newFolder("newfolder");
    File createdFile = folder.newFile("myfilefile.txt");
    assertTrue(createdFile.exists());
  }
}
```

For more examples of existing rules see <https://github.com/junit-team/junit4/wiki/Rules>.

### [4.7. Writing custom JUnit rules](http://www.vogella.com/tutorials/JUnit/article.html#writing-custom-junit-rules)

To write your custom rule, you need to implement the `TestRule` interface. This interface defines the `apply(Statement, Description)` method which must return an instance of `Statement`. Statement represent the tests within the JUnit runtime and Statement#evaluate() run these. Description describes the individual test. It allows to read information about the test via reflection.

The following is a simple example for adding a log statement to an Android application before and after test execution.

```
package testing.android.vogella.com.asynctask;


import android.util.Log;

import org.junit.rules.TestRule;
import org.junit.runner.Description;
import org.junit.runners.model.Statement;

public class MyCustomRule implements TestRule {
    private Statement base;
    private Description description;

    @Override
    public Statement apply(Statement base, Description description) {
        this.base = base;
        this.description = description;
        return new MyStatement(base);
    }

    public class MyStatement extends Statement {
        private final Statement base;

        public MyStatement(Statement base) {
            this.base = base;
        }

        @Override
        public void evaluate() throws Throwable {
            System.
            Log.w("MyCustomRule",description.getMethodName() + "Started" );
            try {
                base.evaluate();
            } finally {
                Log.w("MyCustomRule",description.getMethodName() + "Finished");
            }
        }
    }
}
```

To use this rule, simple add a field annotated with `@Rule` to your test class.

```
@Rule
public MyCustomRule myRule = new MyCustomRule();
```

### [4.8. Categories](http://www.vogella.com/tutorials/JUnit/article.html#junitadvanced_categories)

It is possible to define categories of tests and include or exclude them based on annotations. The following example is based on the [JUnit 4.8 release notes](https://github.com/junit-team/junit/blob/master/doc/ReleaseNotes4.8.md).

```
public interface FastTests { /* category marker */
}

public interface SlowTests { /* category marker */
}

public class A {
    @Test
    public void a() {
        fail();
    }

    @Category(SlowTests.class)
    @Test
    public void b() {
    }
}

@Category({ SlowTests.class, FastTests.class })
public class B {
    @Test
    public void c() {
    }
}

@RunWith(Categories.class)
@IncludeCategory(SlowTests.class)
@SuiteClasses({ A.class, B.class })
// Note that Categories is a kind of Suite
public class SlowTestSuite {
    // Will run A.b and B.c, but not A.a
}

@RunWith(Categories.class)
@IncludeCategory(SlowTests.class)
@ExcludeCategory(FastTests.class)
@SuiteClasses({ A.class, B.class })
// Note that Categories is a kind of Suite
public class SlowTestSuite {
    // Will run A.b, but not A.a or B.c
}
```

## [5. Eclipse support for JUnit 4](http://www.vogella.com/tutorials/JUnit/article.html#eclipse-support-for-junit-4)

### [5.1. Creating JUnit tests](http://www.vogella.com/tutorials/JUnit/article.html#eclipse_creatingjunittest)

You can write the JUnit tests manually, but Eclipse supports the creation of JUnit tests via wizards.

For example, to create a JUnit test or a test class for an existing class. Right-click on your new class, select this class in the Package Explorer_ view, right-click on it and select New ▸ JUnit Test Case.

Alternatively you can also use the JUnit wizards available under File ▸ New ▸ Other… ▸ Java ▸ JUnit.

### [5.2. Running JUnit tests](http://www.vogella.com/tutorials/JUnit/article.html#running-junit-tests)

The Eclipse IDE also provides support for executing your tests interactively.

To run a test, select the test class, right-click on it and select Run-as ▸ JUnit Test.

This starts JUnit and executes all test methods in this class.

Eclipse provides the Alt+Shift+X, T shortcut to run the test in the selected class.

To run only the selected test, position the cursor on the test method name and use the shortcut.

To see the result of a JUnit test, Eclipse uses the *JUnit* view which shows the results of the tests. You can also select individual unit tests in this view, right-click on them and select *Run* to execute them again.

![JUnit view](http://www.vogella.com/tutorials/JUnit/img/xjunitview10.png.pagespeed.ic.vLB_Q1w0yd.webp)

By default, this view shows all tests. You can also configure, that it only shows failing tests.

![JUnit view](http://www.vogella.com/tutorials/JUnit/img/xjunit_showonerroronly.png.pagespeed.ic.muI0DehN5J.webp)

You can also define that the view is only activated if you have a failing test.

![JUnit view](http://www.vogella.com/tutorials/JUnit/img/xjunit_showonerroronly2.png.pagespeed.ic.syElgy8Ym5.webp)

NOTE:Eclipse creates run configurations for tests. You can see and modify these via the Run ▸ Run Configurations…menu.

### [5.3. Extracting the failed test and stacktraces](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_failedtests)

To get the list of failed test,	right click on the test result and select *Copy Failure List*. This copies the failed tests and there stack traces into the clipboard.

![Copy failed tests into clipboard](http://www.vogella.com/tutorials/JUnit/img/xjunitcopyfailurelist10.png.pagespeed.ic.-Cw8Y_uqHL.webp)

### [5.4. JUnit static imports](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_staticimports)

Static import is a feature that allows fields and methods defined in a class as `public static` to be used without specifying the class in which the field is defined.

JUnit assert statements are typically defined as `public static` to allow the developer to write short test statements. The following snippet demonstrates an assert statement with and without static imports.

```
// without static imports you have to write the following statement
Assert.assertEquals("10 x 5 must be 50", 50, tester.multiply(10, 5));


// alternatively define assertEquals as static import
import static org.junit.Assert.assertEquals;

// more code

// use assertEquals directly because of the static import
assertEquals("10 x 5 must be 50", 50, tester.multiply(10, 5));
```

### [5.5. Wizard for creating test suites](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_testsuites)

You can create a test suite via Eclipse. For this, select the test classes which should be included in suite in the *Package Explorer* view, right-click on them and select New ▸ Other… ▸ JUnit ▸ JUnit Test Suite.

![Create a test suite](http://www.vogella.com/tutorials/JUnit/img/xjunittestsuite10.png.pagespeed.ic.hSuAliHYw2.webp)

### [5.6. Testing exception](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_execption)

The `@Test (expected = Exception.class)` annotation is limited as it can only test for one exception. To testexceptions, you can use the following testpattern.

```
try {
   mustThrowException();
   fail();
} catch (Exception e) {
   // expected
   // could also check for message of exception, etc.
}
```

### [5.7. JUnit Plug-in Test](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_plugintest)

JUnit Plug-in tests are used to write unit tests for your plug-ins. These tests are executed by a special test runner that launches another Eclipse instance in a separate VM. The test methods are executed within that instance.

## [6. Installation of JUnit](http://www.vogella.com/tutorials/JUnit/article.html#installation-of-junit)

### [6.1. Using JUnit with Gradle](http://www.vogella.com/tutorials/JUnit/article.html#using-junit-with-gradle)

To use JUnit in your Gradle build, add a testCompile dependency to your build file.

```
apply plugin: 'java'

dependencies {
  testCompile 'junit:junit:4.12'
}
```

### [6.2. Using JUnit with Maven](http://www.vogella.com/tutorials/JUnit/article.html#using-junit-with-maven)

To use JUnit in your Maven build, add the following dependency to your pom file.

```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

### [6.3. Using JUnit without Maven or Gradle](http://www.vogella.com/tutorials/JUnit/article.html#using-junit-without-maven-or-gradle)

The Eclipse IDE ships with a version of JUnit.

It is also possible to download the JUnit library explicitly from the [JUnit](http://junit.org/) website. Add this library to your classpath, to use it.

## [7. Setting Eclipse up for using JUnits static imports](http://www.vogella.com/tutorials/JUnit/article.html#setting-eclipse-up-for-using-junits-static-imports)

The Eclipse IDE cannot always create the corresponding `static import` statements automatically.

You can configure the Eclipse IDE to use code completion to insert typical JUnit method calls and to add the static import automatically. For this open the Preferences via Window ▸ Preferences and select Java ▸ Editor ▸ Content Assist ▸ Favorites.

Use the **New Type** button to add the following entries to it:

- `org.junit.Assert`
- `org.hamcrest.CoreMatchers`
- `org.hamcrest.Matchers`

This makes, for example, the `assertTrue`, `assertFalse` and `assertEquals` methods directly available in the *Content Assists*.

![Adding static imports to the preferences](http://www.vogella.com/tutorials/JUnit/img/xstaticimport10.png.pagespeed.ic.Rv9IRD2fb_.webp)

You can now use *Content Assists* (shortcut: Ctrl+Space) to add the method and the import.

## [8. Exercise: Using JUnit](http://www.vogella.com/tutorials/JUnit/article.html#exercise-using-junit)

### [8.1. Project preparation](http://www.vogella.com/tutorials/JUnit/article.html#project-preparation)

Create a new project called *com.vogella.junit.first*. Create a new source folder *test*. For this right-click on your project, select *Properties* and choose Java ▸ Build Path. Select the *Source* tab.

![Create new source folder for the tests](http://www.vogella.com/tutorials/JUnit/img/xjunit10.png.pagespeed.ic.YAQdLxdoRT.webp)

Press the **Add Folder** button. Afterwards, press the **Create New Folder** button. Enter *test* as folder name.

The result is depicted in the following screenshot.

![Creating a new folder](http://www.vogella.com/tutorials/JUnit/img/xjunit20.png.pagespeed.ic.8dcvpjQze4.webp)

NOTE:You can also add a new source folder by right-clicking on a project and selecting New ▸ Source Folder.

### [8.2. Create a Java class](http://www.vogella.com/tutorials/JUnit/article.html#create-a-java-class)

In the *src* folder, create the `com.vogella.junit.first` package and the following class.

```
package com.vogella.junit.first;

public class MyClass {
  public int multiply(int x, int y) {
    // the following is just an example
    if (x > 999) {
      throw new IllegalArgumentException("X should be less than 1000");
    }
    return x / y;
  }
}
```

### [8.3. Create a JUnit test](http://www.vogella.com/tutorials/JUnit/article.html#create-a-junit-test)

Right-click on your new class in the *Package Explorer* view and select New ▸ JUnit Test Case.

In the following wizard ensure that the *New JUnit 4 test* flag is selected and set the source folder to *test*, so that your test class gets created in this folder.

![Create new test class](http://www.vogella.com/tutorials/JUnit/img/xjunit30.png.pagespeed.ic.sA9cIvCzHl.webp)

Press the **Next** button and select the methods that you want to test.

![Selecting the methods to test](http://www.vogella.com/tutorials/JUnit/img/xjunit40.png.pagespeed.ic.U0dxy0131Z.webp)

If the JUnit library is not part of the classpath of your project, Eclipse will prompt you to add it. Use this to add JUnit to your project.

![Eclipse prompt for adding JUnit to the project classpath](http://www.vogella.com/tutorials/JUnit/img/xjunit50.png.pagespeed.ic.Wo2DbHZS6l.webp)

Create a test with the following code.

```
package com.vogella.junit.first;

import static org.junit.Assert.assertEquals;

import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;

public class MyClassTest {

  @Test(expected = IllegalArgumentException.class)
  public void testExceptionIsThrown() {
    MyClass tester = new MyClass();
    tester.multiply(1000, 5);
  }

  @Test
  public void testMultiply() {
    MyClass tester = new MyClass();
    assertEquals("10 x 5 must be 50", 50, tester.multiply(10, 5));
  }
}
```

### [8.4. Run your test in Eclipse](http://www.vogella.com/tutorials/JUnit/article.html#run-your-test-in-eclipse)

Right-click on your new test class and select Run-As ▸ JUnit Test.

![Run JUnit test in Eclipse](http://www.vogella.com/tutorials/JUnit/img/xjunit60.png.pagespeed.ic.bZxWEO8Ko7.webp)

The result of the tests are displayed in the JUnit view. In our example one test should be successful and one test should show an error. This error is indicated by a red bar.

![Result of running a unit test](http://www.vogella.com/tutorials/JUnit/img/xjunit70.png.pagespeed.ic.rFUslg5C6o.webp)

The test is failing, because our multiplier class is currently not working correctly. It does a division instead of multiplication. Fix the bug and re-run the test to get a green bar.

## [9. Mocking](http://www.vogella.com/tutorials/JUnit/article.html#mockingframeworks)

Unit testing also makes use of object mocking. In this case the real object is exchanged by a replacement which has a predefined behavior for the test.

There are several frameworks available for mocking. To learn more about mock frameworks please see the [Mockito tutorial](http://www.vogella.com/tutorials/Mockito/article.html).

## [10. Overview of JUnit 5](http://www.vogella.com/tutorials/JUnit/article.html#junit5)

JUnit 5 is the latest major release of JUnit.

JUnit 5 consists of a number of discrete components:

- JUnit Platform - foundation layer which enables different testing frameworks to be launched on the JVM
- Junit Jupiter - is the JUnit 5 test framework which is launched by JUnit Platform
- JUnit Vintage - legacy TestEngine which runs older tests

### [10.1. Usage of JUnit 5 with Gradle](http://www.vogella.com/tutorials/JUnit/article.html#usage-of-junit-5-with-gradle)

```
buildscript {
    repositories {
        mavenCentral()
        // The following is only necessary if you want to use SNAPSHOT releases.
        // maven { url 'https://oss.sonatype.org/content/repositories/snapshots' }
    }
    dependencies {
        classpath 'org.junit.platform:junit-platform-gradle-plugin:1.0.0'
    }
}

repositories {
    mavenCentral()
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.junit.platform.gradle.plugin'

dependencies {
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.0.0")
    testRuntime("org.junit.jupiter:junit-jupiter-engine:5.0.0")
    // to run JUnit 3/4 tests:
    testImplementation("junit:junit:4.12")
    testRuntime("org.junit.vintage:junit-vintage-engine:4.12.0")
}
```

You can find the official gradle.build example here: <https://github.com/junit-team/junit5-samples/blob/master/junit5-gradle-consumer/build.gradle>

After letting gradle set up your project can then execute your JUnit 5 tests through the terminal:

```
gradle junitPlatformTest
```

If you are using Eclipse it is best to install the [Buildship tooling](http://www.vogella.com/tutorials/EclipseGradle/article.html#install-eclipse-gradle-buildship-tooling). Then you can start your tests via Run as ▸ Gradle Test. The result of the test execution will be displayed in the `Console` view.

![Menu entry ](http://www.vogella.com/tutorials/JUnit/img/xrun_as_gradle_test.png.pagespeed.ic.1tg-tsrAuS.webp)

### [10.2. Usage of JUnit 5 with Maven](http://www.vogella.com/tutorials/JUnit/article.html#usage-of-junit-5-with-maven)

This example shows how to import all components of JUnit 5 into your project.

We need to register the individual components with Maven surefire:

```
<build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>${java.version}</source>
                <target>${java.version}</target>
            </configuration>
        </plugin>
        <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.19.1</version>
            <configuration>
                <includes>
                    <include>**/Test*.java</include>
                    <include>**/*Test.java</include>
                    <include>**/*Tests.java</include>
                    <include>**/*TestCase.java</include>
                </includes>
                <properties>
                    <!-- <includeTags>fast</includeTags> -->
                    <excludeTags>slow</excludeTags>
                </properties>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>org.junit.platform</groupId>
                    <artifactId>junit-platform-surefire-provider</artifactId>
                    <version>${junit.platform.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.junit.jupiter</groupId>
                    <artifactId>junit-jupiter-engine</artifactId>
                    <version>${junit.jupiter.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                    <version>${junit.vintage.version}</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

And add the dependencies:

```
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>${junit.jupiter.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>${junit.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

You can find a complete example of a working maven configuration here: <https://github.com/junit-team/junit5-samples/blob/r5.0.0-M4/junit5-maven-consumer/pom.xml>

| **   | The above works for Java projects but not yet for Android projects. |
| ---- | ---------------------------------------- |
|      |                                          |

### [10.3. Defining test methods](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_annotations)

JUnit uses annotations to mark methods as test methods and to configure them. The following table gives an overview of the most important annotations in JUnit for the 4.x and 5.x versions. All these annotations can be used on methods.

| `import org.junit.jupiter.api.*`         | Import statement for using the following annotations. |
| ---------------------------------------- | ---------------------------------------- |
| `@Test`                                  | Identifies a method as a test method.    |
| `@RepeatedTest(<Number>)`                | Repeats the test a <Number> of times     |
| `@TestFactory`                           | Method is a Factory for dynamic tests    |
| `@BeforeEach`                            | Executed before each test. It is used to prepare the test environment (e.g., read input data, initialize the class). |
| `@AfterEach`                             | Executed after each test. It is used to cleanup the test environment (e.g., delete temporary data, restore defaults). It can also save memory by cleaning up expensive memory structures. |
| `@BeforeAll`                             | Executed once, before the start of all tests. It is used to perform time intensive activities, for example, to connect to a database. Methods marked with this annotation need to be defined as `static` to work with JUnit. |
| `@AfterAll`                              | Executed once, after all tests have been finished. It is used to perform clean-up activities, for example, to disconnect from a database. Methods annotated with this annotation need to be defined as `static` to work with JUnit. |
| `@Nested`                                | Lets you nest inner test classes to force a certain execution order |
| `@Tag("<TagName>")`                      | Tests in JUnit 5 can be filtered by tag. Eg., run only tests tagged with "fast". |
| `@ExtendWith`                            | Lets you register an Extension class that integrates with one or more extension points |
| `@Disabled` or `@Disabled("Why disabled")` | Marks that the test should be disabled. This is useful when the underlying code has been changed and the test case has not yet been adapted. Or if the execution time of this test is too long to be included. It is best practice to provide the optional description, why the test is disabled. |
| `@DisplayName("<Name>")`                 | <Name> that will be displayed by the test runner. In contrast to method names the DisplayName can contain spaces. |

### [10.4. Disabling tests](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_executionorder)

The `@Disabled` annotation allows to statically ignore a test.

Alternatively you can use `Assumptions.assumeFalse` or `Assumptions.assumeTrue` to define a condition for test deactivation. `Assumptions.assumeFalse` marks the test as invalid, if its condition evaluates to true.`Assumptions.assumeTrue` evaluates the test as invalid if its condition evaluates to false. For example, the following disables a test on Linux:

```
Assumptions.assumeFalse(System.getProperty("os.name").contains("Linux"));
```

### [10.5. Test Suites](http://www.vogella.com/tutorials/JUnit/article.html#test-suites)

To run multiple tests together, you can use test suites. They allow to aggregate multiple test classes. JUnit 5 provides two annotations:

- `@SelectPackages` - used to specify the names of packages for the test suite
- `@SelectClasses` - used to specify the classes for the test suite. They can be located in different packages.

```
@RunWith(JUnitPlatform.class)
@SelectPackages("com.vogella.junit5.examples")
public class AllTests {}
```

```
@RunWith(JUnitPlatform.class)
@SelectClasses({AssertionTest.class, AssumptionTest.class, ExceptionTest.class})
public class AllTests {}
```

### [10.6. Expecting Exceptions](http://www.vogella.com/tutorials/JUnit/article.html#expecting-exceptions)

Exception is handling with `org.junit.jupiter.api.Assertions.expectThrows()`. You define the expected Exception class and provide code that should throw the exception.

```
import static org.junit.jupiter.api.Assertions.expectThrows;

@Test
void exceptionTesting() {
    // set up user
    Throwable exception = expectThrows(IllegalArgumentException.class, () -> user.setAge("23"));
    assertEquals("Age must be an Integer.", exception.getMessage());
}
```

This lets you define which part of the test should throw the exception. The test will still fail if an exception is thrown outside of this scope.

### [10.7. Grouped assertions](http://www.vogella.com/tutorials/JUnit/article.html#grouped-assertions)

```
@Test
void groupedAssertions() {
    Address address = new Address();
    // In a grouped assertion all assertions are executed, even after a failure.
    // The error messages get grouped together.
    assertAll("address name",
        () -> assertEquals("John", address.getFirstName()),
        () -> assertEquals("User", address.getLastName())
    );
}
    => org.opentest4j.MultipleFailuresError: address name (2 failures)
    expected: <John> but was: <null>
    expected: <User> but was: <null>
```

### [10.8. Timeout tests](http://www.vogella.com/tutorials/JUnit/article.html#timeout-tests)

If you want to ensure that a test fails if it isn’t done in a certain amount of time you can use the `assertTimeout()`method. This method will wait until

```
import static org.junit.jupiter.api.Assertions.assertTimeout;
import static java.time.Duration.ofSeconds;
import static java.time.Duration.ofMinutes;

@Test
void timeoutNotExceeded() {
    assertTimeout(ofMinutes(1), () -> service.doBackup());
}

// if you have to check a return value
@Test
void timeoutNotExceededWithResult() {
    String actualResult = assertTimeout(ofSeconds(1), () -> {
        return restService.request(request);
    });
    assertEquals(200, request.getStatus());
}
=> org.opentest4j.AssertionFailedError: execution exceeded timeout of 1000 ms by 212 ms
```

If you want your tests to cancel after the timeout period is passed you can use the `assertTimeoutPreemptively()`method.

```
@Test
void timeoutNotExceededWithResult() {
    String actualResult = assertTimeoutPreemptively(ofSeconds(1), () -> {
        return restService.request(request);
    });
    assertEquals(200, request.getStatus());
}
=> org.opentest4j.AssertionFailedError: execution timed out after 1000 ms
```

### [10.9. Running the same test repeatedly on a data set](http://www.vogella.com/tutorials/JUnit/article.html#running-the-same-test-repeatedly-on-a-data-set)

Sometimes we want to be able to run the same test on a data set. Holding the data set in a Collection and iterating over it with the assertion in the loop body has the problem that the first assertion failure will stop the test execution. JUnit 5 offers multiple ways to overcome this limitation.

#### [10.9.1. Using Dynamic Tests](http://www.vogella.com/tutorials/JUnit/article.html#dynamic_tests)

JUnit 5 offers the possibility to define dynamic tests. We can use this to rewrite our example. Dynamic test methods are annotated with `@TestFactory` and can return an Iterable, a Collection or a Stream of `DynamicTest`s. JUnit then runs every DynamicTest when the test is executed. `@BeforeEach` and `@AfterEach` methods will not be called for dynamic tests. This means that you can’t use them to reset the test object if you change it’s state in the lambda expression for a dynamic test.

In this example we return a Stream:

```
import static org.junit.jupiter.api.Assertions.*;
import static org.junit.jupiter.api.DynamicTest.dynamicTest;

import java.util.Arrays;
import java.util.stream.Stream;

import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.TestFactory;

public class DynamicTestCreationTest {

    @TestFactory
    public Stream<DynamicTest> testMultiplyException() {
        MyClass tester = new MyClass();
        int[][] data = new int[][] { { 1, 2, 2 }, { 5, 3, 15 }, { 121, 4, 484 } };
        return Arrays.stream(data).map(entry -> {
            int m1 = entry[0];
            int m2 = entry[1];
            int expected = entry[2];
            return dynamicTest(m1 + " * " + m2 + " = " + expected, () -> {
                assertEquals(expected, tester.multiply(m1, m2));
            });
        });
    }

    // class to be tested
    class MyClass {
        public int multiply(int i, int j) {
            return i * j;
        }
    }
}
```

#### [10.9.2. Using Parameterized Tests](http://www.vogella.com/tutorials/JUnit/article.html#using-parameterized-tests)

Junit5 also supports parameterized tests. To use them you have to add the `junit-jupiter-params` package as a test dependencies. If you are using gradle:

```
dependencies {
  // ..
  testCompile group: 'org.junit.jupiter', name: 'junit-jupiter-params', version: '5.0.0'
}
```

For this example we use the `@MethodSource` annotation. We give it the name of the function(s) we want it to call to get it’s test data. The function has to be static and must return either a Collection, an Iterator, a Stream or an Array. On execution the test method gets called once for every entry in the data source. In contrast to [Dynamic Tests](http://www.vogella.com/tutorials/JUnit/article.html#dynamic_tests) `@BeforeEach`and `@AfterEach` methods will be called for parameterized tests.

```
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;

public class DynamicTestCreationTest {

    public static int[][] data() {
        return new int[][] { { 1 , 2, 2 }, { 5, 3, 15 }, { 121, 4, 484 } };
    }

    @ParameterizedTest
    @MethodSource(names = "data")
    void testWithStringParameter(int[] data) {
        MyClass tester = new MyClass();
        int m1 = data[0];
        int m2 = data[1];
        int expected = data[2];
        assertEquals(expected, tester.multiply(m1, m2));
    }

    // class to be tested
    class MyClass {
        public int multiply(int i, int j) {
            return i * j;
        }
    }
}
```

##### [Data sources](http://www.vogella.com/tutorials/JUnit/article.html#data-sources)

The following table gives an overview of all possible test data sources for parameterized tests.

| Annotation                               | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| `@ValueSource(ints = { 1, 2, 3 })`       | Lets you define an array of test values. Permissible types are `String`, `int`, `long`, or `double`. |
| `@EnumSource(value = Months.class, names = {"JANUARY", "FEBRUARY"})` | Lets you pass Enum constants as test class. With the optional attribute `names`you can choose which constants should be used. Otherwise all attributes are used. |
| `@MethodSource(names = "genTestData")`   | The result of the named method is passed as argument to the test. |
| `@CsvSource({ "foo, 1", "'baz, qux', 3" })void testMethod(String first, int second) {` | Expects strings to be parsed as Csv. The delimiter is `','`. |
| `@ArgumentsSource(MyArgumentsProvider.class)` | Specifies a class that provides the test data. The referenced class has to implement the `ArgumentsProvider`interface. |

##### [Argument conversion](http://www.vogella.com/tutorials/JUnit/article.html#argument-conversion)

JUnit tries to automatically convert the source strings to match the expected arguments of the test method.

If you need explicit conversion you can specify a converter with the `@ConvertWith` annotation. To define your own converter you have to implement the `ArgumentConverter` interface. In the following example we use the abstract `SimpleArgumentConverter` base class.

```
@ParameterizedTest
@ValueSource(ints = {1, 12, 42})
void testWithExplicitArgumentConversion(@ConvertWith(ToOctalStringArgumentConverter.class) String argument) {
    System.err.println(argument);
    assertNotNull(argument);
}

static class ToOctalStringArgumentConverter extends SimpleArgumentConverter {
    @Override
    protected Object convert(Object source, Class<?> targetType) {
        assertEquals(Integer.class, source.getClass(), "Can only convert from Integers.");
        assertEquals(String.class, targetType, "Can only convert to String");
        return Integer.toOctalString((Integer) source);
    }
}
```

## [11. Comparison of annotations between JUnit 4 and 5](http://www.vogella.com/tutorials/JUnit/article.html#usingjunit_annotations)

| JUnit 5                                  | JUnit 4                                | Description                              |
| ---------------------------------------- | -------------------------------------- | ---------------------------------------- |
| `import org.junit.jupiter.api.*`         | `import org.junit.*`                   | Import statement for using the following annotations. |
| `@Test`                                  | `@Test`                                | Identifies a method as a test method.    |
| `@BeforeEach`                            | `@Before`                              | Executed before each test. It is used to prepare the test environment (e.g., read input data, initialize the class). |
| `@AfterEach`                             | `@After`                               | Executed after each test. It is used to cleanup the test environment (e.g., delete temporary data, restore defaults). It can also save memory by cleaning up expensive memory structures. |
| `@BeforeAll`                             | `@BeforeClass`                         | Executed once, before the start of all tests. It is used to perform time intensive activities, for example, to connect to a database. Methods marked with this annotation need to be defined as `static` to work with JUnit. |
| `@AfterAll`                              | `@AfterClass`                          | Executed once, after all tests have been finished. It is used to perform clean-up activities, for example, to disconnect from a database. Methods annotated with this annotation need to be defined as `static` to work with JUnit. |
| `@Disabled` or `@Disabled("Why disabled")` | `@Ignore` or `@Ignore("Why disabled")` | Marks that the test should be disabled. This is useful when the underlying code has been changed and the test case has not yet been adapted. Or if the execution time of this test is too long to be included. It is best practice to provide the optional description, why the test is disabled. |
| Not available, is replaced by `org.junit.jupiter.api.Assertions.expectThrows()` | `@Test (expected = Exception.class)`   | Fails if the method does not throw the named exception. |
| Not available, is replaced by `AssertTimeout.assertTimeout()`and `AssertTimeout.assertTimeoutPreemptively()` | `@Test(timeout=100)`                   | Fails if the method takes longer than 100 milliseconds. |

## [12. About this website](http://www.vogella.com/tutorials/JUnit/article.html#about-this-website)

[Support free content![Support free tutorials](http://www.vogella.com/img/common/donate.svg)](http://www.vogella.com/support.html)

[Questions and discussion![Questions and discussion](http://www.vogella.com/img/common/discussions.svg)](http://www.vogella.com/contact.html)

[Tutorial & code license![License](http://www.vogella.com/img/common/license_new.svg)](http://www.vogella.com/license.html)

[Get the source code![Source Code](http://www.vogella.com/img/common/sourcecode.svg)](http://www.vogella.com/code/index.html)

## [13. JUnit Resources](http://www.vogella.com/tutorials/JUnit/article.html#junit_links)

[JUnit Homepage](http://www.junit.org/)

[JUnit 5 user guide](http://junit.org/junit5/docs/current/user-guide/)

[TestNG, alternative testing framework to JUnit](http://testng.org/)

[TestNG, alternative testing framework to JUnit](http://testng.org/)

[Eclipse IDE book from Lars Vogel](http://www.vogella.com/books/eclipseide.html)

### [13.1. vogella GmbH training and consulting support](http://www.vogella.com/tutorials/JUnit/article.html#vogella-gmbh-training-and-consulting-support)

| [TRAINING](http://www.vogella.com/training/) | [SERVICE & SUPPORT](http://www.vogella.com/consulting/) |
| ---------------------------------------- | ---------------------------------------- |
| The vogella company provides comprehensive [training and education services](http://www.vogella.com/training/) from experts in the areas of Eclipse RCP, Android, Git, Java, Gradle and Spring. We offer both public and inhouse training. Whichever course you decide to take, you are guaranteed to experience what many before you refer to as [“The best IT class I have ever attended”](http://www.vogella.com/training/). | The vogella company offers [expert consulting](http://www.vogella.com/consulting/) services, development support and coaching. Our customers range from Fortune 100 corporations to individual developers. |

## [Appendix A: Copyright and License](http://www.vogella.com/tutorials/JUnit/article.html#copyright-and-license)

Copyright © 2012-2017 vogella GmbH. Free use of the software examples is granted under the terms of the EPL License. This tutorial is published under the [Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Germany](http://creativecommons.org/licenses/by-nc-sa/3.0/de/deed.en) license.

See [Licence](http://www.vogella.com/license.html).