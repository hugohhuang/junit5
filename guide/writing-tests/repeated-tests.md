# 重复测试

JUnit Jupiter通过使用`@RepeatedTest`注解方法并指定所需的重复次数，提供了重复测试指定次数的功能。每次重复测试的调用都像执行常规的@Test方法一样，完全支持相同的生命周期回调和扩展。

以下示例演示了如何声明名为`repeatedTest()`的测试，该测试将自动重复10次。

```java
@RepeatedTest(10)
void repeatedTest() {
    // ...
}
```

除了指定重复次数外，还可以通过`@RepeatedTest`注解的`name`属性为每次重复配置自定义显示名称。此外，显示名称可以是模式，由静态文本和动态占位符的组合而成。目前支持以下占位符：

* {displayName}: @RepeatedTest方法的显示名称

* {currentRepetition}: 当前重复次数

* {totalRepetitions}: 重复的总次数

给定重复的默认显示名称基于以下模式生成：`"repetition {currentRepetition} of {totalRepetitions}"`。 因此，前面的`repeatedTest()`示例的单个重复的显示名称将是：`repetition 1 of 1`0, `repetition 2 of 10`等等。如果您希望每个重复的名称中包含`@RepeatedTest`方法的显示名称，您可以定义自己的自定义模式或使用预定义的`RepeatedTest.LONG_DISPLAY_NAME`模式。后者相当于`"{displayName} :: repetition {currentRepetition} of {totalRepetitions}"`，这会导致重复测试的显示名称变成这样：`repeatedTest() :: repetition 1 of 10`, `repeatedTest() :: repetition 2 of 10`等。

为了以编程方式获取有关当前重复和总重复次数的信息，开发人员可以选择将`RepetitionInfo`的实例注入`@RepeatedTest`，`@BeforeEach`或`@AfterEach`方法。

## 重复测试示例

本节末尾的`RepeatedTestsDemo`类将演示重复测试的几个示例。

`repeatedTest()`方法与上一节中的示例相同; 而`repeatedTestWithRepetitionInfo()`演示了如何将`RepetitionInfo`的实例注入到测试中，以获取当前重复测试的总重复次数。

接下来的两个方法演示了如何在每个重复的显示名称中包含`@RepeatedTest`方法的自定义`@DisplayName`。 `customDisplayName()`用自定义模式组合自定义显示名称，然后使用`TestInfo`来验证生成的显示名称的格式。`Repeat!`是来自`@DisplayName`，来自`{displayName}`声明，`1/1`来自`{currentRepetition}/{totalRepetitions}`。相反，`customDisplayNameWithLongPattern()`使用前面提到的预定义的`RepeatedTest.LONG_DISPLAY_NAME`模式。

`repeatedTestInGerman()`展示了将重复测试的显示名称翻译成外语的能力 - 在这种情况下是德语，从而得到单个重复的名称，例如：`Wiederholung 1 von 5`，`Wiederholung 2 von 5`等。

由于`beforeEach()`方法用`@BeforeEach`标注，所以在每次重复测试之前都会执行它。通过将`TestInfo`和`RepetitionInfo`注入到方法中，我们可以看到，有可能获得有关当前正在执行的重复测试的信息。在启用了`INFO`日志级别的情况下，执行`RepeatedTestsDemo`会得到以下输出。

```bash
INFO: About to execute repetition 1 of 10 for repeatedTest
INFO: About to execute repetition 2 of 10 for repeatedTest
INFO: About to execute repetition 3 of 10 for repeatedTest
INFO: About to execute repetition 4 of 10 for repeatedTest
INFO: About to execute repetition 5 of 10 for repeatedTest
INFO: About to execute repetition 6 of 10 for repeatedTest
INFO: About to execute repetition 7 of 10 for repeatedTest
INFO: About to execute repetition 8 of 10 for repeatedTest
INFO: About to execute repetition 9 of 10 for repeatedTest
INFO: About to execute repetition 10 of 10 for repeatedTest
INFO: About to execute repetition 1 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 2 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 3 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 4 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 5 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 1 of 1 for customDisplayName
INFO: About to execute repetition 1 of 1 for customDisplayNameWithLongPattern
INFO: About to execute repetition 1 of 5 for repeatedTestInGerman
INFO: About to execute repetition 2 of 5 for repeatedTestInGerman
INFO: About to execute repetition 3 of 5 for repeatedTestInGerman
INFO: About to execute repetition 4 of 5 for repeatedTestInGerman
INFO: About to execute repetition 5 of 5 for repeatedTestInGerman
```

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.logging.Logger;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.RepeatedTest;
import org.junit.jupiter.api.RepetitionInfo;
import org.junit.jupiter.api.TestInfo;

class RepeatedTestsDemo {

    private Logger logger = // ...

    @BeforeEach
    void beforeEach(TestInfo testInfo, RepetitionInfo repetitionInfo) {
        int currentRepetition = repetitionInfo.getCurrentRepetition();
        int totalRepetitions = repetitionInfo.getTotalRepetitions();
        String methodName = testInfo.getTestMethod().get().getName();
        logger.info(String.format("About to execute repetition %d of %d for %s", //
            currentRepetition, totalRepetitions, methodName));
    }

    @RepeatedTest(10)
    void repeatedTest() {
        // ...
    }

    @RepeatedTest(5)
    void repeatedTestWithRepetitionInfo(RepetitionInfo repetitionInfo) {
        assertEquals(5, repetitionInfo.getTotalRepetitions());
    }

    @RepeatedTest(value = 1, name = "{displayName} {currentRepetition}/{totalRepetitions}")
    @DisplayName("Repeat!")
    void customDisplayName(TestInfo testInfo) {
        assertEquals(testInfo.getDisplayName(), "Repeat! 1/1");
    }

    @RepeatedTest(value = 1, name = RepeatedTest.LONG_DISPLAY_NAME)
    @DisplayName("Details...")
    void customDisplayNameWithLongPattern(TestInfo testInfo) {
        assertEquals(testInfo.getDisplayName(), "Details... :: repetition 1 of 1");
    }

    @RepeatedTest(value = 5, name = "Wiederholung {currentRepetition} von {totalRepetitions}")
    void repeatedTestInGerman() {
        // ...
    }

}
```

在启用了unicode主题的情况下，使用`ConsoleLauncher`或`junitPlatformTest` Gradle插件时，执行`RepeatedTestsDemo`会将以下输出给控制台。

```bash
├─ RepeatedTestsDemo ✔
│  ├─ repeatedTest() ✔
│  │  ├─ repetition 1 of 10 ✔
│  │  ├─ repetition 2 of 10 ✔
│  │  ├─ repetition 3 of 10 ✔
│  │  ├─ repetition 4 of 10 ✔
│  │  ├─ repetition 5 of 10 ✔
│  │  ├─ repetition 6 of 10 ✔
│  │  ├─ repetition 7 of 10 ✔
│  │  ├─ repetition 8 of 10 ✔
│  │  ├─ repetition 9 of 10 ✔
│  │  └─ repetition 10 of 10 ✔
│  ├─ repeatedTestWithRepetitionInfo(RepetitionInfo) ✔
│  │  ├─ repetition 1 of 5 ✔
│  │  ├─ repetition 2 of 5 ✔
│  │  ├─ repetition 3 of 5 ✔
│  │  ├─ repetition 4 of 5 ✔
│  │  └─ repetition 5 of 5 ✔
│  ├─ Repeat! ✔
│  │  └─ Repeat! 1/1 ✔
│  ├─ Details... ✔
│  │  └─ Details... :: repetition 1 of 1 ✔
│  └─ repeatedTestInGerman() ✔
│     ├─ Wiederholung 1 von 5 ✔
│     ├─ Wiederholung 2 von 5 ✔
│     ├─ Wiederholung 3 von 5 ✔
│     ├─ Wiederholung 4 von 5 ✔
│     └─ Wiederholung 5 von 5 ✔
```
