# 标签和过滤

测试类和方法可以打标签。这些标签以后可以用来过滤[测试发现和执行](../running-tests/)。

## 标签的语法规则

* 标签不能为`null`或空白。

* 裁剪标签不能包含空格(whitespace)。

* 裁剪标签不得包含ISO控制字符。

* 裁剪标签不得包含以下任何_保留_字符。

	- `,`, `(`, `)`, `&`, `|`, `!`

> 在上面的上下文中，“裁剪”意思是前后的空白字符已被删除。

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Tag("fast")
@Tag("model")
class TaggingDemo {

    @Test
    @Tag("taxes")
    void testingTaxCalculation() {
    }

}
```