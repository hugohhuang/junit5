# Junit 5是什么？

与以前的JUnit版本不同，JUnit 5由三个不同子项目的多个不同模块组成。

**JUnit 5 = _JUnit Platform_ + _JUnit Jupiter_ + _JUnit Vintage_**

**JUnit Platform**为在JVM上[启动测试框架][]提供基础。它还定义了[TestEngine][] API, 用来开发在平台上运行的测试框架。此外，平台提供了一个[控制台启动器][]，用于从命令行启动平台，并为[Gradle][]和[Maven][]提供构建插件以及[基于JUnit 4的Runner][junit4-runner]，用于在平台上运行任意`TestEngine`。

**JUnit Jupiter**是在JUnit 5中编写测试和扩展的新型[编程模型][]和[扩展模型][]的组合.Jupiter子项目提供了`TestEngine`，用于在平台上运行基于Jupiter的测试。

**JUnit Vintage**提供`TestEngine`，用于在平台上运行基于JUnit 3和JUnit 4的测试。

[启动测试框架]: ../advanced-topics/launcher-api.md
[TestEngine]: http://junit.org/junit5/docs/current/api/org/junit/platform/engine/TestEngine.html
[控制台启动器]: ../running-tests/console-launcher.md
[Gradle]: ../running-tests/build.md#gradle
[Maven]: ../running-tests/build.md#maven
[junit4-runner]: ../running-tests/junit-platform-runner.md
[编程模型]: ../writing-tests/
[扩展模型]: ../extensions/