# mockito

```xml
<!-- java 8 最高版本 -->
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>4.11.0</version>
    <scope>test</scope>
</dependency>
```

## 方法

```java
// 创建mock对象
List mockList = Mockito.mock(ArrayList.class);
```

## 使用示例

```java
package org.lxh.demo.test;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import java.util.ArrayList;
import java.util.List;

class MockTest {
    @Test
    public void testA(){
        //创建ArrayList的Mock对象
        List mockList = Mockito.mock(ArrayList.class);
        //pass
        Assertions.assertTrue(mockList instanceof ArrayList);

        //当我们mockList调用方法去add("张三")的时候会返回true
        Mockito.when(mockList.add("张三")).thenReturn(true);
        //当我们mockList调用方法size()的时候返回10
        Mockito.when(mockList.size()).thenReturn(10);
        //pass
        Assertions.assertTrue(mockList.add("张三"));
        //pass
        Assertions.assertFalse(mockList.add("李四"));
        //pass
        Assertions.assertEquals(mockList.size(),10);
        //null
        System.out.println(mockList.get(0));
    }
}


```
