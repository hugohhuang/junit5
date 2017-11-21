# 参数化测试

参数化测试可以用不同的参数多次运行测试。它们和普通的`@Test`方法一样声明，但是使用`@ParameterizedTest`注解。另外，您必须声明至少一个将为每次调用提供参数的_来源(source)_。

> 参数化测试目前是实验性功能。有关详细信息，请参阅[实验性API](../api-evolution/experimental-apis.md)中的表格。

```java
@ParameterizedTest
@ValueSource(strings = { "racecar", "radar", "able was I ere I saw elba" })
void palindromes(String candidate) {
    assertTrue(isPalindrome(candidate));
}
```

这个参数化的测试使用`@ValueSource`注解来指定一个`String`数组作为参数的来源。执行上述方法时，每次调用将分别报告。例如，`ConsoleLauncher`将打印输出类似于以下内容。

```bash
palindromes(String) ✔
├─ [1] racecar ✔
├─ [2] radar ✔
└─ [3] able was I ere I saw elba ✔
```

## 必须的设置

为了使用参数化测试，您需要添加对`junit-jupiter-params`构建的依赖。有关详细信息，请参阅[依赖元数据](../installation/dependency-metadata.md)。

## 参数来源

JUnit Jupiter开箱即用，提供了不少_source_注解。下面的每个小节都为他们提供了简要的概述和示例。请参阅[`org.junit.jupiter.params.provider`](http://junit.org/junit5/docs/current/api/org/junit/jupiter/params/provider/package-summary.html)包中的JavaDoc以获取更多信息。

### @ValueSource

`@ValueSource`是最简单的source之一。它可以让你指定一个原生类型（String，int，long或double）的数组，并且只能为每次调用提供一个参数。

```java
@ParameterizedTest
@ValueSource(ints = { 1, 2, 3 })
void testWithValueSource(int argument) {
    assertNotNull(argument);
}
```

### @EnumSource

`@EnumSource`提供了一个使用`Enum`常量的简便方法。该注释提供了一个可选的`name`参数，可以指定使用哪些常量。如果省略，所有的常量将被用在下面的例子中。

```java
@ParameterizedTest
@EnumSource(TimeUnit.class)
void testWithEnumSource(TimeUnit timeUnit) {
    assertNotNull(timeUnit);
}
```

```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, names = { "DAYS", "HOURS" })
void testWithEnumSourceInclude(TimeUnit timeUnit) {
    assertTrue(EnumSet.of(TimeUnit.DAYS, TimeUnit.HOURS).contains(timeUnit));
}
```

`@EnumSource`注解还提供了一个可选的`mode`参数，可以对将哪些常量传递给测试方法进行细化控制。例如，您可以从枚举常量池中排除名称或指定正则表达式，如下例所示。

```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, mode = EXCLUDE, names = { "DAYS", "HOURS" })
void testWithEnumSourceExclude(TimeUnit timeUnit) {
    assertFalse(EnumSet.of(TimeUnit.DAYS, TimeUnit.HOURS).contains(timeUnit));
    assertTrue(timeUnit.name().length() > 5);
}
```


```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, mode = MATCH_ALL, names = "^(M|N).+SECONDS$")
void testWithEnumSourceRegex(TimeUnit timeUnit) {
    String name = timeUnit.name();
    assertTrue(name.startsWith("M") || name.startsWith("N"));
    assertTrue(name.endsWith("SECONDS"));
}
```

## @MethodSource

`@MethodSource`允许你引用一个或多个测试类的工厂方法。这样的方法必须返回一个`Stream`，`Iterable`，`Iterator`或者参数数组。另外，这种方法不能接受任何参数。默认情况下，除非测试类用`@TestInstance(Lifecycle.PER_CLASS)`注解，否则这些方法必须是静态的。

如果只需要一个参数，则可以返回参数类型的实例`Stream`，如以下示例所示。

```java
@ParameterizedTest
@MethodSource("stringProvider")
void testWithSimpleMethodSource(String argument) {
    assertNotNull(argument);
}

static Stream<String> stringProvider() {
    return Stream.of("foo", "bar");
}
```

支持原始类型（`DoubleStream`，`IntStream`和`LongStream`）的流，示例如下：

```java
@ParameterizedTest
@MethodSource("range")
void testWithRangeMethodSource(int argument) {
    assertNotEquals(9, argument);
}

static IntStream range() {
    return IntStream.range(0, 20).skip(10);
}
```

如果测试方法声明多个参数，则需要返回一个集合或`Arguments`实例流，如下所示。请注意，`Arguments.of(Object…)`是`Arguments`接口中定义的静态工厂方法。

```java
@ParameterizedTest
@MethodSource("stringIntAndListProvider")
void testWithMultiArgMethodSource(String str, int num, List<String> list) {
    assertEquals(3, str.length());
    assertTrue(num >=1 && num <=2);
    assertEquals(2, list.size());
}

static Stream<Arguments> stringIntAndListProvider() {
    return Stream.of(
        Arguments.of("foo", 1, Arrays.asList("a", "b")),
        Arguments.of("bar", 2, Arrays.asList("x", "y"))
    );
}
```

### @CsvSource

`@CsvSource`允许您将参数列表表示为以逗号分隔的值（例如，字符串文字）。

```java
@ParameterizedTest
@CsvSource({ "foo, 1", "bar, 2", "'baz, qux', 3" })
void testWithCsvSource(String first, int second) {
    assertNotNull(first);
    assertNotEquals(0, second);
}
```

`@CsvSource`使用`'`作为转义字符。 请参阅上述示例和下表中的'baz, qux'值。 一个空的引用值`''`会导致一个空的`String`; 而一个完全空的值被解释为一个null引用。如果null引用的目标类型是基本类型，则引发`ArgumentConversionException`。

| 示例输入 | 结果字符列表 |
|--------|--------|
|    @CsvSource({ "foo, bar" })    |   `"foo"`, `"bar"`    |
|    @CsvSource({ "foo, 'baz, qux'" })    |    `"foo"`, `"baz, qux"`    |
|    @CsvSource({ "foo, ''" })    |     `"foo"`, `""`   |
|    @CsvSource({ "foo, " })    |    `"foo"`, null    |

### @CsvFileSource

`@CsvFileSource`让你使用classpath中的CSV文件。CSV文件中的每一行都会导致参数化测试的一次调用。

```java
@ParameterizedTest
@CsvFileSource(resources = "/two-column.csv")
void testWithCsvFileSource(String first, int second) {
    assertNotNull(first);
    assertNotEquals(0, second);
}
```

_two-column.csv_

```java
foo, 1
bar, 2
"baz, qux", 3
```

与`@CsvSource`中使用的语法相反，`@CsvFileSource`使用双引号`"`作为转义字符，请参阅上面例子中的`"baz, qux"`值，一个空的转义值`""`会产生一个空字符串， 一个完全为空的值被解释为null引用，如果null引用的目标类型是基本类型，则引发`ArgumentConversionException`。

### @ArgumentsSource

可以使用`@ArgumentsSource`指定一个自定义的，可重用的`ArgumentsProvider`。

```java
@ParameterizedTest
@ArgumentsSource(MyArgumentsProvider.class)
void testWithArgumentsSource(String argument) {
    assertNotNull(argument);
}

static class MyArgumentsProvider implements ArgumentsProvider {

    @Override
    public Stream< ? extends Arguments > provideArguments(ExtensionContext context) {
        return Stream.of("foo", "bar").map(Arguments::of);
    }
}
```

## 参数转换

### 隐式转换

为了支持像`@CsvSource`这样的情况，JUnit Jupiter提供了一些内置的隐式类型转换器。 转换过程取决于每个方法参数的声明类型。

例如，如果`@ParameterizedTest`声明`TimeUnit`类型的参数，并且声明的源提供的实际类型是`String`，则该字符串将自动转换为相应的`TimeUnit`枚举常量。

```java
@ParameterizedTest
@ValueSource(strings = "SECONDS")
void testWithImplicitArgumentConversion(TimeUnit argument) {
    assertNotNull(argument.name());
}
```

`String`实例目前隐式转换为以下目标类型。

| 目标类型 | 示例 |
|--------|--------|
| `boolean`/`Boolean` | `"true"` → `true` |
| `byte`/`Byte` | `"1"` → `(byte) 1` |
| `char`/`Character` | `"o"` → `'o'` |
| `short`/`Short` | `"1"` → `(short) 1` |
| `int`/`Integer` | `"1"` → `1` |
| `long`/`Long` | `"1"` → `1L` |
| `float`/`Float` | `"1.0"` → `1.0f` |
| `double`/`Double` | `"1.0"` → `1.0d` |
| `Enum` subclass | `"SECONDS"` → `TimeUnit.SECONDS` |
| `java.time.Instant` | `"1970-01-01T00:00:00Z"` → `Instant.ofEpochMilli(0)` |
| `java.time.LocalDate` | `"2017-03-14"` → `LocalDate.of(2017, 3, 14)` |
| `java.time.LocalDateTime` | `"2017-03-14T12:34:56.789"` → `LocalDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000)` |
| `java.time.LocalTime` | `"12:34:56.789"` → `LocalTime.of(12, 34, 56, 789_000_000)` |
| `java.time.OffsetDateTime` | `"2017-03-14T12:34:56.789Z"` → `OffsetDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000, ZoneOffset.UTC)` |
| `java.time.OffsetTime` | `"12:34:56.789Z"` → `OffsetTime.of(12, 34, 56, 789_000_000, ZoneOffset.UTC)` |
| `java.time.Year` | `"2017"` → `Year.of(2017)` |
| `java.time.YearMonth` | `"2017-03"` → `YearMonth.of(2017, 3)` |
| `java.time.ZonedDateTime` | `"2017-03-14T12:34:56.789Z"` → `ZonedDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000, ZoneOffset.UTC)` |

### 显式转换

您可以使用`@ConvertWith`注解来显式指定`ArgumentConverter`来用于某个参数，而不是像下面的示例那样使用隐式参数转换。

```java
@ParameterizedTest
@EnumSource(TimeUnit.class)
void testWithExplicitArgumentConversion(@ConvertWith(ToStringArgumentConverter.class) String argument) {
    assertNotNull(TimeUnit.valueOf(argument));
}

static class ToStringArgumentConverter extends SimpleArgumentConverter {

    @Override
    protected Object convert(Object source, Class< ?> targetType) {
        assertEquals(String.class, targetType, "Can only convert to String");
        return String.valueOf(source);
    }
}
```

显式参数转换器意味着由测试作者实现。因此，`junit-jupiter-params`只提供一个显式的参数转换器，可以作为参考实现：`JavaTimeArgumentConverter`。通过组合的注解`JavaTimeConversionPattern`使用。

```java
@ParameterizedTest
@ValueSource(strings = { "01.01.2017", "31.12.2017" })
void testWithExplicitJavaTimeConverter(@JavaTimeConversionPattern("dd.MM.yyyy") LocalDate argument) {
    assertEquals(2017, argument.getYear());
}
```

## 自定义显示名称

默认情况下，参数化测试调用的显示名称包含该特定调用的所有参数的调用索引和字符串表示。 但是，您可以通过`@ParameterizedTest`注解的`name`属性自定义调用显示名称，如下所示：

```java
@DisplayName("Display name of container")
@ParameterizedTest(name = "{index} ==> first=''{0}'', second={1}")
@CsvSource({ "foo, 1", "bar, 2", "'baz, qux', 3" })
void testWithCustomDisplayNames(String first, int second) {
}
```

当使用`ConsoleLauncher`执行上述方法时，您将看到类似于以下内容的输出。

```bash
Display name of container ✔
├─ 1 ==> first='foo', second=1 ✔
├─ 2 ==> first='bar', second=2 ✔
└─ 3 ==> first='baz, qux', second=3 ✔
```

自定义显示名称中支持以下占位符。

| 占位符 | 描述 |
|--------|--------|
| `{index}`         | 当前调用下标(从1开始) |
| `{arguments}`     | 完成的，逗号分隔的参数列表  |
| `{0}`, `{1}`, ... | 单个参数 |

## 生命周期和互操作性

参数化测试的每个调用与普通的`@Test`方法具有相同的生命周期。例如，`@BeforeEach`方法将在每次调用之前执行。与[动态测试](dynamic-tests.md)类似，调用将逐个出现在IDE的测试树中。可能会在同一个测试类中混合常规的`@Test`方法和`@ParameterizedTest`方法。

可以在`@ParameterizedTest`方法中使用`ParameterResolver`扩展。但是，由参数来源解析的方法参数需要先在参数列表中找到。由于测试类可能包含常规测试，以及具有不同参数列表的参数化测试，因此参数源的值不会针对生命周期方法（例如`@BeforeEach`）和测试类构造函数进行解析。

```java
@BeforeEach
void beforeEach(TestInfo testInfo) {
    // ...
}

@ParameterizedTest
@ValueSource(strings = "foo")
void testWithRegularParameterResolver(String argument, TestReporter testReporter) {
    testReporter.publishEntry("argument", argument);
}

@AfterEach
void afterEach(TestInfo testInfo) {
    // ...
}
```