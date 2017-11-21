# 测试模板

`@TestTemplate`方法不是常规的测试用例，而是测试用例的模板。为此，它被设计成根据已注册的提供者返回的调用上下文的数量被调用多次。因此，它必须与注册的`TestTemplateInvocationContextProvider`扩展一起使用。测试模板方法的每次调用都像执行常规`@Test`方法一样，完全支持相同的生命周期回调和扩展。有关使用示例，请参阅[为测试模板提供调用上下文](../extensions/test-templates.md)。

