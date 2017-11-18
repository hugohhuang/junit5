# 构造函数和方法的依赖注入

在之前的所有JUnit版本中，测试构造函数或方法都不允许有参数（至少不能使用标准的Runner实现）。作为JUnit Jupiter的主要变化之一，测试构造函数和方法现在都允许有参数。这带来了更大的灵活性，并为构造函数和方法启用依赖注入。

[`ParameterResolver`](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ParameterResolver.html)定义了测试扩展的API，希望在运行时动态解析参数。如果测试构造函数或`@Test`， `@TestFactory`， `@BeforeEach`， `@AfterEach`， `@BeforeAll`或`@AfterAll`方法接受参数，则该参数必须在运行时由已注册的`ParameterResolver`解析。

目前有三个自动注册的内置解析器。

* [`TestInfoParameterResolver`][TestInfoParameterResolver]

	如果方法参数是[TestInfo][]类型，则`TestInfoParameterResolver`将提供一个TestInfo的实例，对应当前测试，作为参数的值。然后TestInfo可以用来获取有关当前测试的信息，例如测试的显示名称，测试类，测试方法或关联的标签。显示名称可以是技术名称，例如测试类或测试方法的名称，也可以是通过`@DisplayName`配置的自定义名称。

	[TestInfo][]充当JUnit4的`TestName`规则的替换品。以下演示如何将`TestInfo`注入到测试构造函数，`@BeforeEach`方法和`@Test`方法中。

	```java
    import static org.junit.jupiter.api.Assertions.assertEquals;
    import static org.junit.jupiter.api.Assertions.assertTrue;

    import org.junit.jupiter.api.BeforeEach;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Tag;
    import org.junit.jupiter.api.Test;
    import org.junit.jupiter.api.TestInfo;

    @DisplayName("TestInfo Demo")
    class TestInfoDemo {

        TestInfoDemo(TestInfo testInfo) {
            assertEquals("TestInfo Demo", testInfo.getDisplayName());
        }

        @BeforeEach
        void init(TestInfo testInfo) {
            String displayName = testInfo.getDisplayName();
            assertTrue(displayName.equals("TEST 1") || displayName.equals("test2()"));
        }

        @Test
        @DisplayName("TEST 1")
        @Tag("my-tag")
        void test1(TestInfo testInfo) {
            assertEquals("TEST 1", testInfo.getDisplayName());
            assertTrue(testInfo.getTags().contains("my-tag"));
        }

        @Test
        void test2() {
        }

    }
    ```

* `RepetitionInfoParameterResolver`

	如果`@RepeatedTest`，`@BeforeEach`或`@AfterEach`方法中的方法参数类型为[RepetitionInfo][]，则`RepetitionInfoParameterResolver`将提供`RepetitionInfo`的实例。然后可以使用`RepetitionInfo`获取当前重复信息以及相应的`@RepeatedTest`的重复总数。但是请注意，`RepetitionInfoParameterResolver`不在`@RepeatedTest`的上下文之外注册。请参阅[重复测试示例](repeated-tests.md#重复测试示例)。

* [`TestReporterParameterResolver`][TestReporterParameterResolver]

	如果方法参数的类型是[TestReporter][]，`TestReporterParameterResolver`将提供一个`TestReporter`的实例。`TestReporter`可用于发布有关当前测试运行的额外数据。数据可以通过`TestExecutionListener.reportingEntryPublished()`来使用，因此可以被IDE查看或包含在报告中。

	在JUnit Jupiter中，当你需要打印信息时，就像在JUnit4使用`stdout`或`stderr`，你应该使用`TestReporter`。使用`@RunWith（JUnitPlatform.class）`甚至会将所有报告的条目输出到`stdout`。

    ```java
    import java.util.HashMap;

    import org.junit.jupiter.api.Test;
    import org.junit.jupiter.api.TestReporter;

    class TestReporterDemo {

        @Test
        void reportSingleValue(TestReporter testReporter) {
            testReporter.publishEntry("a key", "a value");
        }

        @Test
        void reportSeveralValues(TestReporter testReporter) {
            HashMap<String, String> values = new HashMap<>();
            values.put("user name", "dk38");
            values.put("award year", "1974");

            testReporter.publishEntry(values);
        }

    }
    ```

> 其他参数解析器必须通过`@ExtendWith`注册适当的扩展来显式启用。

查看[MockitoExtension][]获取自定义`ParameterResolver`的示例。虽然不打算生产就绪，它展示了扩展模型和参数解析过程的简单性和表达性。`MyMockitoTest`演示了如何将Mockito mock注入`@BeforeEach`和`@Test`方法。

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import com.example.Person;
import com.example.mockito.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class MyMockitoTest {

    @BeforeEach
    void init(@Mock Person person) {
        when(person.getName()).thenReturn("Dilbert");
    }

    @Test
    void simpleTestWithInjectedMock(@Mock Person person) {
        assertEquals("Dilbert", person.getName());
    }

}
```

[TestInfoParameterResolver]:https://github.com/junit-team/junit5/tree/r5.0.2/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestInfoParameterResolver.java
[TestInfo]:http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestInfo.html
[RepetitionInfo]:http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/RepetitionInfo.html
[TestReporterParameterResolver]:https://github.com/junit-team/junit5/tree/r5.0.2/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestReporterParameterResolver.java
[TestReporter]:http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestReporter.html
[MockitoExtension]:https://github.com/junit-team/junit5-samples/tree/r5.0.2/junit5-mockito-extension/src/main/java/com/example/mockito/MockitoExtension.java




