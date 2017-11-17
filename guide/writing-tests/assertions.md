# 断言

JUnit Jupiter提供了许多JUnit4已有的断言方法，并增加了一些适合与Java 8 lambda一起使用的断言方法。所有JUnit Jupiter断言都是[org.junit.jupiter.Assertions][Assertions]类中的静态方法。

```java
import static java.time.Duration.ofMillis;
import static java.time.Duration.ofMinutes;
import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTimeout;
import static org.junit.jupiter.api.Assertions.assertTimeoutPreemptively;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.Test;

class AssertionsDemo {

    @Test
    void standardAssertions() {
        assertEquals(2, 2);
        assertEquals(4, 4, "The optional assertion message is now the last parameter.");
        assertTrue(2 == 2, () -> "Assertion messages can be lazily evaluated -- "
                + "to avoid constructing complex messages unnecessarily.");
    }

    @Test
    void groupedAssertions() {
        // In a grouped assertion all assertions are executed, and any
        // failures will be reported together.
        assertAll("person",
            () -> assertEquals("John", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName())
        );
    }

    @Test
    void dependentAssertions() {
        // Within a code block, if an assertion fails the
        // subsequent code in the same block will be skipped.
        assertAll("properties",
            () -> {
                String firstName = person.getFirstName();
                assertNotNull(firstName);

                // Executed only if the previous assertion is valid.
                assertAll("first name",
                    () -> assertTrue(firstName.startsWith("J")),
                    () -> assertTrue(firstName.endsWith("n"))
                );
            },
            () -> {
                // Grouped assertion, so processed independently
                // of results of first name assertions.
                String lastName = person.getLastName();
                assertNotNull(lastName);

                // Executed only if the previous assertion is valid.
                assertAll("last name",
                    () -> assertTrue(lastName.startsWith("D")),
                    () -> assertTrue(lastName.endsWith("e"))
                );
            }
        );
    }

    @Test
    void exceptionTesting() {
        Throwable exception = assertThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    }

    @Test
    void timeoutNotExceeded() {
        // The following assertion succeeds.
        assertTimeout(ofMinutes(2), () -> {
            // Perform task that takes less than 2 minutes.
        });
    }

    @Test
    void timeoutNotExceededWithResult() {
        // The following assertion succeeds, and returns the supplied object.
        String actualResult = assertTimeout(ofMinutes(2), () -> {
            return "a result";
        });
        assertEquals("a result", actualResult);
    }

    @Test
    void timeoutNotExceededWithMethod() {
        // The following assertion invokes a method reference and returns an object.
        String actualGreeting = assertTimeout(ofMinutes(2), AssertionsDemo::greeting);
        assertEquals("hello world!", actualGreeting);
    }

    @Test
    void timeoutExceeded() {
        // The following assertion fails with an error message similar to:
        // execution exceeded timeout of 10 ms by 91 ms
        assertTimeout(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            Thread.sleep(100);
        });
    }

    @Test
    void timeoutExceededWithPreemptiveTermination() {
        // The following assertion fails with an error message similar to:
        // execution timed out after 10 ms
        assertTimeoutPreemptively(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            Thread.sleep(100);
        });
    }

    private static String greeting() {
        return "hello world!";
    }

}
```

## 第三方断言类库

虽然JUnit Jupiter提供的断言功能足以满足许多测试场景的需要，但是有时需要更强大和附加功能，例如匹配器。在这种情况下，JUnit小组推荐使用[AssertJ][]，[Hamcrest][]，[Truth][]等第三方断言库。开发人员可以自由使用他们选择的断言库。

[Assertions]:http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assertions.html
[AssertJ]:http://joel-costigliola.github.io/assertj/
[Hamcrest]:http://hamcrest.org/JavaHamcrest/
[Truth]:http://google.github.io/truth/
[assertThat]:http://junit.org/junit4/javadoc/latest/org/junit/Assert.html#assertThat

例如，匹配器和fluent API的组合可以用来使断言更具描述性和可读性。但是，JUnit Jupiter的[org.junit.jupiter.Assertions][Assertions]类没有提供类似于JUnit 4的`org.junit.Assert`类的[`assertThat()`][assertThat]方法，以接受Hamcrest Matcher。相反，鼓励开发人员使用由第三方断言库提供的匹配器的内置支持。

以下示例演示如何在JUnit Jupiter测试中使用来自Hamcrest的`assertThat()`支持。 只要Hamcrest库已经添加到classpath中，就可以静态地导入诸如`assertThat()`，`is()`和`equalTo()`之类的方法，然后像下面的`assertWithHamcrestMatcher()`方法那样在测试中使用它们。

```java
import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;

import org.junit.jupiter.api.Test;

class HamcrestAssertionDemo {

    @Test
    void assertWithHamcrestMatcher() {
        assertThat(2 + 1, is(equalTo(3)));
    }

}
```

当然，基于JUnit 4编程模型的遗留测试可以继续使用`org.junit.Assert＃assertThat`。
