# 测试实例生命周期

为了允许隔离执行单个的测试方法，并避免由于可变测试实例状态而产生的意外副作用，JUnit在执行每个测试方法之前创建每个测试类的新实例（请参阅下面的讲解，何为测试方法）。这个"per-method"测试实例生命周期是JUnit Jupiter中的默认行为，类似于JUnit以前的所有版本。

如果您希望JUnit Jupiter在同一个测试实例上执行所有测试方法，只需使用`@TestInstance(Lifecycle.PER_CLASS)`对您的测试类进行注解即可。当使用这种模式时，每个测试类将创建一个新的测试实例。因此，如果您的测试方法依赖于存储在实例变量中的状态，则可能需要在`@BeforeEach`或`@AfterEach`方法中重置该状态。

"per-class"模式比默认的"per-method"模式有一些额外的好处。具体来说，使用"per-class"模式，可以在非静态方法和接口默认方法上声明`@BeforeAll`和`@AfterAll`。因此，"per-class"模式也可以在`@Nested`测试类中使用`@BeforeAll`和`@AfterAll`方法。

如果使用Kotlin编程语言编写测试，则可能会发现，通过切换到"per-class"测试实例生命周期模式，可以更轻松地实现`@BeforeAll`和`@AfterAll`方法。

> 在测试实例生命周期的上下文中，测试方法是用`@Test`，`@RepeatedTest`，`@ParameterizedTest`，`@TestFactory`或`@TestTemplate`注解的任何方法。

## 修改默认测试实例生命周期

如果测试类或测试接口没有用`@TestInstance`注解，JUnit Jupiter将使用默认的生命周期模式。标准默认模式是`PER_METHOD`; 但是，可以更改整个测试计划执行的默认值。要更改默认测试实例生命周期模式，只需将`junit.jupiter.testinstance.lifecycle.default`配置参数设置为在`TestInstance.Lifecycle`中定义的枚举常量的名称，忽略大小写。这可以作为JVM系统属性提供，作为传递给`Launcher`的`LauncherDiscoveryRequest`中的配置参数，或通过JUnit Platform配置文件（请参阅[配置参数](../running-tests/config-params.md)了解详细信息）。

例如，要将默认测试实例生命周期模式设置为`Lifecycle.PER_CLASS`，可以使用以下系统属性启动JVM。

```bash
-Djunit.jupiter.testinstance.lifecycle.default=per_class
```

但是请注意，通过JUnit Platform配置文件设置默认测试实例生命周期模式是一个更健壮的解决方案，因为可以将配置文件与项目一起检入到版本控制系统中，因此可以在IDE和构建软件中使用。

要通过JUnit Platform配置文件将默认测试实例生命周期模式设置为`Lifecycle.PER_CLASS`，请在class path 的根目录（例如`src/test/resources`）中使用以下内容创建名为`junit-platform.properties`的文件。

```bash
junit.jupiter.testinstance.lifecycle.default = per_class
```

> 更改默认的测试实例生命周期模式可能会导致不可预测的结果和脆弱的构建，如果应用的不一致。例如，如果构建将"per-class"语义配置为默认值，但是IDE中的测试使用"per-method"的语义来执行，则可能使调试构建服务器上发生的错误变得困难。因此，建议更改JUnit Platform配置文件中的默认值，而不是通过JVM系统属性。
