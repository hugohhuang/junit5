# 注解

JUnit Jupiter支持下列注解，用于配置测试和扩展框架。

所有核心注解位于`junit-jupiter-api`模块中的[`org.junit.jupiter.api`](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/package-summary.html)包中。

| 注解 | 描述 |
|--------|--------|
|   @Test     |    表示方法是测试方法。与JUnit4的@Test注解不同的是，这个注解没有声明任何属性，因为JUnit Jupiter中的测试扩展是基于他们自己的专用注解来操作的。除非被覆盖，否则这些方法可以继承。    |
|   @ParameterizedTest     |   表示方法是[参数化测试](../writing-tests/parameterized-tests.md)。 除非被覆盖，否则这些方法可以继承。    |
|   @RepeatedTest     |   表示方法是用于[重复测试](../writing-tests/repeated-tests.md)的测试模板。除非被覆盖，否则这些方法可以继承。     |
|   @TestFactory     |    表示方法是用于[动态测试](../writing-tests/dynamic-tests.md)的测试工厂。除非被覆盖，否则这些方法可以继承。    |
|   @TestInstance     |    用于为被注解的测试类配置[测试实例生命周期][]。 这个注解可以继承。    |
|   @TestTemplate     |    表示方法是[测试用例的模板](../writing-tests/test-templates.md)，设计为被调用多次，调用次数取决于自注册的提供者返回的调用上下文。除非被覆盖，否则这些方法可以继承。    |
|   @DisplayName     |    声明测试类或测试方法的自定义显示名称。这个注解不被继承。    |
|   @BeforeEach     |   表示被注解的方法应在当前类的每个@Test，@RepeatedTest，@ParameterizedTest或@TestFactory方法**之前**执行; 类似于JUnit 4的@Before。 除非被覆盖，否则这些方法可以继承。     |
|   @AfterEach     |    表示被注解的方法应在当前类的每个@Test，@RepeatedTest，@ParameterizedTest或@TestFactory方法**之后**执行; 类似于JUnit 4的@After。 除非被覆盖，否则这些方法可以继承。    |
|   @BeforeAll     |   表示被注解的方法应该在当前类的所有@Test，@RepeatedTest，@ParameterizedTest和@TestFactory方法**之前**执行; 类似于JUnit 4的@BeforeClass。 这样的方法可以继承（除非被隐藏或覆盖），并且必须是静态的（除非使用“per-class”[测试实例生命周期][]）。     |
|   @AfterAll     |    表示被注解的方法应该在当前类的所有@Test，@RepeatedTest，@ParameterizedTest和@TestFactory方法**之后**执行; 类似于JUnit 4的@AfterClass。 这样的方法可以继承（除非被隐藏或覆盖），并且必须是静态的（除非使用“per-class”[测试实例生命周期][]）。   |
|   @Nested     |    表示被注解的类是一个嵌套的非静态测试类。除非使用“per-class”[测试实例生命周期][]，否则@BeforeAll和@AfterAll方法不能直接在@Nested测试类中使用。 这个注解不能继承。    |
|   @Tag     |   在类或方法级别声明标签，用于过滤测试; 类似于TestNG中的test group或JUnit 4中的Categories。这个注释可以在类级别上继承，但不能在方法级别上继承。     |
|   @Disabled     |    用于禁用测试类或测试方法; 类似于JUnit4的@Ignore。这个注解不能继承。    |
|   @ExtendWith     |    用于注册自定义[扩展](../extensions/)。 这个注解可以继承。    |

使用@Test，@TestTemplate，@RepeatedTest，@BeforeAll，@AfterAll，@BeforeEach或@AfterEach注解的方法不能有返回值。

[测试实例生命周期]:../writing-tests/test-instance-lifecycle.md

## 元注解和组合注解

JUnit Jupiter注解可以用作元注解。这意味着您可以定义自己的组合注释，它将自动继承其元注释的语义。

例如，您可以像下面那样创建一个名为`@Fast`的自定义组合注释，而不必在整个代码库（请参阅[标签和过滤](../writing-tests/tagging-and-filtering.md)）中复制和粘贴`@Tag("fast")`。然后@Fast可以用作`@Tag("fast")`的一个替代品。

