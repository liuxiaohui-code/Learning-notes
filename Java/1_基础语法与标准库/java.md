# JAVA

# 数据类型与变量

Java 语言提供了八种基本类型。六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。

| 数据类型 | 最小值  | 最大值   | 存储大小(bit) | 默认值      | 包装类    |
| -------- | ------- | -------- | ------------- | ----------- | --------- |
| byte     | -2^7    | 2^7-1    | 8             | 0           | Byte      |
| short    | -2^15   | 2^15-1   | 16            | 0           | Short     |
| int      | -2^31   | 2^31-1   | 32            | 0           | Integer   |
| long     | -2^63   | 2^63-1   | 64            | 0L          | Long      |
| float    | 2^-149  | 2^128-1  | 32            | 0.0F        | Float     |
| double   | 2^-1074 | 2^1024-1 | 64            | 0.0         | Double    |
| boolean  |         |          | 32            | false       | Boolean   |
| char     | \u0000  | \uffff   | 16            | \u0000 即 0 | Character |

- Java 语言对布尔类型的存储并没有做规定，因为理论上存储布尔类型只需要 1 bit，但是通常 JVM 内部会把 boolean 表示为 4 字节整数。
- float 最多识别45位小数
- double 最多识别323位小数


**包装类**

1. 基本类型的包装类
2. jdk1.5 自动装箱与拆箱
3. `Integer` `==`比较;为了节省内存，`Integer.valueOf()`对于较小的数`(<128)`，**始终返回相同的实例**，因此，`==`比较“恰好”为`true`，所以必须使用 `equals()`比较包装类
4. 创建包装类对象优先使用`Integer.valueOf()` 静态工厂的方式,而不是 `new` 操作符

· Number：Integer、Short、Long、Double、Float、Byte 都是 Number 的子类表示是一个 数字。

· Object：Character、Boolean 都是 Object 的直接子类。

### 进制(jdk7)

byte、int、long、和 short 都可以用十进制、16 进制以及 8 进制的方式来表示。

1. 前缀 **0b** 二进制
2. 前缀 **0** 表示 8 进制
3. 前缀 **0x** 代表 16 进制

### 数字下划线(jdk7)

Java 7 开始支持在数字定义时候使用下划线分割，增加了数字的可读性。

```java
int a = 10_000_000;
int b = 10_0000;

// 错误转换
Integer.valueOf("10_000_000");
```

### 类型转换

#### 自动类型转换

**整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。**

转换从低级到高级。

```java
低  ------------------------------------>  高

byte,short,char—> int —> long—> float —> double
```

数据类型转换必须满足如下规则：

1. 不能对 boolean 类型进行类型转换。
2. 不能把对象类型转换成不相关类的对象。
3. 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。
4. 转换过程中可能导致溢出或损失精度，例如：

```java
int i =128;
byte b = (byte)i;
```

因为 byte 类型是 8 位，最大值为 127，所以当 int 强制转换为 byte 类型时，值 128 时候就会导致溢出。 5. 浮点数到整数的转换是通过舍弃小数得到，而不是四舍五入，例如：

```java
(int)23.7 == 23;
(int)-45.89f == -45
```

#### 强制类型转换

- 条件是转换的数据类型必须是兼容的。
- 格式：`(type)value type`是要强制类型转换后的数据类型

```java
  int i =128;
  byte b = (byte)i;
```

要注意，超出范围的强制转型会得到错误的结果，原因是转型时，高位字节直接被扔掉，仅保留了低位的两个字节

#### 字符串转换

```java
//在Integer类中提供了以下的操作方法：
public static int parseInt(String s) ：将String变为int型数据
//在Float类中提供了以下的操作方法：
public static float parseFloat(String s) ：将String变为Float
//在Boolean 类中提供了以下操作方法：
public static boolean parseBoolean(String s) ：将String变为boolean
```

### 高精度大数字

- java.math.BigDecimal 不可变的，任意精度的带符号十进制数。
- java.math.BigInteger 不可变的任意精度整数

### Optional 类型(jdk8)

Java 8 中新增了 Optional 类用来解决空指针异常。Optional 是一个可以保存 null 的容器对象。通过 isPresent() 方法检测值是否存在，通过 get() 方法返回对象。

### 引用类型

- 在 Java 中，引用类型的变量非常类似于 C/C++的指针。引用类型指向一个对象，指向对象的变量是引用变量。这些变量在声明时被指定为一个特定的类型，比如 Employee、Puppy 等。变量一旦声明后，类型就不能被改变了。
- 对象、数组都是引用数据类型。
- 所有引用类型的默认值都是 null。
- 一个引用变量可以用来引用任何与之兼容的类型。
- 例子：Site site = new Site("Runoob")。

### 常量

常量在程序运行时是不能被修改的。

在 Java 中使用 final 关键字来修饰常量，声明方式和变量类似：

```java
final <数据类型> <变量名> = <值>;

final int NUMBER = 0;
```

虽然常量名也可以用小写，但为了便于识别，通常使用大写字母+下划线表示常量。

### 变量

关于 Java 标识符，有以下几点需要注意：

- 所有的标识符都应该以字母（A-Z 或者 a-z）,美元符（$）、或者下划线（\_）开始
- 首字符之后可以是字母（A-Z 或者 a-z）,美元符（$）、下划线（\_）或数字的任何字符组合
- 关键字不能用作标识符
- 标识符是大小写敏感的
- 合法标识符举例：age、$salary、\_value、\_\_1_value
- 非法标识符举例：123abc、-salary

```java
<数据类型> <变量名> = <值>  [,<变量名1> = <值1>,....]；

// 可以使用 var 关键字替换变量的类型,让编译器自行推断数据类型
var sb = new StringBuilder();
// 编译器会将以上语句处理为
StringBuilder sb = new StringBuilder();
// 使用var定义变量，仅仅是少写了变量类型而已。
```

**局部变量**

- 局部变量声明在方法、构造方法或者语句块中；
- 局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁；
- 访问修饰符不能用于局部变量；
- 局部变量只在声明它的方法、构造方法或者语句块中可见；
- 局部变量是在栈上分配的。
- 局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。

**实例变量**

- 实例变量声明在一个类中，但在方法、构造方法和语句块之外；
- 当一个对象被实例化之后，每个实例变量的值就跟着确定；
- 实例变量在对象创建的时候创建，在对象被销毁的时候销毁；
- 实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息；
- 实例变量可以声明在使用前或者使用后；
- 访问修饰符可以修饰实例变量；
- 实例变量对于类中的方法、构造方法或者语句块是可见的。一般情况下应该把实例变量设为私有。通过使用访问修饰符可以使实例变量对子类可见；
- 实例变量具有默认值。数值型变量的默认值是 0，布尔型变量的默认值是 false，引用类型变量的默认值是 null。变量的值可以在声明时指定，也可以在构造方法中指定；
- 实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名：ObjectReference.VariableName。

**类变量（静态变量）**

- 类变量也称为静态变量，在类中以 static 关键字声明，但必须在方法之外。
- 无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。
- 静态变量除了被声明为常量外很少使用，静态变量是指声明为 public/private，final 和 static 类型的变量。静态变量初始化后不可改变。
- 静态变量储存在静态存储区。经常被声明为常量，很少单独使用 static 声明变量。
- 静态变量在第一次被访问时创建，在程序结束时销毁。
- 与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为 public 类型。
- 默认值和实例变量相似。数值型变量默认值是 0，布尔型默认值是 false，引用类型默认值是 null。变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
- 静态变量可以通过：*ClassName.VariableName*的方式访问。
- 类变量被声明为 public static final 类型时，类变量名称一般建议使用大写字母。如果静态变量不是 public 和 final 类型，其命名方式与实例变量以及局部变量的命名方式一致。

# 运算符

**算术运算符**

算术运算符用在数学表达式中，它们的作用和在数学中的作用一样。下表列出了所有的算术运算符。

表格中的实例假设整数变量 A 的值为 10，变量 B 的值为 20：

| 操作符 | 描述 例子                                 |
| :----- | :---------------------------------------- | :-------------- |
| +      | 加法 - 相加运算符两侧的值                 | A + B 等于 30   |
| -      | 减法 - 左操作数减去右操作数               | A – B 等于 -10  |
| \*     | 乘法 - 相乘操作符两侧的值                 | A \* B 等于 200 |
| /      | 除法 - 左操作数除以右操作数               | B / A 等于 2    |
| ％     | 取余 - 左操作数除以右操作数的余数         | B%A 等于 0      |
| ++     | 自增: 操作数的值增加 1 B++ 或 ++B 等于 21 |
| --     | 自减: 操作数的值减少 1 B-- 或 --B 等于 19 |

```Java
int a = 1;
int b = 1;
int resulta = a++; // resulta=1,a=2
int resultb = ++b; // resultb=2,b=2
```

**关系运算符**

下表为 Java 支持的关系运算符

表格中的实例整数变量 A 的值为 10，变量 B 的值为 20：

| 运算符 | 描述 例子                                                             |
| :----- | :-------------------------------------------------------------------- | :--------------- |
| ==     | 检查如果两个操作数的值是否相等，如果相等则条件为真。 （A == B）为假。 |
| !=     | 检查如果两个操作数的值是否相等，如果值不相等则条件为真。              | (A != B) 为真。  |
| >      | 检查左操作数的值是否大于右操作数的值，如果是那么条件为真。            | （A> B）为假。   |
| <      | 检查左操作数的值是否小于右操作数的值，如果是那么条件为真。            | （A <B）为真。   |
| >=     | 检查左操作数的值是否大于或等于右操作数的值，如果是那么条件为真。      | （A> = B）为假。 |
| <=     | 检查左操作数的值是否小于或等于右操作数的值，如果是那么条件为真。      | （A <= B）为真。 |

**位运算符**

Java 定义了位运算符，应用于整数类型(int)，长整型(long)，短整型(short)，字符型(char)，和字节型(byte)等类型。

位运算符作用在所有的位上，并且按位运算。假设 a = 60，b = 13;它们的二进制格式表示将如下：

```java
A = 0011 1100
B = 0000 1101
-----------------
A&B = 0000 1100
A | B = 0011 1101
A ^ B = 0011 0001
~A= 1100 0011

对`byte`和`short`类型进行移位时，会首先转换为`int`再进行位移。
```

下表列出了位运算符的基本运算，假设整数变量 A 的值为 60 和变量 B 的值为 13：

| 操作符 | 描述 例子                                                                              |
| :----- | :------------------------------------------------------------------------------------- | :----------------------------------------------------------------------- |
| ＆     | 如果相对应位都是 1，则结果为 1，否则为 0 （A＆B），得到 12，即 0000 1100               |
| `      | `                                                                                      | 如果相对应位都是 0，则结果为 0，否则为 1 （A \| B）得到 61，即 0011 1101 |
| ^      | 如果相对应位值相同，则结果为 0，否则为 1 （A ^ B）得到 49，即 0011 0001                |
| `〜`   | 按位取反运算符翻转操作数的每一位，即 0 变成 1，1 变成 0。 （〜A）得到-61，即 1100 0011 |
| <<     | 按位左移运算符。左操作数按位左移右操作数指定的位数。 A << 2 得到 240，即 1111 0000     |
| >>     | 按位右移运算符。左操作数按位右移右操作数指定的位数。 A >> 2 得到 15 即 1111            |
| >>>    | 按位右移补零操作符。左操作数的值按右操作数指定的位数右移，移动得到的空位以零填充。     | A>>>2 得到 15 即 0000 1111                                               |

**逻辑运算符**

下表列出了逻辑运算符的基本运算，假设布尔变量 A 为真，变量 B 为假

| 操作符 | 描述 例子                                                                                 |
| :----- | :---------------------------------------------------------------------------------------- | :----------------- | -------------------------------------------------------------------------------- |
| &&     | 称为逻辑与运算符。当且仅当两个操作数都为真，条件才为真。 （A && B）为假。                 |
| `      |                                                                                           | `                  | 称为逻辑或操作符。如果任何两个操作数任何一个为真，条件为真。 （A \| \| B）为真。 |
| ！     | 称为逻辑非运算符。用来反转操作数的逻辑状态。如果条件为 true，则逻辑非运算符将得到 false。 | ！（A && B）为真。 |

**赋值运算符**

下面是 Java 语言支持的赋值运算符：

| 操作符  | 描述 例子                                                                          |
| :------ | :--------------------------------------------------------------------------------- | :--------------------------------------- |
| =       | 简单的赋值运算符，将右操作数的值赋给左侧操作数 C = A + B 将把 A + B 得到的值赋给 C |
| + =     | 加和赋值操作符，它把左操作数和右操作数相加赋值给左操作数                           | C + = A 等价于 C = C + A                 |
| - =     | 减和赋值操作符，它把左操作数和右操作数相减赋值给左操作数                           | C - = A 等价于 C = C - A                 |
| \* =    | 乘和赋值操作符，它把左操作数和右操作数相乘赋值给左操作数                           | C _ = A 等价于 C = C _ A                 |
| / =     | 除和赋值操作符，它把左操作数和右操作数相除赋值给左操作数                           | C / = A，C 与 A 同类型时等价于 C = C / A |
| （％）= | 取模和赋值操作符，它把左操作数和右操作数取模后赋值给左操作数                       | C％= A 等价于 C = C％A                   |
| << =    | 左移位赋值运算符 C << = 2 等价于 C = C << 2                                        |
| >> =    | 右移位赋值运算符 C >> = 2 等价于 C = C >> 2                                        |
| ＆=     | 按位与赋值运算符 C＆= 2 等价于 C = C＆2                                            |
| ^ =     | 按位异或赋值操作符 C ^ = 2 等价于 C = C ^ 2                                        |
| \| =    | 按位或赋值操作符 C \| = 2 等价于 C = C \| 2                                        |

**条件运算符（?:）**

条件运算符也被称为三元运算符。该运算符有 3 个操作数，并且需要判断布尔表达式的值。该运算符的主要是决定哪个值应该赋值给变量。

```java
variable x = (expression) ? value if true : value if false
```

### instanceof 运算符

该运算符用于操作对象实例，检查该对象是否是一个特定类型（类类型或接口类型）。

instanceof 运算符使用格式如下：

```java
Integer i = Integer.ValueOf(1);

if(i instanceof Number){
    // 如果 i 的类型 Integer 是 Number 类型或是Number类型的子类
}

if(i instanceof Serializable){
    // 如果 i 的类型 Integer 实现了 Serializable 接口
}

```

如果运算符左侧变量所指的对象，是操作符右侧类或接口(class/interface)的一个对象，那么结果为真。

### Java 运算符优先级

当多个运算符出现在一个表达式中，谁先谁后呢？这就涉及到运算符的优先级别的问题。在一个多运算符的表达式中，运算符优先级不同会导致最后得出的结果差别甚大。

例如，（1+3）＋（3+2）\*2，这个表达式如果按加号最优先计算，答案就是 18，如果按照乘号最优先，答案则是 14。

再如，x = 7 + 3 _ 2;这里 x 得到 13，而不是 20，因为乘法运算符比加法运算符有较高的优先级，所以先计算 3 _ 2 得到 6，然后再加 7。

下表中具有最高优先级的运算符在的表的最上面，最低优先级的在表的底部。

| 类别     | 操作符 关联性                               |
| :------- | :------------------------------------------ | :------- |
| 后缀     | () [] . (点操作符) 左到右                   |
| 一元     | expr++ expr-- 从左到右                      |
| 一元     | ++expr --expr + - ～ ！ 从右到左            |
| 乘性     | \* /％ 左到右                               |
| 加性     | + - 左到右                                  |
| 移位     | >> >>> << 左到右                            |
| 关系     | > >= < <= 左到右                            |
| 相等     | == != 左到右                                |
| 按位与   | ＆ 左到右                                   |
| 按位异或 | ^ 左到右                                    |
| 按位或   | \| 左到右                                   |
| 逻辑与   | && 左到右                                   |
| 逻辑或   | \| \| 左到右                                |
| 条件     | ？： 从右到左                               |
| 赋值     | = + = - = \* = / =％= >> = << =＆= ^ = \| = | 从右到左 |
| 逗号     | ， 左到右                                   |

# 数组

- 数组是**引用类型**，并且**数组大小不可变**;
- java.util.Arrays 该类包含用于操作数组的各种方法
- jdk8 增加了 parallelSort() 并行排序算法

```java
//数组声明
//默认值 null
int[] array; // 首选的方法
//或
int array[];  // 效果相同，但不是首选方法

// 数组定义,必须给数组长度;
int[] xs = new int[5];

// 数组赋值
xs[0] = 0;
xs[1] = 1;
xs[2] = 2;
xs[3] = 3;
xs[4] = 4;

// 定义数组时直接指定初始化的元素，这样就不必写出数组大小，而是由编译器自动推算数组大小
int[] xs = {0,1,2,3,4,5};
int[] xs = new int[] {0,1,2,3,4,5};

// 数组长度
int length = xs.length;

int[] xs = {0,1,2,3,4,5};
// for
for(int i = 0;i<xs.length;i++){
  System.out.print(xs[i]+"\t");
}
// for each
for(int el : xs){
  System.out.print(el+"\t");
}
// Arrays.toString()
Arrays.toString(xs)
```

**二维数组**

```java
  String[][] str = new String[3][4];
  // 直接为每一维分配空间，格式如下：
  type[][] typeName = new type[typeLength1][typeLength2];
  //type 可以为基本数据类型和复合数据类型，typeLength1 和 typeLength2 必须为正整数，typeLength1 为行数，typeLength2 为列数。

  //从最高维开始，分别为每一维分配空间，例如：
  String[][] s = new String[2][];
  s[0] = new String[2];
  s[1] = new String[3];
  s[0][0] = new String("Good");
  s[0][1] = new String("Luck");
  s[1][0] = new String("to");
  s[1][1] = new String("you");
  s[1][2] = new String("!");


  int[][] ns = {
    {1,2,3},
    {1,2,3},
    {1,2,3},
    {1,2,3},
  };
  // 多维数组打印
  System.out.println(Arrays.deepToString(ns));
```

## 流程语句

### 循环

1. `while`
2. `do...while`
3. `for(::)`
4. `for(:)`

```java
// ---- 1 ----
while( 布尔表达式 ) {
  //循环内容
}

// ---- 2 ----
do {
       //代码语句
}while(布尔表达式);

// ---- 3 ----
for(初始条件; 循环检测条件; 循环后更新计数器) {
    //代码语句
}

// ---- 4 jdk1.5 数组、集合 ----
for(声明语句 : 表达式)
{
   //代码句子
}
```

### 循环控制

- `break`
- `continue`

### 分支 if-else

```java
if(布尔表达式 1){
   //如果布尔表达式 1的值为true执行代码
}else if(布尔表达式 2){
   //如果布尔表达式 2的值为true执行代码
}else if(布尔表达式 3){
   //如果布尔表达式 3的值为true执行代码
}else {
   //如果以上布尔表达式都不为true执行代码
}
```

### 分支 switch-case

```js
switch (expression) {
	case value:
		//语句
		break; //可选
	case value:
		//语句
		break; //可选
	//你可以有任意数量的case语句
	default: //可选
	//语句
}
```

- switch 语句中的变量类型可以是： `byte` `short` `int` 或者 `char`;
- Java SE 7 开始，switch 支持字符串 `String` 类型和枚举类型，同时 `case` 标签必须为**字符串常量**或**字面量**。

从**Java 12**开始，switch 语句升级为更简洁的表达式语法，使用类似模式匹配（Pattern Matching）的方法，保证只有一种路径会被执行，并且不需要 break 语句：**新语法只会执行匹配的语句，没有穿透效应**。

```java
String fruit =  "apple";
switch(fruit){
  case "apple" -> System.out.println("selected apple");
  case "pear" -> System.out.println("selected pear");
  case "mango" ->{
    System.out.println("good selected ");
    System.out.println("selected mango");
  }
  default -> System.out.println("no fruit selected");
}

使用新的switch语法，不但不需要break，还可以直接返回值:
int x = switch(1){
  case 1 -> 2;
  case 2 -> {
    int code = hash(2);
    yield code; // 但是，如果需要复杂的语句，我们也可以写很多语句，放到{…}里，然后，用yield返回一个值作为switch语句的返回值
  }
  default -> 0;
}
// x = 2;

// 由于switch表达式是作为Java 13的预览特性（Preview Language Features）实现的，编译的时候，我们还需要给编译器加上参数：

javac --source 13 --enable-preview Main.java
```

# 面对对象

## 概念

面向对象思想从**概念**上讲分为以下三种：OOA、OOD、OOP

- `OOA` ：面向对象分析（Object Oriented Analysis）
- `OOD` ：面向对象设计（Object Oriented Design）
- `OOP` ：面向对象程序（Object Oriented Programming

三大**特征**：

- `封装性` ：所有的内容对外部不可见 private 与 getter setter
- `继承性` ：将其他的功能继承下来继续发展 extends
- `多态性` ：方法的重载本身就是一个多态性的体现 interface implements

类与对象：

​ 类表示一个共性的产物，是一个综合的特征，而对象，是一个个性的产物，是一个个体的特征。 （类似生活中的图纸与实物的概念。） 类必须通过对象才可以使用，对象的所有操作都在类中定义。 类由属性和方法组成

- 属性：就相当于人的一个个的特征
- 方法：就相当于人的一个个的行为，例如：说话、吃饭、唱歌、睡觉

**this**

- 调用类中的属性
- 调用类中的方法或构造方法
- 表示当前对象

**super**

- 调用父类中的属性
- 调用父类中的方法或构造方法
- 表示当前父对象

## 类

当在一个源文件中定义多个类，并且还有 import 语句和 package 语句时，要特别注意这些规则。

- 一个源文件中只能有一个 `public` 类
- 一个源文件可以有多个非 `public` 类
- 源文件的名称应该和 `public` 类的类名保持一致。例如：源文件中 `public` 类的类名是 `Employee` ，那么源文件应该命名为 `Employee.java`。
- 如果一个类定义在某个包中，那么 `package` 语句应该在源文件的首行。
- 如果源文件包含 `import` 语句，那么应该放在 `package` 语句和类定义之间。如果没有 package 语句，那么 import 语句应该在源文件中最前面。
- import 语句和 package 语句对源文件中定义的所有类都有效。在同一源文件中，不能给不同的类不同的包声明。

```java
// <类名>.java  文件名与类名保持一致，一个文件一个类
<访问控制> class <类名> { //类名使用大驼峰

	//构造函数
  <类名>(参数列表){}  //无返回值

	//类属性，使用： <类名>.<属性名>
  <访问控制> static <数据类型> <属性名>;

  //类方法，使用： <类名>.<方法名>();
  <访问控制> static <返回值类型> <方法名> (参数列表){}

  //成员属性，使用： <对象名>.<属性名>
  <访问控制> <数据类型> <属性名>;

  //成员方法，使用： <对象名>.<方法名>();
  <访问控制> <返回值类型> <方法名> (参数列表){}
}

public class Person {
  // 属性
  public String name;
  // 静态属性
  public static int age;
  // 构造方法
  Person(){}
  // 成员方法
  public void func(){}
  // 静态代码块
  static {}
  // 构造代码块
  {}
  // 类方法
  public static void func1(){}
}

// 类的实例化
Person p = new Person();

/*
## 类的实例化执行顺序

1. 父类静态代码块
2. 子类静态代码块
3. 父类构造代码块
4. 父类构造器
5. 子类构造代码块
6. 子类构造器
*/

// 静态属性和静态代码块只在类第一次加载时执行一次
// 调用静态属性和静态方法不需要实例化,直接 <类名>.<方法|属性> 即可
```

## 访问控制

Java 中，可以使用访问控制符来保护对类、变量、方法和构造方法的访问。Java 支持 4 种不同的访问权限。

- **private** : 在同一类内可见。使用对象：变量、方法。 **同类**
- **default** (即默认，什么也不写): 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。**同类、同包**
- **protected** : 作用于继承关系。定义为 protected 的字段和方法可以被子类访问，以及子类的子类。使用对象：变量、方法。 **子类**；
- **public** : 对所有类可见。使用对象：类、接口、变量、方法

## 可变参数的方法

一个方法中定义完了参数，则在调用的时候必须传入与其一一对应的参数，但是在 **JDK 1.5** 之后提供了新的功能，可以根据需要自动传入任意个数的参数。

可变参数只能出现在参数列表的最后

```java
返回值类型 方法名称(数据类型 …参数名称){
//参数在方法内部 ， 以数组的形式来接收
}

public void function_name1(int... args){}
//相当于
public void function_name2(int[] args){}
//不同之处在于调用
function_name1(1,2,3);
int[] args = new int[]{1,2,3};
function_name2(x);
```

## 构造方法

作用：用于对象初始化。
执行时机：在创建对象时,自动调用
特点:

1. 所有的 Java 类中都会至少存在一个构造方法
2. 如果一个类中没有明确的编写构造方法, 则编译器会自动生成一个无参的构造方法, 构造方法中没有任何的代码！
3. 如果自行编写了任意一个构造器, 则编译器不会再自动生成无参的构造方法
4. **子类的构造方法在被调用时,会默认调用父类的无参构造函数;**

```java
public class Person {
  public Person(){
    // 子类构造函数第一行默认调用了父类的构造函数
    super();
  }
}
```

## 封装

通过 private 关键字控制成员属性和方法,只用通过特定的方法才能访问这些属性;
常用的操作:

```java
public class Person{
  private String name;
  public void setName(String name){
    this.name = name;
  }
  public String getName(){
    return name;
  }
}
```

## 继承

特点：

1. 单继承
2. 子类拥有父类的 `public`,`protected` 方法和属性
3. 子类可以**重写**父类方法
4. 通过 `this` 关键字调用成员方法和属性,常用于成员方法的参数与成员属性名冲突;
5. 通过 `super` 访问父类的方法和属性,包括被子类重写的方法和属性;
6. 通过 `final` 修饰类和方法,防止子类继承和重写;

```java
class 类名 extends 父类 {
  类名(){
    //子类的构造方法第一行默认调用了 super()
    super()
  }
}
class Person{}
class Student extends Person{}
Person p = new Person();
Student s = new Student();
// 向上转型
Person p = new Student();
// 向下转型
Person p1 = new Student(); // upcasting, ok
Person p2 = new Person();
Student s1 = (Student) p1; // ok
Student s2 = (Student) p2; // runtime error! ClassCastException!
// 为了避免向下转型出错，Java提供了instanceof操作符，可以先判断一个实例究竟是不是某种类型：
Person p = new Student();
if (p instanceof Student) {
    // 只有判断成功才会向下转型:
    Student s = (Student) p; // 一定会成功
}
```

## 重载

方法的重载 ,可以让我们在不同的需求下, 通过传递不同的参数调用方法来完成具体的功能。

1. **方法名称**相同, **参数类型**或**参数个数**不同
2. **方法的重载与返回值无关!**

## 重写

1. 重写是方法的重写，与属性无关
2. 前提：继承，子类重写父类的方法
3. 返回值、方法名、参数表相同
4. 修饰符：范围可以扩大，但不可以缩小 `public` > `protected` >`default` > `private`
5. `@Override`

需要重写的原因：

1. 父类的功能子类不需要，或者不一定满足

## final

继承可以允许子类覆写父类的方法。如果一个父类不允许子类对它的某个方法进行覆写，可以把该方法标记为 final。

1. 标记方法: **用 final 修饰的方法不能被子类重写**
2. 标记类: **用 final 修饰的类不能被继承**
3. 标记属性:**在初始化后不能被修改**,可以在构造方法中初始化 final 字段

## 抽象类 abstract

抽象类必须使用`abstract class`声明,**一个抽象类中可以没有抽象方法,但是抽象方法必须写在抽象类或者接口中。**
格式：

```java
abstract class 类名{ // 抽象类
  public abstract void 方法名() ; // 抽象方法，只声明而未实现
}
```

**在抽象类的使用中有几个原则：**

· 抽象类本身是不能直接进行实例化操作的，即：不能直接使用关键字 new 完成。

· 一个抽象类必须被子类所继承，被继承的子类（如果不是抽象类）则必须覆写(重写)抽象类中的全部抽象方法。

**1、 抽象类能否使用 final 声明？**

​ 不能，因为 final 属修饰的类是不能有子类的 ， 而抽象类必须有子类才有意义，所以不能。

**2、 抽象类能否有构造方法？**

```
能有构造方法，而且子类对象实例化的时候的流程与普通类的继承是一样的，都是要先调用父类中的构造方法（默认是无参的），之后再调用子类自己的构造方法。
```

**抽象类与普通类的区别：**

```
1、抽象类必须用public或procted 修饰(如果为private修饰，那么子类则无法继承，也就无法实现其 抽象方法) 默认缺省为 public

2、抽象类不可以使用new关键字创建对象， 但是在子类创建对象时， 抽象父类也会被JVM实例化。

3、如果一个子类继承抽象类，那么必须实现其所有的抽象方法。如果有未实现的抽象方法，那么子类也必须定义为 abstract类
```

## 内部类

在 Java 中，可以将一个类定义在另一个类里面或者一个方法里面，这样的类称为内部类。 广泛意义上的内部类一般来说包括这四种：

​ 1、成员内部类

​ 2、局部内部类

​ 3、匿名内部类 （终点）

​ 4、静态内部类

### 成员内部类

```java
成员内部类是最普通的内部类，它的定义为位于另一个类的内部，形如下面的形式：
class Outer {
	private double x = 0;
	public Outer(double x) {
		this.x = x;
	}
	class Inner { //内部类
		public void say() {
			System.out.println("x="+x);
		}
	}
}
特点： 成员内部类可以无条件访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。
不过要注意的是，当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：
		外部类.this.成员变量
		外部类.this.成员方法
```

### 局部内部类

```
	局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。
	注意:局部内部类就像是方法里面的一个局部变量一样，是不能有public、protected、private以及static修饰符的。
```

### 匿名内部类

```java
匿名内部类由于没有名字，所以它的创建方式有点儿奇怪。创建格式如下：
new 父类构造器（参数列表）|实现接口（）
{
//匿名内部类的类体部分
}
在这里我们看到使用匿名内部类我们必须要继承一个父类或者实现一个接口，当然也仅能只继承一个父类或者实现一个接口。同时它也是没有class关键字，这是因为匿名内部类是直接使用new来生成一个对象的引用。当然这个引用是隐式的。

在使用匿名内部类的过程中，我们需要注意如下几点：
1、使用匿名内部类时，我们必须是继承一个类或者实现一个接口，但是两者不可兼得，同时也只能
继承一个类或者实现一个接口。
2、匿名内部类中是不能定义构造函数的。
3、匿名内部类中不能存在任何的静态成员变量和静态方法。
4、匿名内部类为局部内部类，所以局部内部类的所有限制同样对匿名内部类生效。
5、匿名内部类不能是抽象的，它必须要实现继承的类或者实现的接口的所有抽象方法。
6、只能访问final型的局部变量
```

### 静态内部类

```
静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。
静态内部类是不需要依赖于外部类对象的，这点和类的静态成员属性有点类似，并且它不能使用外部类的非static成员变量或者方法.
```

## 包

我们使用 `package` 来解决名字冲突。

Java 定义了一种名字空间，称之为包： `package` 。一个类总是属于某个包，类名（比如 Person）只是一个简写，真正的完整类名是`包名.类名`。在 Java 虚拟机执行的时候，JVM 只看完整类名，因此，只要包名不同，类就不同。

包可以是多层结构，用`.`隔开。例如：`java.util`。

要特别注意：**包没有父子关系**。java.util 和 java.util.zip 是不同的包，两者没有任何继承关系。

**没有定义包名的 class，它使用的是默认包**，非常容易引起名字冲突，因此，不推荐不写包名的做法。

我们还需要按照包结构把上面的 Java 文件组织起来。假设以 package_sample 作为根目录，src 作为源码目录，那么所有文件结构就是：

```
package_sample
└─ src
    ├─ hong
    │  └─ Person.java
    │  ming
    │  └─ Person.java
    └─ mr
       └─ jun
          └─ Arrays.java

即所有Java文件对应的目录层次要和包的层次一致。
```

**编译**: `javac -d ../bin ming/Person.java hong/Person.java mr/jun/Arrays.java`

使用其他包的方式:

1. 直接写完整类名 `mr.jun.Arrays arrays = new mr.jun.Arrays();`
2. `import`: `import mr.jun.Arrays; Arrays arrays = new Arrays();`;可以使用`*`，表示把这个包下面的所有 class 都导入进来
3. `import static`,可以导入一个类的静态字段和静态方法;`import static java.lang.System.*;out.println("Hello, world!");`

Java 编译器最终编译出的`.class` 文件只使用完整类名，因此，在代码中，当编译器遇到一个 class 名称时：

1. 如果是`完整类名`，就直接根据完整类名查找这个 class；
2. 如果是`简单类名`，按下面的顺序依次查找：
   1. 查找当前 `package` 是否存在这个 class；
   2. 查找 `import` 的包是否包含这个 class；
   3. 查找 `java.lang` 包是否包含这个 class。
      如果按照上面的规则还无法确定类名，则编译报错。

编写 class 的时候，编译器会自动帮我们做两个 import 动作：

1. 默认自动 import 当前 package 的其他 class；
2. 默认自动 import java.lang.\*。

## classpath 和 jar

classpath 是 JVM 用到的一个环境变量，它用来指示 JVM 如何搜索 class。
因为 Java 是编译型语言，源码文件是.java，而编译后的.class 文件才是真正可以被 JVM 执行的字节码。因此，JVM 需要知道，如果要加载一个 abc.xyz.Hello 的类，应该去哪搜索对应的 Hello.class 文件。
现在我们假设 classpath 是`.;C:\work\project1\bin;C:\shared`，当 JVM 在加载 `abc.xyz.Hello` 这个类时，会依次查找：

1. `<当前目录>\abc\xyz\Hello.class`
2. `C:\work\project1\bin\abc\xyz\Hello.class`
3. `C:\shared\abc\xyz\Hello.class`

classpath 的设定方法有两种：

1. 在系统环境变量中设置 classpath 环境变量，不推荐；
2. 在启动 JVM 时设置 classpath 变量，推荐。
   1. `java -classpath .;C:\work\project1\bin;C:\shared abc.xyz.Hello`;简写:`java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello`
   2. `java abc.xyz.Hello` 默认 classpath 为`.`即当前目录;

如果有很多`.class`文件，散落在各层目录中，肯定不便于管理。如果能把目录打一个包，变成一个文件，就方便多了。`jar`包就是用来干这个事的，它**可以把 package 组织的目录层级，以及各个目录下的所有文件（包括.class 文件和其他文件）都打成一个 jar 文件**，这样一来，无论是备份，还是发给客户，就简单多了。

jar 包实际上就是一个`zip`格式的压缩文件，而 jar 包相当于目录。如果我们要执行一个 jar 包的 class，就可以把 jar 包放到 classpath 中：`java -cp ./hello.jar abc.xyz.Hello`

制作 jar 包:将所有 class 文件添加到一个 zip 压缩文件,然后修改后缀名为`.jar`

jar 包还可以包含一个特殊的`/META-INF/MANIFEST.MF`文件，MANIFEST.MF 是纯文本，可以指定 `Main-Class` 和其它信息。JVM 会自动读取这个 MANIFEST.MF 文件，如果存在 Main-Class，我们就不必在命令行指定启动的类名，而是用更方便的命令：`java -jar hello.jar`

jar 包还可以包含其它 jar 包，这个时候，就需要**在 MANIFEST.MF 文件里配置 classpath**了。

在大型项目中，不可能手动编写 MANIFEST.MF 文件，再手动创建 zip 包。Java 社区提供了大量的开源构建工具，例如 Maven，可以非常方便地创建 jar 包。

# 接口 interface

**接口比抽象类更抽象**如果一个类中的全部方法都是抽象方法，全部属性都是全局常量，那么此时就可以将这个类定义成一个接口。
特点:

1. 方法全是抽象方法,属性都是静态常量
2. 多继承
3. 多实现
4. jdk1.8 `default` 方法,实现类可以不必覆写 default 方法。

```java

interface X接口名称 extends A,B{
	[public static final] 全局常量 ;
	[public abstract] 抽象方法 ;
  default void func(int x){}
}
class XImpl implements X,A,B,C,D{
  @Override
}
```

**接口与抽象类的区别**

1、抽象类要被子类继承，接口要被类实现。
2、接口只能声明抽象方法，抽象类中可以声明抽象方法，也可以写非抽象方法。
3、接口里定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量。
4、抽象类使用继承来使用，无法多继承。 接口使用实现来使用， 可以多实现
5、抽象类中可以包含 static 方法 ，但是接口中不允许（静态方法不能被子类重写，因此接口中不能声明静态方法）
6、接口不能有构造方法，但是抽象类可以有

## 函数式接口: 只有一个接口方法的接口

java 为我们提供了四个比较重要的函数式接口:

消费型接口 `Consumer<T>`: ` void accept(T t)`有参数，无返回值的抽象方法；
供给型接口 `Supplier <T>`: ` T get()` 无参有返回值的抽象方法；
断定型接口 `Predicate<T> `: `boolean test(T t)`:有参，但是返回值类型是固定的 boolean
函数型接口 `Function<T,R>`: ` R apply(T t)`有参有返回值的抽象方法；

## lambda 表达式 (jdk 8)

1. 函数式接口的匿名实现,可以使用 lambda 表达式;
2. lambda 表达式可以转换为匿名类
3. 写法 `(参数表)->{方法体}`
   1. `x->{}` 只有一个参数可以省略()
   2. `()->""` 方法体只有一个返回语句,可以省略{},return 和;号

```java
/**
只有一个接口方法
 */
@FunctionalInterface
interface Operation {
    int operation(int a, int b);
}

class Test {
    private int operate(int a, int b, Operation operation) {
        return operation.operation(a, b);
    }
}

Test test = new Test();

test.operate(1,2,(a,b)->a+b);
```

## 方法引用(jdk8)

通过方法引用，可以使用方法的名字来指向一个方法。使用一对冒号来引 `::` 用方法。

构造方法引用
使用方式：`Class::new`

静态方法引用
使用方式：`Class::staticMethod`

对象的实例方法引用
使用方式：`instance::method`

类的实例方法引用
使用方式：`Class::method`

作用: 指代一个实现函数式接口的匿名对象,要求除函数名可以不一致外,参数表和返回值类型要保持一致

```java
    @FunctionalInterface
    interface Operation {
        int operation(int a, int b);
    }
    @FunctionalInterface
    interface Operation2 {
        int operation(OperationFactory factory,int a, int b);
    }
    @FunctionalInterface
    interface Operation3 {
        OperationFactory operation();
    }
    static class OperationFactory{
        int add(int a,int b){
            return a+b;
        }
        //add, subtract, multiply and divide
        static int subtract(int a,int b){
            return a-b;
        }
        int multiply(int a,int b){
            return a*b;
        }
        int divide(int a,int b){
            return a/b;
        }

        @Override
        public String toString() {
            return "OperationFactory{}";
        }
    }
    @Test
    void methodReference(){
        // 对象的实例方法
        Operation operation1 = new OperationFactory()::add;
        System.out.println(operation1.operation(1,2));
        // 类的静态方法
        Operation operation2 = OperationFactory::subtract;
        System.out.println(operation2.operation(1,2));
        // 类的实例方法
        Operation2 operation3 = OperationFactory::multiply;
        System.out.println(operation3.operation(new OperationFactory(),2,2));
        // 类的构造方法
        Operation3 operation4 = OperationFactory::new;
        System.out.println(operation4.operation());
    }
```

## 默认方法和静态方法(jdk8)

```java
public interface TestInterface {
    String test();

    // 接口默认方法,类似于抽象类的非抽象方法
    default String defaultTest() {
        return "default";
    }
    // 接口静态方法
    static String staticTest() {
        return "static";
    }
}
```

## 私有方法(jdk9)

java 9 可以使用私有方法

# 泛型

​ 泛型，即“参数化类型”。就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定 义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

- 所有泛型方法声明都有一个类型参数声明部分（由尖括号分隔），该类型参数声明部分在方法返回类型之前。
- 每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。
- 类型参数能被用来声明返回值类型，并且能作为泛型方法得到的实际参数类型的占位符。
- 泛型方法体的声明和其他方法一样。注意**类型参数只能代表引用型类型，不能是原始类型**（像 int、double、char 等）。

java 中泛型常用标记符：

- E - Element (在集合中使用，因为集合中存放的是元素)
- T - Type（Java 类）
- K - Key（键）
- V - Value（值）
- N - Number（数值类型）
- ？ - 表示不确定的 java 类型

限制:

1. Java 使用擦拭法实现泛型，导致了：编译器把类型`<T>`视为 Object；编译器根据`<T>`实现安全的强制转型。
2. `<T>`不能是基本类型，例如 int，因为实际类型是 Object，Object 类型无法持有基本类型
3. 无法取得带泛型的 `Class`
4. 无法判断带泛型的 `Class`
5. 一个类可以继承自一个泛型类,在继承了泛型类型的情况下，子类可以获取父类的泛型类型。但是比较复杂

```java
Type genericSuperclass = GenericType.class.getGenericSuperclass();
if (genericSuperclass instanceof ParameterizedType) {
    ParameterizedType pt = (ParameterizedType) genericSuperclass;
    Type[] atas = pt.getActualTypeArguments(); // 可能有多个
    Class<?> ata = (Class<?>) atas[0];
    System.out.println(ata);
}
```

因为 Java 引入了泛型，所以，只用 Class 来标识类型已经不够了

```yaml
Type:
  - Class
  - ParameterizedType # 参数化类型
  - GenericArrayType # 数组类型，其组件类型是参数化类型或类型变量
  - WildcardType # 表示一个通配符型表达，如 ? ， ? extends Number ，或 ? super Integer 。
```

## 泛型类

```java
public class GenericType<T> {
    public T name;
    public static void main(String[] args) {
        GenericType<String> demo = new GenericType<>();
        demo.name = "name";
        System.out.println(demo.name);
    }
}

```

## 泛型方法

```java
public static class Object1{
    public <T> T get (T t){
        return t;
    }
}

System.out.println(new Object1().<String>get("x"));// <String>可省略
System.out.println(new Object1().<Integer>get(1));//<Integer>不能省略
```

## 泛型限制类型

何时使用 `extends` ，何时使用 super？为了便于记忆，我们可以用 PECS 原则：Producer Extends Consumer Super。

即：如果需要返回 T，它是生产者（Producer），要使用 extends 通配符；如果需要写入 T，它是消费者（Consumer），要使用 super 通配符。

```java
<? extends Parent> 指定了泛型类型的上届
<? super Child> 指定了泛型类型的下届
<?> 指定了没有限制的泛型类型,<?>通配符有一个独特的特点，就是：Pair<?>是所有Pair<T>的超类：
```

# 枚举

1. 定义的 `enum` 类型总是继承自 `java.lang.Enum`，且无法被继承；
2. 只能定义出 `enum` 的实例，而无法通过 `new` 操作符创建 `enum` 的实例；
3. 定义的每个实例都是引用类型的唯一实例；默认都被 `final、public, static` 修饰
4. 可以将 `enum` 类型用于 `switch` 语句。
5. 注意：枚举类的字段也可以是非 final 类型，即可以在运行期修改，但是不推荐这样做！

命名规范：
枚举名称，首字母大写，驼峰标识；其枚举值，全大写，下划线分割；
命名规范参考：java.lang.Character 的 UnicodeScript 枚举示例；

定义枚举类时，如果只有枚举值，则最后一个枚举值后可以没有逗号或分号； 如果有自定义方法，则最后一个枚举值与后续代码之间要用分号隔开，不能使用逗号或空格； 5. 枚举可以实现接口，代码参考：java.net.StandardProtocolFamily implements ProtocolFamily； 6. 比较枚举时，要使用 equal 方法，不能使用==方法比较，否则在分布式环境下会出错；（enum 的值是在运行期生成，在 cluster 环境下，每个虚拟机都会构造出一个同义的枚举对象。因此使用==方法比较时，其枚举值一定不相等，因为这不是同一个对象实例。 7. String 字符串转换为枚举

有些时候（历史问题、旧的接口等），会传输 String 类型，而不是枚举类，

需要内部将 String 转换为枚举，方法如下（以上文中的颜色为例）：

```java
public static String getDescOfColor(String colorStr) {
  try {
    Color entry = Enum.valueOf(Color.class, colorStr);
    return entry.getDesc();
  } catch (Exception e) {
    return "未知颜色["+colorStr+"]";
  }
}
```

```java
// 关键字 eum
public enum Color {
  // 枚举成员 x,y,z,...;
  RED,BLUE,GREEN,BLACK;
}



// 编译器编译出的class大概就像这样
public final class Color extends Enum { // 继承自Enum，标记为final class
    // 每个实例均为全局唯一:
    public static final Color RED = new Color();
    public static final Color GREEN = new Color();
    public static final Color BLUE = new Color();
    // private构造方法，确保外部无法调用new操作符:
    private Color() {}
}
```

优点:

1. enum 常量本身带有类型信息
2. 不可能引用到非枚举的值，因为无法通过编译
3. 不同类型的枚举不能互相比较或者赋值，因为类型不符。

使得编译器可以在编译期自动检查出所有可能的潜在错误。

## 比较

枚举类是引用类型,可以重重写`equals()`方法进行比较;另外,因为 enum 类型的每个常量在 JVM 中只有一个唯一实例，所以可以直接用`==`比较;

## 常用方法

```java
name() 返回常量名
String s = Weekday.SUN.name(); // "SUN"

toString()  默认情况下，对枚举常量调用toString()会返回和name()一样的字符串。但是，toString()可以被覆写，而name()则不行。
// 注意：判断枚举常量的名字，要始终使用name()方法，绝不能调用toString()！

values()	以数组形式返回枚举类型的所有成员

valueOf()	将普通字符串（枚举成员的字符串形式）转换为枚举实例

compareTo()	比较两个枚举成员在定义时的顺序

ordinal()	返回定义的常量的顺序，从0开始计数 //要编写健壮的代码，就不要依靠ordinal()的返回值。因为enum本身是class，所以我们可以定义private的构造方法，并且，给每个枚举常量添加字段
int n = Weekday.MON.ordinal(); // 1
```

## 为枚举添加方法

```java
enum WeekDay {
  // 枚举的成员，必须先定义，而且使用分号结束
  Mon("Monday"),Tue("Tuesday"),Wed("Wednesday"),Thu("Thursday"),Fri("Friday"),Sat("Saturday"),Sun("Sunday");
  // 属性
  private final String day;
  // 构造函数
  private WeekDay(String day) {
    this.day = day;
  }
  //
  public static void printDay(int i) {
          switch(i) {
              case 1:
                  System.out.println(WeekDay.Mon);
                  break;
              case 2:
                  System.out.println(WeekDay.Tue);
                  break;
              case 3:
                  System.out.println(WeekDay.Wed);
                  break;
              case 4:
                  System.out.println(WeekDay.Thu);
                  break;
              case 5:
                  System.out.println(WeekDay.Fri);
                  break;
              case 6:
                  System.out.println(WeekDay.Sat);
                  break;
              case 7:
                  System.out.println(WeekDay.Sun);
                  break;
              default:
                  System.out.println("wrong number!");
          }
  }

  // 枚举方法
  public String getDay() {
    return day;
  }
  @Override
  public String toString() {
    return this.day ;
  }
}
```

## EnumMap

EnumMap 是专门为枚举类型量身定做的 Map 实现，HashMap 只能接收同一枚举类型的实例作为键值，并且由于枚举类型实例的数量相对固定并且有限，所以 EnumMap 使用数组来存放与枚举类型对应的值，使得 EnumMap 的效率非常高。

## EnumSet

EnumSet 是枚举类型的高性能 Set 实现，它要求放入它的枚举常量必须属于同一枚举类型。

## 常用枚举工具类

```java
package com.epoint.shenhua.jsgcztbmis2.jiaoyisystem.service;

import java.util.EnumSet;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * 枚举通用接口
 *
 * @author lxiaohui
 * @version 2023年12月7日
 */
public interface ICommonEnumHandler
{
    String getValue();

    /**
     * 通过枚举的value值获取枚举常量
     *
     *  @param <E>
     *  @param value
     *  @param clazz
     *  @return
     */
    static <E extends Enum<E> & ICommonEnumHandler> E getEnmu(String value, Class<E> clazz) {
        EnumSet<E> all = EnumSet.allOf(clazz);
        return all.stream().filter(e -> value.equals(e.getValue())).findFirst().orElse(null);
    }
    /**
     * 打印枚举的所有值 [name1:value1,name2:value2,...]
     *
     *  @param <E>
     *  @param clazz 枚举类型
     *  @return
     */
    static <E extends Enum<E> & ICommonEnumHandler> String print(Class<E> clazz) {
        EnumSet<E> all = EnumSet.allOf(clazz);
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("[");
        all.stream().forEach(e->{
            stringBuilder.append(e.name());
            stringBuilder.append(":");
            stringBuilder.append(e.getValue());
            stringBuilder.append(",");
        });
        stringBuilder.append("]");
        return stringBuilder.toString().replace(",]", "]");
    }
    /**
     * 将枚举值转换为map,value->name
     *
     *  @param <E>
     *  @param clazz
     *  @return
     */
    static <E extends Enum<E> & ICommonEnumHandler> Map<String,String> toMap(Class<E> clazz) {
        return EnumSet.allOf(clazz).stream().collect(Collectors.toMap(n->n.getValue(),n->n.name()));
    }
}

```

使用:

1. 实现接口 ICommonEnumHandler
2. 调用方法

```java
public enum Fruit implements ICommonEnumHandler{
  苹果("apple");
  private String value;
  Fruit(String value){
    this.value = value;
  }
  public String getValue(){
    return this.value;
  }
}
Fruit x = ICommonEnumHandler.getEnmu("apple",Fruit.class);
x.equals(Fruit.苹果); //true
```

# 异常处理

异常是在程序中导致程序中断运行的一种指令流。

获知异常的方法:

1. 约定返回错误码,这种方式常见于底层 C 函数。
2. 在语言层面上提供一个异常处理机制,Java 异常类
3. `Throwable`
   1. `Exception` 运行时的错误，它可以被捕获并处理
      1. `RuntimeException` 运行时异常
         1. 0/1
         2. ClassNotFound
         3. NullPoint
         4. UnLownType
         5. 下标越界
         6. ...
      2. 非 RuntimeException
         1. IOException
         2. ReflectiveOperationException
      3. `printStackTrace()` 打印出方法的调用栈
   2. `Error`
      1. AWT 错误
      2. JVM 错误
         1. StackOverflowError 栈溢出
         2. OutOfMemoryError 内存溢出
         3. NoClassDefFoundError 无法加载某个 Class

## 捕获与处理

1. 在 Java 中，凡是可能抛出异常的语句，都可以用`try … catch`捕获。把可能发生异常的语句放在`try { … }`中，然后使用`catch`捕获对应的`Exception`及其子类。
2. JVM 在捕获到异常后，会从上到下匹配 `catch` 语句，匹配到某个 `catch` 后，执行 `catch` 代码块，然后不再继续匹配。**多个 catch 语句只有一个能被执行**。
3. 存在多个 `catch` 的时候，catch 的顺序非常重要：**子类必须写在前面**。
4. `finally` 总是最后执行;不是必须的，可写可不写;

非 RuntimeException 异常不进行抛出或捕获编译会报错;

```java
//如果要想对异常进行处理，则必须采用标准的处理格式，处理格式语法如下：
try{
// 有可能发生异常的代码段
}catch(异常类型1 对象名1){
// 异常的处理操作
}catch(异常类型2 对象名2){
// 异常的处理操作
}
//特殊的多异常捕获写法：
catch(异常类型1 | 异常类型2 对象名){
//表示此块用于处理异常类型1 和 异常类型2 的异常信息
}
...
/*
  多异常捕获的注意点：
		1、 捕获更粗的异常不能放在捕获更细的异常之前。
		2、 如果为了方便，则可以将所有的异常都使用Exception进行捕获。
*/
finally{
// 异常的统一出口，不管是否产生了异常，最终都要执行此段代码。
}

1、 一旦产生异常，则系统会自动产生一个异常类的实例化对象。
2、 那么，此时如果异常发生在try语句，则会自动找到匹配的catch语句执行，如果没有在try语句中，则会将异
常抛出.
3、 所有的catch根据方法的参数匹配异常类的实例化对象，如果匹配成功，则表示由此catch进行处理。
```

> 1. try-catch-finally 中哪个部分可以省略？
>
>    答： catch 和 finally 可以省略其中一个 ， catch 和 finally 不能同时省略 注意:格式上允许省略 catch 块, 但是发生异常时就不会捕获异常了,我们在开发中也不会这样去写代码
>
> 2. try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？
>
>    答：finally 中的代码会执行 详解： 执行流程：
>
>    1. 先计算返回值，并将返回值存储起来，等待返回
>
>    2. 执行 finally 代码块
>
>    3. 将之前存储的返回值，返回出去；
>
>       需注意：
>
>       1. 返回值是在 finally 运算之前就确定了，并且缓存了，不管 finally 对该值做任何的改变，返回的值都不会改变
>       2. finally 代码中不建议包含 return，因为程序会在上述的流程中提前退出，也就是说返回的值不是 try 或 catch 中的值
>       3. 如果在 try 或 catch 中停止了 JVM,则 finally 不会执行.例如停电- -, 或通过如下代码退出 JVM:System.exit(0)

## 抛出

1. 当某个方法抛出了异常时，如果当前方法没有捕获异常，异常就会被抛到上层调用方法，直到遇到某个`try … catch`被捕获为止
2. `throws` 用在方法上，用于抛出方法内部产生的异常给调用者
3. `throw` 在方法内部抛出异常
4. `finally` 抛出异常后，原来在 catch 中准备抛出的异常就“消失”了，因为只能抛出一个异常。没有被抛出的异常称为**被屏蔽的异常**（Suppressed Exception）。

```java
返回值 方法名称() throws Exception{
}
```

**throw** 人为制造一个异常

```java
throw关键字表示在程序中人为的抛出一个异常，因为从异常处理机制来看，所有的异常一旦产生之后，实际上抛出的就是一个异常类的实例化对象，那么此对象也可以由throw直接抛出。
代码： throw new Exception("抛着玩的。") ;
```

## 自定义异常类

- 编写一个类， 继承 Exception，并重写一参构造方法 即可完成自定义受检异常类型。
- 编写一个类， 继承 RuntimeExcepion，并重写一参构造方法 即可完成自定义运行时异常类型。

```java
class MyException extends Exception{ // 继承Exception，表示一个自定义异常类
	public MyException(String msg){
		super(msg) ; // 调用Exception中有一个参数的构造
	}
};

// 自定义异常可以做很多事情， 例如：
class MyException extends Exception{
	public MyException(String msg){
		super(msg) ;
		//在这里给维护人员发短信或邮件， 告知程序出现了BUG。
	}
};
```

## 使用断言

断言（Assertion）是一种调试程序的方式。

1. 在 Java 中，使用 `assert` 关键字来实现断言。
2. 如果计算结果为 false，则断言失败，抛出 `AssertionError`
3. 对于可恢复的程序错误，不应该使用断言。
4. JVM 开启断言:要执行 assert 语句，必须给 Java 虚拟机传递`-enableassertions`（可简写为`-ea`）参数启用断言。
   1. `$ java -ea Main.java`
   2. 还可以有选择地对特定地类启用断言，命令行参数是：`-ea:com.itranswarp.sample.Main`，表示只对`com.itranswarp.sample.Main`这个类启用断言。
   3. 对特定地包启用断言，命令行参数是：`-ea:com.itranswarp.sample…`（注意结尾有 3 个.），表示对 `com.itranswarp.sample` 这个包启动断言。
5. 实际开发中，很少使用断言。更好的方法是编写**单元测试**，

```java
// assert <断言条件>;
assert x >= 0;
// assert <断言条件>[:<可选的断言消息>];  断言失败的时候，AssertionError会带上消息x must >= 0，
assert x >= 0 : "x must >= 0";
```

## 异常堆栈信息转字符串

```java
    /**
     * 将异常堆栈信息转为string
     *
     * @param e
     * @return
     */
    private String printExceptionStackTrace(Exception e) {
        PrintWriter printWriter = null;
        try {
            StringWriter result = new StringWriter();
            printWriter = new PrintWriter(result);
            e.printStackTrace(printWriter);
            return result.getBuffer().toString();
        }
        catch (Exception e1) {
            logger.error(e1.getMessage(), e1);
            return e1.getMessage();
        }
        finally {
            if (printWriter != null) {
                printWriter.close();
            }
        }
    }
```

## 打印隐藏的堆栈信息

```java
    public String test() {
        Thread.setDefaultUncaughtExceptionHandler(InsuranceOrderController::uncaughtException);
        throw new Exception("");
    }
    /**
     * 打印所有错误信息堆栈,包含隐藏的...more
     *  [一句话功能简述]
     *  @param t
     *  @param e
     */
    private static void uncaughtException(Thread t, Throwable e) {
        StackTraceElement[] ses = e.getStackTrace();
        System.err.println("Exception in thread \"" + t.getName() + "\" " + e.toString());
        for (StackTraceElement se : ses) {
            System.err.println("\tat " + se);
        }
        Throwable ec = e.getCause();
        if (ec != null) {
            uncaughtException(t, ec);
        }
    }
```

## jdk logging

输出日志，而不是用 System.out.println()，有以下几个好处：

1. 可以**设置输出样式**，避免自己每次都写`"ERROR: " + var`；
2. 可以设置**输出级别**，禁止某些级别输出。例如，只输出错误日志；
3. 可以**被重定向到文件**，这样可以在程序运行结束后查看日志；
4. 可以**按包名控制日志级别**，只输出某些包打的日志；
5. 可以……总之就是好处很多啦。
6. Java 标准库内置了日志包 `java.util.logging`，我们可以直接用。

```java
Logger logger = Logger.getGlobal();
logger.info("信息");
logger.warning("警告");
logger.fine("忽略");
logger.severe("严重");

十月 11, 2022 3:16:46 下午 com.lxh.errorHandle.JdkLog main
信息: 信息
十月 11, 2022 3:16:46 下午 com.lxh.errorHandle.JdkLog main
警告: 警告
十月 11, 2022 3:16:46 下午 com.lxh.errorHandle.JdkLog main
严重: 严重
```

### 日志级别

JDK 的 Logging 定义了 7 个日志级别，从严重到普通：

- `SEVERE` 严重
- `WARNING` 警告
- `INFO` 信息 -- 默认
- `CONFIG` 配置
- `FINE` 好
- `FINER` 很好
- `FINEST` 最好

### 局限

1. Logging 系统在 JVM 启动时读取配置文件并完成初始化，一旦开始运行 main()方法，就无法修改配置；
2. 配置不太方便，需要在 JVM 启动时传递参数`-Djava.util.logging.config.file=<config-file-name>`

# 反射

反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。

1.  Class newInstance()
2.  类加载机制
3.  Method invoke(user3,"xxx")
4.  Field set(xx,"xx")
5.  Construct
    1. newInstance()
    2. 获取的时候需要，传递参数的 Class 类型
6.  破坏私有关键字
    1. setAccessible(true)
7.  性能分析 正常>检测关闭的反射>默认的反射
8.  反射获取注解、泛型

**编译期**是指把源码交给编译器编译成计算机可以执行的文件的过程。在 Java 中也就是把 Java 代码编成 class 文件的过程。编译期只是做了一些翻译功能，并没有把代码放在内存中运行起来，而只是把代码当成文本进行操作，比如检查错误。

**运行期**是把编译后的文件交给计算机执行，直到程序运行结束。所谓运行期就把在磁盘中的代码放到内存中执行起来。

**Java 反射机制**是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为 Java 语言的反射机制。简单来说，反射机制指的是程序在运行时能够获取自身的信息。在 Java 中，只要给定类的名字，就可以通过反射机制来获得类的所有信息。

Java 反射机制主要提供了以下功能，这些功能都位于`java.lang.reflect`包。

- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时判断任意一个类所具有的成员变量和方法。
- 在运行时调用任意一个对象的方法。
- 生成动态代理。

要想知道一个类的属性和方法，必须先获取到该类的字节码文件对象。获取类的信息时，使用的就是 Class 类中的方法。所以先要获取到每一个字节码文件（.class）对应的 Class 类型的对象.

1. `class`由 JVM 在执行过程中动态加载的。JVM 在第一次读取到一种 class 类型时，将其加载进内存
2. 每加载一种 `class` ，JVM 就为其创建一个 `Class` 类型的实例，并关联起来
3. 一个 `Class` 实例包含了该 `class` 的所有完整信息
4. 通过 `Class` 实例获取 `class` 信息的方法称为**反射（Reflection）**

众所周知，所有 Java 类均继承了 Object 类，在 Object 类中定义了一个 getClass() 方法，该方法返回同一个类型为 Class 的对象。例如，下面的示例代码：
`Class labelCls = label1.getClass(); // label1为 JLabel 类的对象`

java.lang.reflect 包提供了反射中用到类，主要的类说明如下：

1. Constructor 类：提供类的构造方法信息。
2. Field 类：提供类或接口中成员变量信息。
3. Method 类：提供类或接口成员方法信息。
4. Array 类：提供了动态创建和访问 Java 数组的方法。
5. Modifier 类：提供类和成员访问修饰符信息。

## JVM 动态加载 class

JVM 在执行 Java 程序的时候，并**不是一次性把所有用到的 class 全部加载到内存，而是第一次需要用到 class 时才加载**。

```java
/*
Commons Logging总是优先使用Log4j，只有当Log4j不存在时，才使用JDK的logging。利用JVM动态加载特性，大致的实现代码如下
*/

// Commons Logging优先使用Log4j:
LogFactory factory = null;
if (isClassPresent("org.apache.logging.log4j.Logger")) {
    factory = createLog4j();
} else {
    factory = createJdkLog();
}
boolean isClassPresent(String name) {
    try {
        Class.forName(name);
        return true;
    } catch (Exception e) {
        return false;
    }
}

```

## 获取类实例

1. 获取类实例
   1. 通过静态属性 `Object.class`
   2. 通过继承的 Object 类方法`object.getClass()`
   3. 通过类的全限定名`Class.forName("全限定名称")`
2. 类实例唯一,可以使用`==`比较
3. 在 Class 类可以获取类的基本信息
   1. 类名
   2. 包名
   3. 访问权限
   4. ......

```java
package com.lxh.reflectDemo;

public class ClassHandle {
    public static void main(String[] args) throws ClassNotFoundException {
        // 1. 静态属性
        Class class1 = ClassHandle.class;
        // 2. Object 类的成员方法 getClass()
        Class class2 = new ClassHandle().getClass();
        // 3. 类全限定名
        Class class3 = Class.forName("com.lxh.reflectDemo.ClassHandle");
        System.out.println(class1);
        System.out.println(class2);
        System.out.println(class3);
        // 可以用`==`比较两个 Class 实例
        System.out.println("class1 == class2:" + (class1 == class2) + "\tclass1 == class3:" + (class1 == class3));
        /*
        class com.lxh.reflectDemo.ClassHandle
        class com.lxh.reflectDemo.ClassHandle
        class com.lxh.reflectDemo.ClassHandle
        class1 == class2:true	class1 == class3:true
         */
    }
}

```

Class 实例在 **JVM 中是唯一的**，所以，上述方法获取的 Class 实例是同一个实例。**可以用`==`比较两个 Class 实例**

注意到**数组**（例如`String[]`）也是一种 Class，而且不同于 String.class，它的类名是`[Ljava.lang.String`。

此外，JVM 为每一种**基本类型**如 int 也创建了 Class，通过`int.class`访问。

## 创建类对象

```java
// Class 类
// 创建实例
newInstance();// 局限是：只能调用public的无参数构造方法
```

```java
public static void main(String[] args) {
    // 1. 获取类实例
    Class<Demo> demoClass = Demo.class;
    // 2.
    Demo demo;
    try {
        demo = demoClass.newInstance();
        System.out.println(demo); // Demo{name='null', age=0}
    } catch (InstantiationException | IllegalAccessException e) {
        e.printStackTrace();
    }
}
```

## 字段

1. 获取 `Class` 实例
2. 通过 `Class` 实例获取字段 `Field` 实例

### 获取字段实例

```java
// Class 类
Field getField(name)：根据字段名获取某个public的field（包括父类）
Field getDeclaredField(name)：根据字段名获取当前类的某个field（不包括父类）
Field[] getFields()：获取所有public的field（包括父类）
Field[] getDeclaredFields()：获取当前类的所有field（不包括父类）
```

例子:

```java
Class<Demo> demoClass = Demo.class;
// 获取 public 属性
Field[] fields = demoClass.getFields(); // [public int com.lxh.reflectDemo.Demo.age]
System.out.println(Arrays.toString(fields));
// 获取所有声明的属性
Field[] declaredFields = demoClass.getDeclaredFields();
System.out.println(Arrays.toString(declaredFields));
/*[
private java.lang.String com.lxh.reflectDemo.Demo.name,
public int com.lxh.reflectDemo.Demo.age,
int com.lxh.reflectDemo.Demo.height,
protected int com.lxh.reflectDemo.Demo.weight
]*/
// 获取单个 public 属性
Field age = demoClass.getField("age");
System.out.println(age); //public int com.lxh.reflectDemo.Demo.age
// 获取单个 private 属性
Field name = demoClass.getDeclaredField("name");
System.out.println(name); // private java.lang.String com.lxh.reflectDemo.Demo.name
```

### 获取字段信息

```java
getName()：返回字段名称，例如，"name"；
getType()：返回字段类型，也是一个Class实例，例如，String.class；
getModifiers()：返回字段的修饰符，它是一个int，不同的bit表示不同的含义。
```

例子:

```java
Field f = String.class.getDeclaredField("value");
f.getName(); // "value"
f.getType(); // class [B 表示byte[]类型
int m = f.getModifiers();
Modifier.isFinal(m); // true
Modifier.isPublic(m); // false
Modifier.isProtected(m); // false
Modifier.isPrivate(m); // true
Modifier.isStatic(m); // false
```

### 获取字段值

```java
field.get(Object) // 获取指定实例的指定字段的值
field.setAccessible(true)  // 允许访问 private
```

例子:

```java
        Demo xx = new Demo("xx", 1, 2, 3);
        Field age = Demo.class.getField("age");
        System.out.println("public age = " + age.get(xx));
        Field name = Demo.class.getDeclaredField("name");
        name.setAccessible(true); // 允许访问 private
        System.out.println("private name = " + name.get(xx));

// public age = 1
// private name = xx
```

如果使用反射可以获取 private 字段的值，那么类的封装还有什么意义？

1. 正常情况下，我们总是通过 p.name 来访问 Person 的 name 字段，编译器会根据 public、protected 和 private 决定是否允许访问字段，这样就达到了数据封装的目的。
2. 而反射是一种非常规的用法，使用反射，首先代码非常繁琐，其次，它更多地是给工具或者底层框架来使用，目的是在不知道目标实例任何信息的情况下，获取特定字段的值。
3. 此外，`setAccessible(true)`可能会失败。如果 JVM 运行期存在 `SecurityManager` ，那么它会根据规则进行检查，有可能阻止 `setAccessible(true)`。例如，某个 `SecurityManager` 可能不允许对 `java` 和 `javax` 开头的 `package` 的类调用 `setAccessible(true)`，这样可以保证 JVM 核心库的安全。

### 设置字段值

设置字段值是通过`Field.set(Object, Object)`实现的，其中第一个 Object 参数是指定的实例，第二个 Object 参数是待修改的值。

修改`非 public` 字段，需要首先调用 `setAccessible(true)`

## 方法

### 成员方法

我们已经能通过 Class 实例获取所有 Field 对象，同样的，可以通过 Class 实例获取所有 `Method` 信息。Class 类提供了以下几个方法来获取 Method：

```Java
Method getMethod(name, Class…)：获取某个public的Method（包括父类）
Method getDeclaredMethod(name, Class…)：获取当前类的某个Method（不包括父类）
Method[] getMethods()：获取所有public的Method（包括父类）
Method[] getDeclaredMethods()：获取当前类的所有Method（不包括父类）

** 方法的信息 **

getName()：返回方法名称，例如："getScore"；
getReturnType()：返回方法返回值类型，也是一个Class实例，例如：String.class；
getParameterTypes()：返回方法的参数类型，是一个Class数组，例如：{String.class, int.class}；
getModifiers()：返回方法的修饰符，它是一个int，不同的bit表示不同的含义。

Object invoke​(Object obj, Object... args)  // 调用 obj 对象的方法
method.invoke(null,Object... args) // 调用 obj 对象的静态方法
```

### 构造方法

`Constructor`

```Java
Class.newInstance() //局限是，它只能调用该类的public无参数构造方法。

// 调用Person的无参构造方法newInstance()
Person p = Person.class.newInstance();

通过Class实例获取 Constructor 的方法如下：

getConstructor(Class…)：获取某个public的Constructor；
getDeclaredConstructor(Class…)：获取某个Constructor；
getConstructors()：获取所有public的Constructor；
getDeclaredConstructors()：获取所有Constructor。
```

注意 `Constructor` **总是当前类定义的构造方法，和父类无关，因此不存在多态的问题**。
调用非 public 的 Constructor 时，必须首先通过 `setAccessible(true)`设置允许访问。**setAccessible(true)可能会失败**。

## 继承与实现关系

```java
class.getSuperclass();// 获取当前类的父类
class.getInterfaces();// 获取当前类实现的接口,只返回当前类直接实现的接口类型，并不包括其父类实现的接口类型;如果一个类没有实现任何interface，那么getInterfaces()返回空数组。
// 对所有 interface 的 Class 调用 getSuperclass() 返回的是 null ，获取接口的父接口要用 getInterfaces()

//判断一个实例是否是某个类型时，正常情况下，使用instanceof操作符
Object n = Integer.valueOf(123);
boolean isDouble = n instanceof Double; // false
boolean isInteger = n instanceof Integer; // true
boolean isNumber = n instanceof Number; // true
boolean isSerializable = n instanceof java.io.Serializable; // true

//如果是两个Class实例，要判断一个向上转型是否成立，可以调用isAssignableFrom()
// Integer i = ?
Integer.class.isAssignableFrom(Integer.class); // true，因为Integer可以赋值给Integer
// Number n = ?
Number.class.isAssignableFrom(Integer.class); // true，因为Integer可以赋值给Number
// Object o = ?
Object.class.isAssignableFrom(Integer.class); // true，因为Integer可以赋值给Object
// Integer i = ?
Integer.class.isAssignableFrom(Number.class); // false，因为Number不能赋值给Integer
```

## 动态代理(接口)

有没有可能不编写实现类，直接在运行期创建某个 `interface` 的实例呢？

这是可能的，因为 Java 标准库提供了一种动态代理（`Dynamic Proxy`）的机制：可以在运行期动态创建某个 `interface` 的实例。

1. 定义接口
2. 定义一个 `InvocationHandler` 实例，它负责实现接口的方法调用
3. 通过 `Proxy.newProxyInstance()`创建 `interface` 实例，它需要 3 个参数
   1. 使用的 `ClassLoader` ，通常就是接口类的 ClassLoader；
   2. 需要实现的`接口数组`，至少需要传入一个接口进去；
   3. 用来处理接口方法调用的 `InvocationHandler` 实例。
4. 将返回的 Object `强制转型`为接口。

动态代理实际上是 JDK 在运行期动态创建 class 字节码并加载的过程，它并没有什么黑魔法

正常接口的使用:

```java
// 接口
public interface Hello {
    void morning(String name);
}
// 实现类
public class HelloWorld implements Hello {
    public void morning(String name) {
        System.out.println("Good morning, " + name);
    }
}
// 创建接口实例
Hello hello = new HelloWorld();
hello.morning("Bob");
```

动态代理: 仍然先定义了接口 Hello，但是我们并不去编写实现类，而是直接通过 JDK 提供的一个 `Proxy.newProxyInstance()`创建了一个 Hello 接口对象。

```java
public class InterfaceProxy {
    public static void main(String[] args) {
        Hello hello = (Hello) Proxy.newProxyInstance(
                Hello.class.getClassLoader(), // 传入 Hello 接口的类加载器
                new Class[]{Hello.class}, // 传入要实现的接口
                new InvocationHandler() { // 实现方法
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println(method);
                        if (method.getName().equals("hello")) { // 实现接口方法 hello
                            System.out.println("hello , " + args[0]);
                        }
                        return null;
                    }
                }
        );
        hello.hello("interface proxy !");
    }
}

interface Hello {
    void hello(String obj);
}
```

## 内部类

```java
内部类	    getClasses()	        Class 型数组	          获取所有权限为 public 的内部类
            getDeclaredClasses()	Class 型数组	        获取所有内部类
内部类的声明类	getDeclaringClass()	Class 对象	        如果该类为内部类，则返回它的成员类，否则返回 null

```

## 注解

```java
class.getAnnotation() // 获取该类的注解
```

# 注解

注释会被编译器直接忽略，注解则可以被编译器打包进入 class 文件，因此，注解是一种用作标注的“元数据”。

1. 开始版本: Java 5;
2. 注解: 代码里的特殊标记，这些标记可以在编译、类加载和运行时被读取，并执行相应的处理;
3. 以 `@` 符号开头;
4. 本质: 接口
5. 5 个基本注解 (Java 8 )
6. 6 个元注解 (Java 8 )
7. 注解定义后也是一种 class，所有的注解都继承自`java.lang.annotation.Annotation`

注解常见的作用有以下几种：

- 生成帮助文档。这是最常见的，也是 Java 最早提供的注解。常用的有 `@see`、`@param` 和 `@return` 等；
- 跟踪代码依赖性，实现替代配置文件功能。比较常见的是 Spring 2.5 开始的基于注解配置。作用就是减少配置。现在的框架基本都使用了这种配置来减少配置文件的数量；
- 在编译时进行格式检查。如把 `@Override` 注解放在方法前，如果这个方法并不是重写了父类方法，则编译时就能检查出。

### 基本注解

```java
// 指定方法重写的，只能修饰方法并且只能用于方法重写，它可以强制一个子类必须重写父类方法或者实现接口的方法。
@Override
// 可以用来注解类、接口、成员方法和成员变量等，用于表示某个元素（类、方法等）已过时。当其他程序使用已过时的元素时，编译器将会给出警告。
/*
Java 9 为 @Deprecated 注解增加了以下两个属性：
forRemoval：该 boolean 类型的属性指定该 API 在将来是否会被删除。
since：该 String 类型的属性指定该 API 从哪个版本被标记为过时。
*/
@Deprecated
// 指示被该注解修饰的程序元素（以及该程序元素中的所有子元素）取消显示指定的编译器警告，且会一直作用于该程序元素的所有子元素
@SuppressWarnings
// 注解抑制编译器警告 相当于@SuppressWarnings("unchecked")
@SafeVarargs
// 只是告诉编译器检查这个接口，保证该接口只能包含一个抽象方法，否则就会编译出错。Lambda
@FunctionalInterface
```

### 元注解

元注解是负责对其它注解进行说明的注解，自定义注解时可以使用元注解。

Java 5 定义了 4 个注解，分别是 `@Documented`、`@Target`、`@Retention` 和 `@Inherited`。

Java 8 又增加了 `@Repeatable` 和 `@Native` 两个注解。

这些注解都可以在 `java.lang.annotation` 包中找到。

```java
/*
是一个标记注解，没有成员变量。用 @Documented 注解修饰的注解类会被 JavaDoc 工具提取成文档。默认情况下，JavaDoc 是不包括注解的，但如果声明注解时指定了 @Documented，就会被 JavaDoc 之类的工具处理，所以注解类型信息就会被包括在生成的帮助文档中。
*/
@Documented
/**
注解用来指定一个注解的使用范围，即被 @Target 修饰的注解可以用在什么地方。@Target 注解有一个成员变量（value）用来设置适用目标，value 是 `java.lang.annotation.ElementType` 枚举类型的数组

  类或接口：ElementType.TYPE；
  字段：ElementType.FIELD；
  方法：ElementType.METHOD；
  构造方法：ElementType.CONSTRUCTOR；
  方法参数：ElementType.PARAMETER。
*/
@Target
/**
用于描述注解的生命周期，也就是该注解被保留的时间长短。@Retention 注解中的成员变量（value）用来设置保留策略，value 是 `java.lang.annotation.RetentionPolicy` 枚举类型，RetentionPolicy 有 3 个枚举常量，如下所示。
  - SOURCE ：在源文件中有效（即源文件保留）
  - CLASS ：在 class 文件中有效（即 class 保留） //默认
  - RUNTIME ：在运行时有效（即运行时保留）
*/
@Retention
/**
是一个标记注解，用来指定该注解可以被继承。使用 @Inherited 注解的 Class 类，表示这个注解可以被用于该 Class 类的子类。就是说如果某个类使用了被 @Inherited 修饰的注解，则其子类将自动具有该注解。
 */
@Inherited
/**
注解是 Java 8 新增加的，它允许在相同的程序元素中重复注解，在需要对同一种注解多次使用时，往往需要借助 @Repeatable 注解。
 */
@Repeatable
/**
注解修饰成员变量，则表示这个变量可以被本地代码引用，常常被代码生成工具使用。
 */
@Native
```

### 自定义注解

1. `@interface` 定义
2. 元注解修饰,一般是 `@Target` `@Retention`
3. 定义属性:
   1. 其实是接口方法,返回值为 所有基本类型,String,枚举,以及以上类型的数组
   2. 配置参数必须是**常量**
   3. 默认值 `default`
   4. `value` 的配置参数,使用时可以只写常量 @xx(1)

```java
@Target({ElementType.TYPE}) // 元注解: 标明该注解可以用在哪里: 类\方法\属性\参数\构造方法\接口
@Retention(RetentionPolicy.RUNTIME)
public @interface DefineAnnotation {
    int type() default 0;

    String level() default "info";

    String value() default "";
}

```

### 注解的使用

通过反射获取注解，并依据注解设置的值对注解对象进行操作

```java
class.isAnnotationPresent(annotation.class) // 类上是否存在该注解
field.isAnnotationPresent(annotation.class) // 字段上是否存在该注解
method.isAnnotationPresent(annotation.class) // 方法上是否存在该注解
constructor.isAnnotationPresent(annotation.class) // 构造方法上是否存在该注解

// 获取注解,如果Annotation不存在，将返回null
Class.getAnnotation(Class)
Field.getAnnotation(Class)
Method.getAnnotation(Class)
Constructor.getAnnotation(Class)

//读取方法参数的Annotation
public void hello(@NotNull @Range(max=5) String name, @NotNull String prefix) {}
// 获取Method实例:
Method m = ...
// 获取所有参数的Annotation:
Annotation[][] annos = m.getParameterAnnotations();
// 第一个参数（索引为0）的所有Annotation:
Annotation[] annosOfName = annos[0];
for (Annotation anno : annosOfName) {
    if (anno instanceof Range) { // @Range注解
        Range r = (Range) anno;
    }
    if (anno instanceof NotNull) { // @NotNull注解
        NotNull n = (NotNull) anno;
    }
}
```

**重复注解**

Java 8 支持了重复注解。在 Java 8 之前想实现重复注解，需要用一些方法来绕过限制。

**类型注解**

Java 8 之前注解只能用在声明中，在 Java 8 中，注解可以使用在 任何地方。

# 多线程

- **进程** 是指一个内存中运行的应用程序，每个进程都有一个独立的内存空间
- **线程** 是进程中的一个执行路径，共享一个内存空间，线程之间可以自由切换，并发执行.
- **分时调度** 所有线程轮流使用 CPU 的使用权，平均分配每个线程占用 CPU 的时间。
- **抢占式调度** 优先让优先级高的线程使用 CPU，如果线程的优先级相同，那么会随机选择一个(线程随机性)， Java 使用的为抢占式调度。
- **同步** 排队执行 , 效率低但是安全
- **异步** 同时执行 , 效率高但是数据不安全
- **并发** 指两个或多个事件在同一个时间段内发生
- **并行** 指两个或多个事件在同一时刻发生（同时发生）

**同一个应用程序**，既可以有多个进程，也可以有多个线程，因此，实现多任务的方法，有以下几种:

1. 多进程模式（每个进程只有一个线程）
2. 多线程模式（一个进程有多个线程）
3. 多进程＋多线程模式（复杂度最高）

和多线程相比，多进程的**缺点**在于: 1. 创建进程比创建线程开销大，尤其是在 Windows 系统上；2. 进程间通信比线程间通信要慢，因为线程间通信就是读写同一个变量，速度很快。

而多进程的**优点**在于: 多进程稳定性比多线程高，因为在多进程的情况下，一个进程崩溃不会影响其他进程，而在多线程的情况下，任何一个线程崩溃会直接导致整个进程崩溃。

java 程序中基本的线程:

1. main 主线程
2. gc 垃圾回收器线程

### 线程创建 1\_继承 Thread 类

Thread: 实现了 Runnable 接口,底层使用静态代理模式用 Thread 代理了 Runnable

1. 继承 `Thread` 类,
2. 重写 `run()` 方法,
3. 调用 `start()` 开启线程,线程开启后由 CPU 调度执行

```java
@Test
public void createByThread() throws InterruptedException {
    class MyThread extends Thread {
        @Override
        public void run() {
            for (int i = 0; i < 100; i++) {
                System.out.println("MyThread-" + getName() + ": " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    new MyThread().start();
    new MyThread().start();
    Thread.sleep(20000);
}
```

### 线程创建 2\_实现 Runnable 接口

实现 Runnable 接口

1. 实现 Runnable 接口
2. 重写 run() 方法
3. 使用 Thread 接收 Runnable 对象
4. 调用 start() 启用线程

```java
@Test
public void createByRunnable() {
    Thread thread = new Thread(() -> {
        System.out.println("lambda 实现 Runnable 接口");
    });
    thread.start();
    System.out.println("主线程");
}
```

### 线程创建 3\_实现 Callable 接口,有返回值,需要结合线程池

1. 优点:
   1. 可以得到返回值
   2. 可以抛出异常
2. 缺点
   1. 实现较为复杂,需要线程池
3. 实现步骤:
   1. 实现 Callable 接口,需要返回值类型
   2. 重写 call() 方法,需要抛出异常
   3. 创建目标对象
   4. 创建执行服务 `ExecutoService ser = Executors.newFixedThreadPool(1)`
   5. 提交执行 `Future<Boolean> result1 = ser.submit(t1);`
   6. 获取结果 `boolean r1 = result1.get()`
   7. 关闭服务 `ser.shutdownNow()`

```java
public class MyCallable implements Callable<T>{
   @Override
   public T call(){
      //线程体
      return T;
   }

   psvm(){
      new Thread(new MyRunnable()).start();
   }
}
```

### 线程状态

1. `New`: 新创建的线程，尚未执行 `new Thread()`；
2. `Runnable`: 运行中的线程，正在执行 `run()`方法的 Java 代码；
3. `Blocked`: 运行中的线程，因为某些操作被阻塞而挂起；
4. `Waiting`: 运行中的线程，因为某些操作在等待中；`join()`
5. `Timed Waiting`: 运行中的线程，因为执行 sleep()方法正在计时等待；
6. `Terminated`: 线程已终止，因为 run()方法执行完毕。 `interrupt()`

当线程启动后，它可以在 Runnable、Blocked、Waiting 和 Timed Waiting 这几个状态之间切换，直到最后变成 Terminated 状态，线程终止。

### 守护线程

守护线程是指为其他线程服务的线程。在 JVM 中，所有非守护线程都执行完毕后，无论有没有守护线程，虚拟机都会自动退出。

如何创建守护线程呢？方法和普通线程一样，只是在调用 start()方法前，调用 setDaemon(true)把该线程标记为守护线程:

```java
Thread t = new MyThread();
t.setDaemon(true);
t.start();
```

在守护线程中，编写代码要注意: 守护线程不能持有任何需要关闭的资源，例如打开文件等，因为虚拟机退出时，守护线程没有任何机会来关闭文件，这会导致数据丢失。

### volatile 及时同步共享变量

volatile 关键字解决的是可见性问题: 当一个线程修改了某个共享变量的值，其他线程能够立刻看到修改后的值。

```java
volatile int x;
```

volatile 关键字的目的是告诉虚拟机:

1. 每次访问变量时，总是获取主内存的最新值；
2. 每次修改变量后，立刻回写到主内存。

### synchronized 线程同步

**synchronized 同步代码块**

执行同步代码块的逻辑:

1. 获取锁对象,检查此时锁对象是否正在使用
   1. 正在使用,则等待锁对象释放
   2. 空闲,则加锁,锁对象被占用,代码块执行完毕,释放锁对象
2. 注意加锁对象必须是同一个实例；

```java
synchronized(object){
   // 同步代码块
   // 同步监视器: object
}
```

**原子操作**

1. 原子操作,不可分割的操作
2. 基本类型（long 和 double 除外）赋值，例如：int n = m；
3. 引用类型赋值，例如：List<String> list = anotherList。
4. long 和 double 是 64 位数据，JVM 没有明确规定 64 位赋值操作是不是一个原子操作，不过在 x64 平台的 JVM 是把 long 和 double 的赋值作为原子操作实现的。

```Java
// 原子操作相当于:
synchronized(object){
   int a = 1;
}
synchronized(object){
   Object  a = new Object();
}
```

**synchronized 同步方法**

用 `synchronized` 修饰方法可以把整个方法变为同步代码块, `synchronized` 方法加锁对象是 `this`

```java
synchronized void method(){

}
// 相当于
void method(){
  synchronized(this){

  }
}
// 静态
public synchronized static void test(int n) {
    ...
}
//相当于
public static void test(int n) {
    synchronized(Counter.class) {
        ...
    }
}
```

线程安全类:

1. StringBuffer
2. 所有成员变量都是 `final` ,只能读不能写,比如 String，Integer，LocalDate
3. 只提供静态方法，没有成员变量的类: Math

**可重入锁**

1. **可重入锁**: 能被*同一个线程*反复获取的锁, Java 的线程锁是可重入的锁。
2. 由于 Java 的线程锁是可重入锁，所以，获取锁的时候，不但要*判断是否是第一次获取，还要记录这是第几次获取*。每获取一次锁，记录+1，每退出 `synchronized` 块，记录-1，减到 0 的时候，才会真正释放锁。

```java
public class Counter {
    private int count = 0;
    public synchronized void add(int n) {
        if (n < 0) {
            dec(-n);
        } else {
            count += n;
        }
    }
    public synchronized void dec(int n) {
        count += n;
    }
}
```

**死锁**

1. **死锁**:两个线程各自持有不同的锁，然后各自试图获取对方手里的锁，造成了双方无限等待下去;

```java
死锁出现的情况:
1. 执行 add(),获取 lockA,
2. 执行 dec(),获取 lockB,
3. 此时死锁,add()请求获得lockB的锁,获取不到,dec()请求获得lockA的锁,获取不到,同时所都无法释放

public void add(int m) {
    synchronized(lockA) { // 获得lockA的锁
        this.value += m;
        synchronized(lockB) { // 获得lockB的锁
            this.another += m;
        } // 释放lockB的锁
    } // 释放lockA的锁
}
public void dec(int m) {
    synchronized(lockB) { // 获得lockB的锁
        this.another -= m;
        synchronized(lockA) { // 获得lockA的锁
            this.value -= m;
        } // 释放lockA的锁
    } // 释放lockB的锁
}
```

### 线程控制

多线程协调运行的原则就是：当条件不满足时，线程进入等待状态；当条件满足时，线程被唤醒，继续执行任务。

```java
Thread.sleep(1000);// 该线程暂停 1 秒
Thread.setPriority(int n); // 置线程的优先级,1~10, 默认值5,优先级高的线程被操作系统调度的优先级较高，操作系统对高优先级线程可能调度更频繁

Thread t;
t.start();// 启动线程
t.join(); // 等待 t 执行完毕
t.join(1000); // 最多等待 t 执行 1000 毫秒,然后竞争
t.join(1000,1000); // 1000 毫秒,1000 纳秒
t.interrupt(); // 立即中断线程 t

Object obj; // 特指锁对象
obj.wait(); // 当前线程进入等待状态,等待唤醒,必须在synchronized块中才能调用wait()方法，因为wait()方法调用时，会释放线程获得的锁，wait()方法返回后，线程又会重新试图获得锁(重新竞争锁,但是会在获取锁后从 wait()方法之后开始执行 )。
obj.notify();  // 唤醒随机一个正在this锁等待的线程
obj.notifyAll(); // 唤醒所有当前正在this锁等待的线程
```

### concurrent

1. jdk5 后引入引入了一个高级的处理并发的`java.util.concurrent`包,它提供了大量更高级的并发功能，能大大*简化多线程程序的编写*。
2. `ReentrantLock` 替换 `synchronized`:synchronized 这种锁一是很重，二是获取时必须一直等待，没有额外的尝试机制。使用 ReentrantLock 比直接使用 synchronized 更安全，线程在 tryLock()失败的时候不会导致死锁。

#### ReentrantLock 替换 synchronized

```java
Lock lock = new ReentrantLock();
public func(){
  lock.lock();
  try {
    // access the resource protected by this lock
  } finally {
    lock.unlock();
  }
}

// 和synchronized不同的是，ReentrantLock可以尝试获取锁：
if (lock.tryLock(1, TimeUnit.SECONDS)) { //在尝试获取锁的时候，最多等待1秒。如果1秒后仍未获取到锁，tryLock()返回false
    try {
        ...
    } finally {
        lock.unlock();
    }
}

```

#### Condition 实现线程唤醒/等待操作

synchronized 可以配合 wait 和 notify 实现线程在条件不满足时等待，条件满足时唤醒;使用 Condition 对象来实现 wait 和 notify 的功能

1. 从 `ReentrantLock` 锁获取 `Condition` 对象 `lock.newCondition();`
2. `await()` 等待唤醒
3. `signalAll()` 唤醒所有 condition.await()的线程,唤醒线程从 await()返回后需要重新获得锁。
4. `signal()` 唤醒其中一个

```java
class TaskQueue {
    // 锁对象
    private final Lock lock = new ReentrantLock();
    // 从锁获取condition对象
    private final Condition condition = lock.newCondition();
    private Queue<String> queue = new LinkedList<>();
    public void addTask(String s) {
        lock.lock();
        try {
            queue.add(s);
            // 唤醒所有锁
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
    public String getTask() {
        lock.lock();
        try {
            while (queue.isEmpty()) {
              // 等待唤醒
                condition.await();
            }
            return queue.remove();
        } finally {
            lock.unlock();
        }
    }
}
```

#### ReadWriteLock 读多写少

1. `ReadWriteLock` 允许多个线程同时读，但只要有一个线程在写，其他线程就必须等待
2. 适用条件是同一个数据，有大量线程读取，但仅有少数线程修改。

```java
public class Counter {
    private final ReadWriteLock rwlock = new ReentrantReadWriteLock();
    private final Lock rlock = rwlock.readLock();
    private final Lock wlock = rwlock.writeLock();
    private int[] counts = new int[10];
    public void inc(int index) {
        wlock.lock(); // 加写锁
        try {
            counts[index] += 1;
        } finally {
            wlock.unlock(); // 释放写锁
        }
    }
    public int[] get() {
        rlock.lock(); // 加读锁
        try {
            return Arrays.copyOf(counts, counts.length);
        } finally {
            rlock.unlock(); // 释放读锁
        }
    }
}

```

#### StampedLock

StampedLock 和 ReadWriteLock 相比，改进之处在于：读的过程中也允许获取写锁后写入！这样一来，我们读的数据就可能不一致，所以，需要一点额外的代码来判断读的过程中是否有写入，这种读锁是一种乐观锁。

**乐观锁**的意思就是乐观地估计读的过程中大概率不会有写入，因此被称为乐观锁。**悲观锁**则是读的过程中拒绝有写入，也就是写入必须等待。显然乐观锁的并发效率更高，但一旦有小概率的写入导致读取的数据不一致，需要能检测出来，再读一遍就行。

1. 多个写操作同步 `stamp = stampedLock.writeLock() ` `stampedLock.unlockWrite(stamp);`
2. 读:
   1. 获取乐观锁 `long stamp = stampedLock.tryOptimisticRead();` 得到一个版本号
   2. 读取数据
   3. 检查乐观读锁后是否有其他写锁发生 `stampedLock.validate(stamp)` 验证版本号
      1. `true` 没有发生写入操作,数据安全,
      2. `false` 发生写入操作,数据不安全
         1. 获取一个悲观读锁 `stamp = stampedLock.readLock();`
         2. 读取数据
         3. 释放悲观读锁 `stampedLock.unlockRead(stamp);`
   4. 可以操作数据进行其他操作

```java
public class Point {
    private final StampedLock stampedLock = new StampedLock();
    private double x;
    private double y;
    public void move(double deltaX, double deltaY) {
        long stamp = stampedLock.writeLock(); // 获取写锁
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            stampedLock.unlockWrite(stamp); // 释放写锁
        }
    }
    public double distanceFromOrigin() {
        long stamp = stampedLock.tryOptimisticRead(); // 获得一个乐观读锁
        // 注意下面两行代码不是原子操作
        // 假设x,y = (100,200)
        double currentX = x;
        // 此处已读取到x=100，但x,y可能被写线程修改为(300,400)
        double currentY = y;
        // 此处已读取到y，如果没有写入，读取是正确的(100,200)
        // 如果有写入，读取是错误的(100,400)
        if (!stampedLock.validate(stamp)) { // 检查乐观读锁后是否有其他写锁发生
            stamp = stampedLock.readLock(); // 获取一个悲观读锁
            try {
                currentX = x;
                currentY = y;
            } finally {
                stampedLock.unlockRead(stamp); // 释放悲观读锁
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}
```

#### 使用 Concurrent 线程安全的集合

使用 `java.util.concurrent` 包提供的线程安全的并发集合可以大大简化多线程编程：

多线程同时读写并发集合是安全的；

尽量使用 Java 标准库提供的并发集合，避免自己编写同步代码。

使用这些并发集合与使用非线程安全的集合类完全相同

```Java
interface	    non-thread-safe	          thread-safe
List	        ArrayList	                CopyOnWriteArrayList
Map	          HashMap	                  ConcurrentHashMap
Set	          HashSet / TreeSet	        CopyOnWriteArraySet
Queue	        ArrayDeque / LinkedList	  ArrayBlockingQueue / LinkedBlockingQueue
Deque	        ArrayDeque / LinkedList	  LinkedBlockingDeque
```

`java.util.Collections` 工具类还提供了一个旧的线程安全集合转换器，可以这么用：

```Java
Map unsafeMap = new HashMap();
Map threadSafeMap = Collections.synchronizedMap(unsafeMap);
```

但是它实际上是用一个包装类包装了非线程安全的 Map，然后对所有读写方法都用 `synchronized` 加锁，这样获得的线程安全集合的性能比 `java.util.concurrent` 集合要低很多，所以*不推荐使用*。

### 原子操作 Atomic

使用 `java.util.concurrent.atomic` 提供的原子操作可以简化多线程编程：

1. 原子操作实现了无锁的线程安全；
2. 适用于*计数器*，*累加器*等。

我们以 AtomicInteger 为例，它提供的主要操作有：

```Java
int addAndGet(int delta) // 增加值并返回新值：
int incrementAndGet() //加 1 后返回新值：
int get() //获取当前值：
int compareAndSet(int expect, int update) //用 CAS 方式设置：
```

Atomic 类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了 CAS：Compare and Set。

如果我们自己通过 CAS 编写 incrementAndGet()，它大概长这样：

```Java
public int incrementAndGet(AtomicInteger var) {
    int prev, next;
    do {
        prev = var.get();
        next = prev + 1;
    } while ( ! var.compareAndSet(prev, next));
    return prev;
}
CAS是指，在这个操作中，如果AtomicInteger的当前值是prev，那么就更新为next，返回true。如果AtomicInteger的当前值不是prev，就什么也不干，返回false。通过CAS操作并配合do … while循环，即使其他线程修改了AtomicInteger的值，最终的结果也是正确的。
```

### ThreadLocal

我们可以在代码中调用 `Thread.currentThread()`获取当前线程。

Java 标准库提供了一个特殊的 ThreadLocal，它可以在一个线程中传递同一个对象。
实际上，可以把 ThreadLocal 看成一个全局`Map<Thread, Object>`：每个线程获取 ThreadLocal 变量时，总是使用 Thread 自身作为 key：因此，ThreadLocal 相当于给每个线程都开辟了一个独立的存储空间，各个线程的 ThreadLocal 关联的实例互不干扰。

为了保证能释放 ThreadLocal 关联的实例，我们可以通过 AutoCloseable 接口配合 try (resource) {…}结构，让编译器自动为我们关闭。

```java

static ThreadLocal<String> threadLocalUser = new ThreadLocal<>();

void processUser(user) {
    try {
        threadLocalUser.set(user);
        step1();
        step2();
        // 在 step1() step2() 中调用 get() 将返回同一个对象user
    } finally {
      // 最后，特别注意ThreadLocal一定要在finally中清除：
        threadLocalUser.remove();
    }
}
```

### 线程池

1. 提高响应速度(减少了创建新线程的时间)
2. 降低了资源消耗(重复利用线程池中线程,不需要每次都创建)
3. 线程池内部维护了若干个线程，没有任务的时候，这些线程都处于等待状态。如果有新任务，就分配一个空闲线程执行。如果所有线程都处于忙碌状态，新任务要么放入队列等待，要么增加一个新线程进行处理。
4. 便于线程管理
   1. corePoolSize 核心池的大小
   2. maximumPoolSize 最大线程数
   3. keepAliveTime 线程没有任务时最多保持多长时间后会终止

#### ExecutorService

`ExecutorService` Java 标准库提供了 ExecutorService 接口表示线程池,`Executors` 工具类\线程池的工厂类,用来创建并返回不同类型的线程池,常用实现类如下:

- 名词解释
- 线程创建
  - Thread
  - Runnable
  - Callable
- 线程状态
- 守护线程
- volatile 及时同步共享变量
- synchronized
  - synchronized 同步代码块
  - jvm 规定的原子操作
  - synchronized 同步方法
  - 可重入锁
  - 死锁
- 线程控制
- concurrent
  - ReentrantLock 替换 synchronized
  - Condition 实现线程唤醒/等待操作
  - ReadWriteLock 读多写少
  - StampedLock
  - 使用 Concurrent 线程安全的集合
  - 原子操作 Atomic
- ThreadLocal
- 线程池
- ExecutorService
  - FixedThreadPool 线程数固定的线程池
  - CachedThreadPool 线程数根据任务动态调整的线程池；
  - ScheduledThreadPool 定时执行任务
- Future
  - Callable 有返回值的线程
  - CompletableFuture
- ForkJoin 大任务拆分为多个并行任务

常用方法:

1. `submit()` 执行任务
2. `shutdown()`执行任务
3. `shutdownNow()`会立刻停止正在执行的任务
4. `awaitTermination()`则会等待指定的时间让线程池关闭。

#### FixedThreadPool 线程数固定的线程池

```java
package com.lxh.threadDemo;

import org.junit.jupiter.api.Test;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorServiceDemo {
    @Test
    public void example() {
      // 创建线程池
        ExecutorService executor = Executors.newFixedThreadPool(3);
      // 添加线程任务
        executor.submit(new Task("高"));
        executor.submit(new Task("山"));
        executor.submit(new Task("流"));
      // 线程池满,等待其中一个线程执行完毕
        executor.submit(new Task("水"));
        // 关闭线程池
        executor.shutdown();
    }
}

class Task implements Runnable {
    public String name;

    public Task(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(name + "\t" + i);
        }
    }
}

```

#### CachedThreadPool 线程数根据任务动态调整的线程池；

```java
@Test
public void example() {
    // 创建线程池
    int min = 4;
    int max = 10;
    // 线程池的大小限制在4～10个之间动态调整
    ExecutorService es = new ThreadPoolExecutor(min, max,
        60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
    // 添加线程任务
    executor.submit(new Task("高"));
    executor.submit(new Task("山"));
    executor.submit(new Task("流"));
    // 线程池满,等待其中一个线程执行完毕
    executor.submit(new Task("水"));
    // 关闭线程池
    executor.shutdown();
}
```

#### ScheduledThreadPool 定时执行任务

```java
@Test
public void example() {
    // 创建线程池
    int min = 4;
    int max = 10;
    // 线程池的大小限制在4～10个之间动态调整
    ScheduledExecutorService ses = Executors.newScheduledThreadPool(4);
    // 1秒后执行一次性任务:
    ses.schedule(new Task("one-time"), 1, TimeUnit.SECONDS);
    // 2秒后开始执行定时任务，每3秒执行:
    ses.scheduleAtFixedRate(new Task("fixed-rate"), 2, 3, TimeUnit.SECONDS);
    // 2秒后开始执行定时任务，以3秒为间隔执行:
    ses.scheduleWithFixedDelay(new Task("fixed-delay"), 2, 3, TimeUnit.SECONDS);
}
```

1. `scheduleAtFixedRate` ，是以上一个任务开始的时间计时，3 秒过去后，检测上一个任务是否执行完毕，如果上一个任务执行完毕，则当前任务立即执行，如果上一个任务没有执行完毕，则需要等上一个任务执行完毕后立即执行。
2. `scheduleWithFixedDelay` ，是以上一个任务结束时开始计时，3 秒过去后，立即执行。
3. 如果其中有一次任务抛出异常而未被处理,那么定时任务将会卡在错误那一行,不会继续进行,程序也不会报错,也不会停止

Java 标准库还提供了一个 `java.util.Timer` 类，这个类也可以定期执行任务，但是，一个 `Timer` 会对应一个 `Thread` ，所以，一个 `Timer` 只能定期执行一个任务，多个定时任务必须启动多个 `Timer` ，而一个 `ScheduledThreadPool` 就可以调度多个定时任务，所以，我们完全可以用 `ScheduledThreadPool` 取代旧的 `Timer` 。

### Future

#### Callable 有返回值的线程

1. 实现 `Callable<T>` 接口,泛型为返回值类型
2. 任务放入线程池`submit()`得到 `Future` 对象
3. `future.get()` 在调用 get()时，如果异步任务已经完成，我们就直接获得结果。如果异步任务还没有完成，那么 get()会阻塞，直到任务完成后才返回结果。
   1. `get()`：获取结果（可能会等待）
   2. `get(long timeout, TimeUnit unit)`：获取结果，但只等待指定的时间；
   3. `cancel(boolean mayInterruptIfRunning)`：取消当前任务；
   4. `isDone()`：判断任务是否已完成。
4.

```java
@Test
public void example(){
  ExecutorService executor = Executors.newFixedThreadPool(4);
  // 定义任务:
  Callable<String> task = new Task();
  // 提交任务并获得Future:
  Future<String> future = executor.submit(task);
  // 从Future获取异步执行返回的结果:
  String result = future.get(); // 可能阻塞
}

class Task implements Callable<String> {
    public String call() throws Exception {
        return "";
    }
}
```

#### CompletableFuture

使用 Future 获得异步执行结果时，要么调用阻塞方法 get()，要么轮询看 isDone()是否为 true，这两种方法都不是很好，因为主线程也会被迫等待。

从 `Java 8` 开始引入了 `CompletableFuture`,它针对 `Future` 做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。

1. 实现了异步回调机制,似 JavaScript 的 promise
   1. 创建: `supplyAsync()`
   2. 成功: `thenAccept()`
   3. 失败: `thenAccept()`
2. 多个 CompletableFuture 可以**串行执行**;例如，定义两个 CompletableFuture，第一个 CompletableFuture 根据证券名称查询证券代码，第二个 CompletableFuture 根据证券代码查询证券价格
   1. `thenApplyAsync()`用于串行化另一个 CompletableFuture
3. 多个 CompletableFuture 还可以**并行执行**
   1. `anyOf()` 可以实现“任意个 CompletableFuture 只要一个成功”
   2. `allOf()` 可以实现“所有 CompletableFuture 都必须成功”
4. 最后我们注意 CompletableFuture 的命名规则：`xxx()`表示该方法将继续在已有的线程中执行；`xxxAsync()`表示将异步在线程池中执行。

```java
// 类
@Test
public void example2() throws InterruptedException {
    // 创建异步执行任务
    CompletableFuture<Double> cf = CompletableFuture.supplyAsync(this::fetchPrice);
    // 如果执行成功
    cf.thenAccept(result -> {
        System.out.println(result);
    });
    // 如果执行失败
    cf.exceptionally(e -> {
        e.printStackTrace();
        System.out.println("11111111");
        return null;
    });
    Thread.sleep(2000);

}

public Double fetchPrice() {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {

    }
    if (Math.random() < 0.8) {
        throw new RuntimeException("fetch price failed !");
    }
    return 5 + Math.random() * 20;
}
```

### ForkJoin 大任务拆分为多个并行任务

Java 7 开始引入了一种新的 Fork/Join 线程池，它可以执行一种特殊的任务：把一个大任务拆成多个小任务并行执行。

我们举个例子：如果要计算一个超大数组的和，

1. 最简单的做法是用一个循环在一个线程内完成
2. 数组拆分为两个数组,并行计算两个数组的值,然后再相加
3. 如果拆成两部分还是很大，我们还可以继续拆，用 4 个线程并行执行

Fork/Join 任务的原理：**判断一个任务是否足够小，如果是，直接计算，否则，就分拆成几个小任务分别计算**。这个过程可以反复“裂变”成一系列小任务。

1. 任务类必须继承自 `RecursiveTask` 或 `RecursiveAction`
2. 原理: 递归创建线程进行计算
3. 在 `compute()` 方法里
   1. 确定递归结束条件
   2. 定义子任务,及新建任务类对象
   3. `invokeAll() ` 并行计算子任务
   4. `join()`获得子任务的结果
   5. 汇总子任务结果,返回最后结果

```java
@Test
public void exampleForkJoin() {
    long[] array = new long[1000000000];
    long expectedSum = 0;
    Random random = new Random(0);
    for (int i = 0; i < array.length; i++) {
        array[i] = random.nextInt(10000);
    }
    long startTime1 = System.currentTimeMillis();
    for (int i = 0; i < array.length; i++) {
        expectedSum += array[i];
    }
    long endTime1 = System.currentTimeMillis();
    System.out.println("sum:" + expectedSum + ",time=" + (endTime1 - startTime1) + "ms");
    // fork/join
    ForkJoinTask<Long> forkJoinTask = new SumTask(array, 0, array.length);
    long startTime2 = System.currentTimeMillis();
    Long invoke = ForkJoinPool.commonPool().invoke(forkJoinTask);
    long endTime2 = System.currentTimeMillis();
    System.out.println("fork/join sum:" + invoke + ",time=" + (endTime2 - startTime2) + "ms");

}
class SumTask extends RecursiveTask<Long> {
    static final int THRESHOLD = 25000000;
    long[] array;
    int start;
    int end;

    public SumTask(long[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // 如果任务足够小,直接计算
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += this.array[i];
            }
            return sum;
        }
        int middle = (end + start) / 2;
        //System.out.println(String.format("split %d-%d ==> %d-%d,%d-%d", start, end, start, middle, middle, end));
        SumTask sumTask1 = new SumTask(this.array, start, middle);
        SumTask sumTask2 = new SumTask(this.array, middle, end);
        invokeAll(sumTask1, sumTask2);
        Long subResult1 = sumTask1.join();
        Long subResult2 = sumTask2.join();
        Long result = subResult1 + subResult2;
        //System.out.printf("%d=%d+%d\n", result, subResult1, subResult2);
        // 汇总结果:
        return result;
    }
}
```

# Stream API (jdk8)

Java 从 8 开始，不但引入了 Lambda 表达式，还引入了一个全新的流式 API：Stream API。它位于 `java.util.stream `包中。

划重点：这个 Stream 不同于 java.io 的 InputStream 和 OutputStream，它代表的是**任意 Java 对象的序列**。这个 Stream 和 List 也不一样，List 存储的每个元素都是已经存储在内存中的某个 Java 对象，而 Stream 输出的元素可能并没有预先存储在内存中，而是实时计算出来的。

```java
// 全体自然数,无法使用List实现,因为内存不够
List<BigInteger> list = ??? ;
// Stream 实现
Stream<BigInteger> naturals = createNaturalStream(); // 全体自然数
Stream<BigInteger> streamNxN = naturals.map(n -> n.multiply(n)); // 全体自然数的平方

//因为这个streamNxN也有无限多个元素，要打印它，必须首先把无限多个元素变成有限个元素，可以用limit()方法截取前100个元素，最后用forEach()处理每个元素，这样，我们就打印出了前100个自然数的平方：
Stream<BigInteger> naturals = createNaturalStream();
naturals.map(n -> n.multiply(n)) // 1, 4, 9, 16, 25...
        .limit(100)
        .forEach(System.out::println);
```

1. 可以“存储”有限个或无限个元素。这里的存储打了个引号，是因为元素有可能已经全部存储在内存中，也有可能是根据需要实时计算出来的。
2. 一个 Stream 可以轻易地转换为另一个 Stream，而不是修改原 Stream 本身。 **惰性计算**的特点是：一个 Stream 转换为另一个 Stream 时，实际上只存储了转换规则，并没有任何计算发生。

基本用法:

```java
int result = createNaturalStream() // 创建Stream
             .filter(n -> n % 2 == 0) // 任意个转换
             .map(n -> n * n) // 任意个转换
             .limit(100) // 任意个转换
             .sum(); // 最终计算结果
```

### 创建

```java
Stream.of(); //传入可变参数即创建了一个能输出确定元素的Stream
Arrays.stream() // 基于一个数组或者Collection
List.of().stream() // 基于一个Collection
Stream<String> s = Stream.generate(Supplier<String> sp); // 基于Supplier

try (Stream<String> lines = Files.lines(Paths.get("/path/to/file.txt"))) {
    ...
    // Files类的lines()方法可以把一个文件变成一个Stream，每个元素代表文件的一行内容
}

// 正则表达式的Pattern对象有一个splitAsStream()方法，可以直接把一个长字符串分割成Stream序列而不是数组
Pattern p = Pattern.compile("\\s+");
Stream<String> s = p.splitAsStream("The quick brown fox jumps over the lazy dog");
s.forEach(System.out::println);

/**
因为Java的范型不支持基本类型，所以我们无法用Stream<int>这样的类型，会发生编译错误。为了保存int，只能使用String<Integer>，但这样会产生频繁的装箱、拆箱操作。为了提高效率，Java标准库提供了IntStream、LongStream和DoubleStream这三种使用基本类型的Stream，它们的使用方法和范型Stream没有大的区别，设计这三个Stream的目的是提高运行效率
 */
// 将int[]数组变为IntStream:
IntStream is = Arrays.stream(new int[] { 1, 2, 3 });
// 将Stream<String>转换为LongStream:
LongStream ls = List.of("1", "2", "3").stream().mapToLong(Long::parseLong);
```

### 转换

Stream.map()是 Stream 最常用的一个转换方法，它把一个 Stream 转换为另一个 Stream

```java
// 利用map()，不但能完成数学计算，对于字符串操作，以及任何Java对象都是非常有用的。
Stream<Integer> s = Stream.of(1, 2, 3, 4, 5);
Stream<Integer> s2 = s.map(n -> n * n); // 计算平方
```

Stream.filter()是 Stream 的另一个常用转换方法。

所谓 filter()操作，就是对一个 Stream 的所有元素一一进行测试，不满足条件的就被“滤掉”了，剩下的满足条件的元素就构成了一个新的 Stream。

```java
Stream<Integer> s = Stream.of(1, 2, 3, 4, 5);
Stream<Integer> s2 = s.map(n -> n>3); // 获取大于3的序列
```

### 聚合

map()和 filter()都是 Stream 的转换方法，而 Stream.reduce()则是 Stream 的一个聚合方法，它可以把一个 Stream 的所有元素按照聚合函数聚合成一个结果。

reduce()方法传入的对象是 BinaryOperator 接口，它定义了一个 apply()方法，负责把上次累加的结果和本次的元素进行运算，并返回累加的结果：

```java
Stream<Integer> s = Stream.of(1, 2, 3, 4, 5);
// 计算序列求和, reduce(初始值,(上次计算的值,下一个元素)->{})
int sum = stream.reduce(0,(acc, n) -> acc + n);
// 如果去掉初始值，我们会得到一个Optional<Integer>：
Optional<Integer> opt = stream.reduce((acc, n) -> acc + n);
if (opt.isPresent) {
  // 这是因为Stream的元素有可能是0个，这样就没法调用reduce()的聚合函数了，因此返回Optional对象，需要进一步判断结果是否存在。
    System.out.println(opt.get());
}
// 除了可以对数值进行累积计算外，灵活运用reduce()也可以对Java对象进行操作。

```

collect() 将流转换为集合

```java
List<Integer> list = Stream.of(1, 2, 3, 4, 5).collect(Collectors.toList()); // 可以把Stream的每个元素收集到List中
collect(Collectors.toSet()) // 可以把Stream的每个元素收集到Set中
collect(Collectors.toMap(n->n,n->n));

collect(Collectors.groupingBy(n->n,Collectors.toList())); // 分组
```

其他

```Java
count()：用于返回元素个数；
max(Comparator<? super T> cp)：找出最大元素；
min(Comparator<? super T> cp)：找出最小元素。

针对IntStream、LongStream和DoubleStream，还额外提供了以下聚合方法：

sum()：对所有元素求和；
average()：对所有元素求平均数。
```

### 其他

```java
sorted() // 排序 每个元素必须实现Comparable接口,自定义排序，传入指定的Comparator
distinct() //去重
skip() // 跳过当前Stream的前N个元素
limit() // 截取当前Stream最多前N个元素
concat() // 合并
flatMap() // 转换集合元素
parallel() // 并行处理

还有一些方法，用来测试Stream的元素是否满足以下条件：

boolean allMatch(Predicate<? super T>)：测试是否所有元素均满足测试条件；
boolean anyMatch(Predicate<? super T>)：测试是否至少有一个元素满足测试条件。

最后一个常用的方法是forEach()，它可以循环处理Stream的每个元素，我们经常传入System.out::println来打印Stream的元素：
forEach(System.out::println)
```
