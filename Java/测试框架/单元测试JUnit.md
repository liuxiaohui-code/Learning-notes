# JUnit

JUnit 是一个开源的 Java 语言的单元测试框架，专门针对 Java 设计，使用最广泛。JUnit 是事实上的单元测试的标准框架，任何 Java 开发者都应当学习并使用 JUnit 编写单元测试。

```java
package com.itranswarp.learnjava;

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class FactorialTest {
    @Test
    void testFact() {
        assertEquals(1, Factorial.fact(1));
        assertEquals(2, Factorial.fact(2));
        assertEquals(6, Factorial.fact(3));
        assertEquals(3628800, Factorial.fact(10));
        assertEquals(2432902008176640000L, Factorial.fact(20));
    }
}
```

1. `@Test` 在测试方法上标注,表明这是一个测试方法
2. 常用方法
3. `@BeforeEach` 和`@AfterEach` 在运行每个`@Test` 方法前后自动运行
4. `@BeforeAll` 和`@AfterAll`,标注在静态方法上,初始化静态变量,在所有@Test 方法运行前后仅运行一次
5. 异常测试 `assertThrows()`
6. `@Disabled`暂不测试
7. 条件测试
   1. `@EnabledOnOs(OS.WINDOWS)` 只在 Windows 系统上测试
   2. `@DisabledOnOs(OS.WINDOWS)` 不在 Windows 系统上运行
   3. `@DisabledOnJre(JRE.JAVA_8)` 只在 Java9 以及上版本运行
   4. `@EnabledIfSystemProperty(named = "os.arch", matches = ".*64.*")` 只能在 64 位操作系统上执行的测试
   5. `@EnabledIfEnvironmentVariable(named = "DEBUG", matches = "true")`需要传入环境变量 DEBUG=true 才能执行的测试
   6. `@EnableIf` 执行任意 Java 语句并根据返回的 boolean 决定是否执行测试 `@EnabledIf("java.time.LocalDate.now().getDayOfWeek()==java.time.DayOfWeek.SUNDAY")`
8. 参数化测试 `@ParameterizedTest`
   1. `@ValueSource` 单参数测试,传入参数
   2. `@MethodSource` 多参数测试,同名静态方法,返回参数集合
   3. `@CsvSource` 多参数测试,它的每一个字符串表示一行，一行包含的若干参数用,分隔
   4. `@CsvFileSource` 多参数测试,使用 csv 文件,JUnit 只在 classpath 中查找指定的 CSV 文件，因此，xx.csv 这个文件要放到 test 目录

```java
// Assertion 断言类
assertEquals(expected, actual) // 期待预期结果
assertEquals(double expected, double actual, double delta);// 比较浮点数,指定误差值
assertTrue()            //期待结果为true
assertFalse()           //期待结果为false
assertNotNull()         //期待结果为非null
assertArrayEquals()     //期待结果为数组并与期望数组每个元素的值均相等

assertThrows()          // 期望捕获一个异常
```

```java
class CalculatorTest{
  Person p;

  @BeforeEach // 在每个test之前执行
  void init(){
    p = new Person();
  }

  @AfterEach // 在每个test之后执行
  void finished(){
    p = null;
  }
  @BeforeAll
  static void first(){
    // 在所有测试之前运行
  }
  @AfterAll
  static void end(){
    // 在所有测试之后执行
  }

// 单参数测试
@ParameterizedTest
@ValueSource(ints = { 0, 1, 5, 100 })
void testAbs(int x) {
    assertEquals(x, Math.abs(x));
}
// 多参数参数化测试
@ParameterizedTest
@MethodSource
void testCapitalize(String input, String result) {
    assertEquals(result, StringUtils.capitalize(input));
}
static List<Arguments> testCapitalize() {
    return List.of( // arguments:
            Arguments.arguments("abc", "Abc"), //
            Arguments.arguments("APPLE", "Apple"), //
            Arguments.arguments("gooD", "Good"));
}
// 多参数
@ParameterizedTest
@CsvSource({ "abc, Abc", "APPLE, Apple", "gooD, Good" })
void testCapitalize(String input, String result) {
    assertEquals(result, StringUtils.capitalize(input));
}
}


```
