# PROTOBUF

ProtoBuf (protocol buffers)是结构数据**序列化[1]** 方法，可简单**类比于 XML[2]**，其具有以下特点：

- **语言无关、平台无关**。即 ProtoBuf 支持 Java、C++、Python 等多种语言，支持多个平台
- **高效**。即比 XML 更小（3 ~ 10 倍）、更快（20 ~ 100 倍）、更为简单
- **扩展性、兼容性好**。你可以更新数据结构，而不影响和破坏原有的旧程序

官方文档: https://developers.google.com/protocol-buffers/docs/overview

协议缓冲区并不适合所有数据。尤其是：

- 协议缓冲区倾向于假设**整个消息可以一次加载到内存中并且不大于对象图**。对于**超过几兆字节**的数据，考虑不同的解决方案；在处理较大的数据时，由于序列化副本，您可能会有效地获得多个数据副本，这可能会导致内存使用量出现惊人的峰值。
- 当协议缓冲区被序列化时，相同的数据可以有许多不同的二进制序列化。如果不完全解析它们，就无法比较两条消息的相等性。
- **消息未压缩**。虽然消息可以像任何其他文件一样被压缩或 gzip 压缩，但专用压缩算法（如 JPEG 和 PNG 使用的压缩算法）将为适当类型的数据生成小得多的文件。
- 对于涉及大型多维浮点数数组的许多科学和工程用途，协议缓冲区消息在大小和速度方面都没有达到最大效率。对于这些应用程序， FITS 和类似格式的开销较小。
- 协议缓冲区在科学计算中流行的非面向对象语言（例如 Fortran 和 IDL）中没有得到很好的支持。
- 协议缓冲区消息本身并不自我描述其数据，但它们具有完全反射的模式，您可以使用它来实现自我描述。也就是说，如果不访问其相应的.proto 文件，您将无法完全解释它。
- 协议缓冲区不是任何组织的正式标准。这使得它们不适合在具有法律或其他要求以建立在标准之上的环境中使用。

**工作原理**:
![1658903931531](image/protobuf/1658903931531.png)

1. 创建`.proto`文件,定义数据结构
2. 使用`protoc`编译器生成代码(go,java,c++,....)
3. 生成的代码会有一个类似`builder`的方法,将数据转换为字节码
4. 将字节码序列化,分享,反序列化

`.proto`

# proto2

[官方文档](https://developers.google.com/protocol-buffers/docs/proto)

## 定义消息类型

```proto

message SearchRequest {
  字段规则 字段类型 字段名 = 字段编号
}
```

### 字段规则

`required`: 格式良好的消息必须恰好具有该字段之一。
`optional` ：一个格式良好的消息可以有零个或一个这个字段（但不能超过一个）。
`repeated` ： 该字段可以在格式良好的消息中重复任意次数（包括零次）。重复值的顺序将被保留

```proto
message msg{
    // 由于历史原因，标量数字类型的 repeated 字段（例如 int32、int64、enum）编码的效率不如预期。新代码应使用特殊选项 [packed = true] 来提高编码效率。
  repeated int32 d = 4 [packed=true];
}
```

### 字段类型

#### 标量类型

`int32` 32 位整数
`int64` 64 位整数
`sint32`
`sint64`
`string` 字符串
`32-bit`

#### 复合类型

`Duration` 是有符号的、固定长度的时间跨度，例如 42 秒。
`Timestamp` 是独立于任何时区或日历的时间点，例如 2017-01-15T01:30:15.01Z。
`Interval` 是独立于时区或日历的时间间隔，例如 2017-01-15T01:30:15.01Z - 2017-01-16T02:30:15.01Z。
`Date` 是一个完整的日历日期，例如 2025-09-19。
`DayOfWeek` 是一周中的某一天，例如星期一。
`TimeOfDay` 是一天中的某个时间，例如 10:42:23。
`LatLng` 是一个纬度/经度对，例如 37.386051 纬度和 -122.083855 经度。
`Money` 是具有货币类型的金额，例如 42 美元。
`PostalAddress` 是邮政地址，例如 1600 Amphitheatre Parkway Mountain View, CA 94043 USA。
`Color` 是 RGBA 颜色空间中的一种颜色。
`Month` 是一年中的一个月，例如四月。

### 字段编号

消息定义中的每个字段都有一个唯一的编号。
。1 到 15 范围内的字段编号需要一个字节进行编码，包括字段编号和字段类型（您可以在 Protocol Buffer Encoding 中找到更多相关信息 ）。16 到 2047 范围内的字段编号占用两个字节。因此，您应该为非常频繁出现的消息元素保留字段编号 1 到 15。请记住为将来可能添加的频繁出现的元素留出一些空间。
https://developers.google.com/protocol-buffers/docs/encoding
https://developers.google.com/protocol-buffers/docs/encoding#structure

**最小字段编号**是 1，**最大**的是 2^(29 - 1)，即 536,870,911。您也不能使用数字 19000 到 19999（FieldDescriptor::kFirstReservedNumber 到 FieldDescriptor::kLastReservedNumber），因为它们是为 Protocol Buffers 实现保留的 - 如果您在.proto. 同样，您不能使用任何以前保留的字段编号。

### 字段注释

```proto
//
/**/
```

### 保留字段

```proto
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
  // reserved之后的字段被废弃,使用会警告
}
```

## 标量值类型

![1658913097418](image/protobuf/1658913097418.png)

## 可选字段与默认值

# proto3

# proto3 与 proto2 的区别

**1、proto3 中，在第一行非空白非注释行，必须写：syntax = "proto3";**

1）如果我们使用 proto3 的库，在文件中没有写这行，那么 proto 默认会使用 proto2 的语法进行编译，同时生成告警：

```
[libprotobuf WARNING google/protobuf/compiler/parser.cc:547] No syntax specified
 for the proto file: cinema.proto. Please use 'syntax = "proto2";' or 'syntax =
"proto3";' to specify a syntax version. (Defaulted to proto2 syntax.)
```

2）我们使用 proto2 的语法写了一个消息文件，然后用 proto3 编译（默认使用 proto2 编译），之后将生成的 java 类导入到工程中会报错（虽然是使用 proto2 进行的编译，但是在编译中还是会引入 3 的一些特性）。这时，maven 需要升级 protobuf-java 到对应的版本（如：3.0.2）之后就好了。

所以，正确的做法是版本对其：使用 proto3 语法编写消息 proto 文件；使用 proto3 编译；项目中使用 proto3 库使用。

**2、消息定义时，移除了 “required”、 “optional” ：**

因为 required 字段通常被认为是有害的并且违反了 protobuf 的兼容性语义。如果没有去除，使用 proto3 编译时会报错：

```
Required fields are not allowed in proto3.
Explicit 'optional' labels are disallowed in the Proto3
```

**3、移除了 default 选项：**

在 proto2 中，可以使用 default 选项为某一字段指定默认值。在 proto3 中，字段的默认值只能根据字段类型由系统决定。也就是说，默认值全部是约定好的，而不再提供指定默认值的语法。在字段被设置为默认值的时候，该字段不会被序列化。这样可以节省空间，提高效率。但这样就无法区分某字段是根本没赋值，还是赋值了默认值。

这在 proto3 中问题不大，但在 proto2 中会有问题。比如，在更新协议的时候使用 default 选项为某个字段指定了一个与原来不同的默认值，旧代码获取到的该字段的值会与新代码不一样。

**4、枚举类型的第一个字段必须为 0 ：**

定义枚举时如果下表从 1 开始会报错：

```
The first enum value must be zero in proto3
```

**5、增加 maps 结构：**

```
message Test {
  map<int32, bool> a = 1;
}
```

**6、去掉 extensions 类型，使用 Any 新标准类型替换**
