# Junit 5是什么？

与以前的JUnit版本不同，JUnit 5由三个不同子项目的多个不同模块组成。

**JUnit 5 = _JUnit Platform_ + _JUnit Jupiter_ + _JUnit Vintage_**

**JUnit Platform**为在JVM上[启动测试框架][]提供基础。它还定义了[TestEngine][] API, 用来开发在平台上运行的测试框架。此外，平台提供了一个[控制台启动器][]，用于从命令行启动平台，并为[Gradle][]和[Maven][]提供构建插件以及[基于JUnit 4的Runner][junit4-runner]，用于在平台上运行任意`TestEngine`。

**JUnit Jupiter**是在JUnit 5中编写测试和扩展的新型[编程模型][]和[扩展模型][]的组合.Jupiter子项目提供了`TestEngine`，用于在平台上运行基于Jupiter的测试。

**JUnit Vintage**提供`TestEngine`，用于在平台上运行基于JUnit 3和JUnit 4的测试。

[启动测试框架]: http://junit.org/junit5/docs/current/user-guide/#launcher-api
[TestEngine]: http://junit.org/junit5/docs/current/api/org/junit/platform/engine/TestEngine.html
[控制台启动器]: http://junit.org/junit5/docs/current/user-guide/#running-tests-console-launcher
[Gradle]: http://junit.org/junit5/docs/current/user-guide/#running-tests-build-gradle
[Maven]: http://junit.org/junit5/docs/current/user-guide/#running-tests-build-maven
[junit4-runner]: http://junit.org/junit5/docs/current/user-guide/#running-tests-junit-platform-runner
[编程模型]: http://junit.org/junit5/docs/current/user-guide/#writing-tests
[扩展模型]: http://junit.org/junit5/docs/current/user-guide/#extensions