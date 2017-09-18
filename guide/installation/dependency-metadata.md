# 依赖元数据

## JUnit平台

* **Group ID**: `org.junit.platform`
* **Version**: `{{book.platformVersion}}`
* **Artifact IDs**:

| Artifact | 说明 |
|--------|--------|
|   junit-platform-commons     |    JUnit的内部公用库/工具。<br>这些工具预期仅用于在JUnit框架本身内部使用。<br>_不支持任何外部使用_。使用它需要自己承担风险！    |
|   junit-platform-console     |    支持从控制台发现和执行JUnit Platform上的测试。<br>有关详情，请参阅[控制台启动器][]    |
|   `junit-platform-console-standalone`     |    Maven Central中，在<br>[junit-platform-console-standalone][]<br>目录下，提供了一个包含所有依赖的可执行JAR 。<br>有关详情，请参阅[控制台启动器][]    |
|   `junit-platform-engine`     |    用于测试引擎（test engine）的公共API.<br>有关详细信息，请参阅[插入自己的测试引擎][]。   |
|   `junit-platform-gradle-plugin`     |    支持用[Gradle][]在JUnit Platform上发现和执行测试 。   |
|   `junit-platform-launcher`     |    用于配置和启动测试计划的公共API。<br> 通常被IDE和构建工具使用。<br>详情请参阅[JUnit Platform Launcher API][]。   |
|   `junit-platform-runner`     |    用于在JUnit 4环境中，在JUnit平台上执行测试和测试套件的运行程序。有关详细信息，请参阅[使用JUnit 4运行JUnit平台][]。   |
|   `junit-platform-suite-api`     |    在JUnit平台上配置测试套件的注释。 由JUnitPlatform转换器支持， 也可能由第三方 TestEngine实现。   |
|   `junit-platform-surefire-provider`     |    支持使用[Maven Surefire][]在JUnit平台上发现和执行测试 。。   |


[控制台启动器]: http://junit.org/junit5/docs/current/user-guide/#running-tests-console-launcher
[junit-platform-console-standalone]: https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone
[插入自己的测试引擎]: http://junit.org/junit5/docs/current/user-guide/#launcher-api-engines-custom
[Gradle]: http://junit.org/junit5/docs/current/user-guide/#running-tests-build-gradle
[JUnit Platform Launcher API]: http://junit.org/junit5/docs/current/user-guide/#launcher-api
[使用JUnit 4运行JUnit平台]: http://junit.org/junit5/docs/current/user-guide/#running-tests-junit-platform-runner
[Maven Surefire]: http://junit.org/junit5/docs/current/user-guide/#running-tests-build-maven
