# 依赖元数据

## JUnit Platform

* **Group ID**: `org.junit.platform`
* **Version**: `{{book.platformVersion}}`
* **Artifact IDs**:

| Artifact | 说明 |
|--------|--------|
|   junit-platform-commons     |    JUnit的内部公共库/工具。<br>这些工具预期仅用于在JUnit框架本身内部使用。<br>_不支持任何外部使用_。使用它需要自己承担风险！    |
|   junit-platform-console     |    支持从控制台发现和执行JUnit Platform上的测试。<br>有关详情，请参阅[控制台启动器][]    |
|   `junit-platform-console-standalone`     |    Maven Central中，在<br>[junit-platform-console-standalone][]<br>目录下，提供了一个包含所有依赖的可执行JAR 。<br>有关详情，请参阅[控制台启动器][]    |
|   `junit-platform-engine`     |    用于test engine的公共API.<br>有关详细信息，请参阅[插入自己的测试引擎][]。   |
|   `junit-platform-gradle-plugin`     |    支持用[Gradle][]在JUnit Platform上发现和执行测试 。   |
|   `junit-platform-launcher`     |    用于配置和启动test plan的公共API。<br> 通常被IDE和构建工具使用。<br>详情请参阅[JUnit Platform Launcher API][]。   |
|   `junit-platform-runner`     |    用于在JUnit 4环境中，在JUnit Platform上<br>执行test和test suite的运行程序。<br>有关详细信息，请参阅[使用JUnit4运行JUnit平台][]。   |
|   `junit-platform-suite-api`     |    在JUnit Platform上配置test suite的注解。 <br>由[JUnitPlatform runner][runner]转换器支持，<br>也可能由第三方 TestEngine实现。   |
|   `junit-platform-surefire-provider`     |    支持使用[Maven Surefire][]在JUnit Platform上<br>发现和执行测试 。。   |


[控制台启动器]: ../running-tests/console-launcher.md
[junit-platform-console-standalone]: https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone
[插入自己的测试引擎]: ../advanced-topics/launcher-api.md#插入自己的测试引擎
[Gradle]: ../running-tests/build.md#gradle
[JUnit Platform Launcher API]: ../advanced-topics/launcher-api.md
[使用JUnit4运行JUnit平台]: ../running-tests/junit-platform-runner.md
[runner]:../running-tests/junit-platform-runner.md
[Maven Surefire]: ../running-tests/build.md#maven

## JUnit Jupiter

* **Group ID**: `org.junit.jupiter`
* **Version**: `{{book.jupiterVersion}}`
* **Artifact IDs**:

| Artifact ID | 说明 |
|--------|--------|
|   `junit-jupiter-api`     |    JUnit Jupiter API，用来[编写测试](../running-tests/) 和[扩展](../extensions/)    |
|   `junit-jupiter-engine`     |  JUnit Jupiter测试引擎实现，仅在运行时需要    |
|   `junit-jupiter-params`     |    支持在JUnit Jupiter中[参数化测试](../writing-tests/parameterized-tests.md)    |
|   `junit-jupiter-migrationsupport`     |    从JUnit4到JUnit Jupiter的迁移支持，<br>仅在运行选定的JUnit规则时需要    |

## JUnit Vintage

* **Group ID**: `org.junit.vintage`
* **Version**: `{{book.vintageVersion}}`
* **Artifact IDs**:

| Artifact ID | 说明 |
|--------|--------|
|   `junit-vintage-engine`     |    JUnit Vintage测试引擎实现，容许在新的JUnit Platform上<br>运行vintage JUnit测试，例如用JUnit3或者JUnit4风格写的测试    |

## 可选依赖

上面的所有构建，在他们发布的maven pom文件中，都有一个`可选`依赖，就是下面的@API Guardian JAR.

* *Group ID*: `org.apiguardian`
* *Artifact ID*: `apiguardian-api`
* *Version*: `{{book.apiguardianVersion}}`

