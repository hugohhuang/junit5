# 动态测试

[注解](annotations.md)中描述的JUnit Jupiter中的标准`@Test`注解与JUnit 4中的`@Test`注解非常相似。两者都描述了实现测试用例的方法。这些测试用例是静态的，因为它们是在编译时完全指定的，而且它们的行为不能由运行时发生的任何事情来改变。_`Assumption`提供了一种基本的动态行为形式，但是刻意在表达方面受到限制_。

除了这些标准测试外，JUnit Jupiter还引入了一种全新的测试编程模型。这种新的测试是`动态测试`，它是由`@TestFactory`注解的工厂方法在运行时生成的。

与`@Test`方法相比，`@TestFactory`方法本身不是测试用例，而是测试用例的工厂。因此，动态测试是工厂的产物。从技术上讲，`@TestFactory`方法必须返回`DynamicNode`实例的`Stream`，`Collection`，`Iterable`或`Iterator`。 `DynamicNode`的可实例化的子类是`DynamicContainer`和`DynamicTest`。 `DynamicContainer`实例由一个显示名称和一个动态子节点列表组成，可以创建任意嵌套的动态节点层次结构。然后，`DynamicTest`实例将被延迟执行，从而实现测试用例的动态甚至非确定性生成。

任何由`@TestFactory`返回的`Stream`都要通过调用`stream.close()`来正确关闭，使得使用诸如`Files.lines()`之类的资源变得安全。

与`@Test`方法一样，`@TestFactory`方法不能是`private`或`static`，并且可以选择声明参数，以便通过`ParameterResolvers`解析。

`DynamicTest`是运行时生成的测试用例。它由显示名称和`Executable`组成。 `Executable`是`@FunctionalInterface`，这意味着动态测试的实现可以作为_lambda表达式_或_方法引用_来提供。

> 动态生命周期
>
> 动态测试的执行生命周期与标准的`@Test`情况完全不同。具体而言，个别动态测试没有生命周期回调。这意味着`@BeforeEach`和`@AfterEach`方法及其相应的扩展回调函数是为`@TestFactory`方法执行，而不是对每个动态测试执行。换句话说，如果您从一个lambda表达式的测试实例中访问动态测试的字段，这些字段将不会由同一个`@TestFactory`方法生成的各个动态测试之间的回调方法或扩展重置。

从JUnit Jupiter 5.0.2开始，动态测试必须始终由工厂方法创建; 不过，在稍后的发行版中，这可以通过注册设施来补充。

> 动态测试目前是实验性功能。有关详细信息，请参阅[实验性API](../api-evolution/experimental-apis.md)中的表格。

## 动态测试示例

下面的`DynamicTestsDemo`类演示了测试工厂和动态测试的几个示例。

第一种方法返回无效的返回类型。由于在编译时无法检测到无效的返回类型，因此在运行时检测并抛出`JUnitException`异常。

接下来的五个方法是非常简单的例子，演示了`Collection`，`Iterable`，`Iterator`或者`DynamicTest`实例的生成。这些例子中的大多数并不真正表现出动态行为，而只是在原则上展示了支持的返回类型。而`dynamicTestsFromStream()`和`dynamicTestsFromIntStream()`演示了如何为给定的一组字符串或一组输入数字生成动态测试是何等的简单。

下一个方法本质上是真正动态的。 `generateRandomNumberOfTests()`实现了一个生成随机数的`Iterator`，一个显示名称生成器和一个测试执行器，然后将这三者全部提供给`DynamicTest.stream()`。尽管`generateRandomNumberOfTests()`的非确定性行为理所当然的会与测试的可重复性相冲突，应谨慎使用，它可以演示动态测试的表现力和力量。

最后一个方法使用`DynamicContainer`生成动态测试的嵌套层次结构。

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.DynamicContainer.dynamicContainer;
import static org.junit.jupiter.api.DynamicTest.dynamicTest;

import java.util.Arrays;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.Random;
import java.util.function.Function;
import java.util.stream.IntStream;
import java.util.stream.Stream;

import org.junit.jupiter.api.DynamicNode;
import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.TestFactory;
import org.junit.jupiter.api.function.ThrowingConsumer;

class DynamicTestsDemo {

    // This will result in a JUnitException!
    @TestFactory
    List<String> dynamicTestsWithInvalidReturnType() {
        return Arrays.asList("Hello");
    }

    @TestFactory
    Collection<DynamicTest> dynamicTestsFromCollection() {
        return Arrays.asList(
            dynamicTest("1st dynamic test", () -> assertTrue(true)),
            dynamicTest("2nd dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterable<DynamicTest> dynamicTestsFromIterable() {
        return Arrays.asList(
            dynamicTest("3rd dynamic test", () -> assertTrue(true)),
            dynamicTest("4th dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterator<DynamicTest> dynamicTestsFromIterator() {
        return Arrays.asList(
            dynamicTest("5th dynamic test", () -> assertTrue(true)),
            dynamicTest("6th dynamic test", () -> assertEquals(4, 2 * 2))
        ).iterator();
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromStream() {
        return Stream.of("A", "B", "C")
            .map(str -> dynamicTest("test" + str, () -> { /* ... */ }));
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromIntStream() {
        // Generates tests for the first 10 even integers.
        return IntStream.iterate(0, n -> n + 2).limit(10)
            .mapToObj(n -> dynamicTest("test" + n, () -> assertTrue(n % 2 == 0)));
    }

    @TestFactory
    Stream<DynamicTest> generateRandomNumberOfTests() {

        // Generates random positive integers between 0 and 100 until
        // a number evenly divisible by 7 is encountered.
        Iterator<Integer> inputGenerator = new Iterator<Integer>() {

            Random random = new Random();
            int current;

            @Override
            public boolean hasNext() {
                current = random.nextInt(100);
                return current % 7 != 0;
            }

            @Override
            public Integer next() {
                return current;
            }
        };

        // Generates display names like: input:5, input:37, input:85, etc.
        Function<Integer, String> displayNameGenerator = (input) -> "input:" + input;

        // Executes tests based on the current input value.
        ThrowingConsumer<Integer> testExecutor = (input) -> assertTrue(input % 7 != 0);

        // Returns a stream of dynamic tests.
        return DynamicTest.stream(inputGenerator, displayNameGenerator, testExecutor);
    }

    @TestFactory
    Stream<DynamicNode> dynamicTestsWithContainers() {
        return Stream.of("A", "B", "C")
            .map(input -> dynamicContainer("Container " + input, Stream.of(
                dynamicTest("not null", () -> assertNotNull(input)),
                dynamicContainer("properties", Stream.of(
                    dynamicTest("length > 0", () -> assertTrue(input.length() > 0)),
                    dynamicTest("not empty", () -> assertFalse(input.isEmpty()))
                ))
            )));
    }

}
```
