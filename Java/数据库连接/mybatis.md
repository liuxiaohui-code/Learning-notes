# MyBatis

## 学习教程

1. 入门学习网站: [mybatis 中文网](https://mybatis.net.cn/)

## 介绍

是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 `Java POJO`（`Plain Old Java Objects，`普通老式 Java 对象）为数据库中的记录。

要使用 `MyBatis，` 只需将 `mybatis-x.x.x.jar` 文件置于类路径（`classpath`）中即可。

如果使用 Maven 来构建项目，则需将下面的依赖代码置于 pom.xml 文件中：

<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>

**基本流程:**

1. 引入包
2. 配置核心配置文件(数据库驱动)
3. 数据库实体类
4. Mapper 接口
5. 编写 Mapper.xml ,sql 语句
6. Mapper 写入核心配置文件
7. 创建`SqlSessionFactory` `SqlSession` `Mapper`接口实例对象

# 环境配置

每个基于 `MyBatis` 的应用都是以一个 `SqlSessionFactory` 的实例为核心的。`SqlSessionFactory` 的实例可以通过 `SqlSessionFactoryBuilder` 获得。而 `SqlSessionFactoryBuilder` 则可以从 XML 配置文件或一个预先配置的 `Configuration` 实例来构建出 `SqlSessionFactory` 实例。

## 使用 xml 构建 SqlSessionFactory

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 file:// URL 构造的输入流。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

```

XML 配置文件中包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的数据源（DataSource）以及决定事务作用域和控制方式的事务管理器（TransactionManager）。

实例配置文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

## 不使用 XML 构建 SqlSessionFactory

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

注意该例中，configuration 添加了一个映射器类（mapper class）。映射器类是 Java 类，它们包含 SQL 映射注解从而避免依赖 XML 文件。不过，由于 Java 注解的一些限制以及某些 MyBatis 映射的复杂性，要使用大多数高级映射（比如：嵌套联合映射），仍然需要使用 XML 配置。有鉴于此，如果存在一个同名 XML 配置文件，MyBatis 会自动查找并加载它（在这个例子中，基于类路径和 BlogMapper.class 的类名，会加载 BlogMapper.xml）。

## 从 SqlSessionFactory 中获取 SqlSession

既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}

session.close(); // 使用后需要关闭
```

诚然，这种方式能够正常工作，对使用旧版本 MyBatis 的用户来说也比较熟悉。但现在有了一种更简洁的方式——使用和指定语句的参数和返回值相匹配的接口（比如 BlogMapper.class），现在你的代码不仅更清晰，更加类型安全，还不用担心可能出错的字符串字面值以及强制类型转换。

例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}

session.commit();// 事务默认没有提交,需要手动提交
session.close(); // 使用后需要关闭
```

# 核心配置文件 mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <!--
    配置文件中的标签需要按照一定的顺序来配置
    properties?,settings?,typeAliases,typeHandlers,
    objectFactory,objectWrapperFactory,reflectorFactory,
    plugins,environments,databaseIdProvider,mappers
  -->
</configuration>
```

## properties 引入外部文件数据

属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 `properties` 元素的子元素中设置。

```properties
# jdbc.properties
jdbc.drever=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/hello_mybatis?useSSL=false&amp;allowPublicKeyRetrieval=true&amp;serverTimezone=GMT%2B8
jdbc.username=root
jdbc.password=2241296634
```

```xml
<properties resource="jdbc.properties"> <!--外部文件-->
  <property name="username" value="dev_user"/> <!--自定义-->
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
<!--使用 ${}-->
<dataSource type="POOLED">
  <property name="username" value="${jdbc.username}"/>
  <property name="password" value="${jdbc.password}"/>
</dataSource>
```

也可以在 `SqlSessionFactoryBuilder.build()` 方法中传入属性值。例如：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);
// ... 或者 ...
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

如果一个属性在不只一个地方进行了配置，那么，MyBatis 将按照下面的顺序来加载：

1. 首先读取在 properties 元素体内指定的属性。
2. 然后根据 properties 元素中的 resource 属性读取类路径下属性文件，或根据 url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。
3. 最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。
   因此，通过方法参数传递的属性具有最高优先级，resource/url 属性中指定的配置文件次之，最低优先级的则是 properties 元素中指定的属性。

从 MyBatis 3.4.2 开始，你可以为占位符指定一个默认值。例如：

```xml
<dataSource type="POOLED">
  <property name="username" value="${username:ut_user}"/>
  <!-- 如果属性 'username' 没有被配置，'username' 属性的值将为 'ut_user' -->

  <property name="org.apache.ibatis.parsing.PropertyParser.default-value-separator" value="?:"/>
  <!-- 修改默认值的分隔符为 ?: -->
</dataSource>

这个特性默认是关闭的。要启用这个特性，需要添加一个特定的属性来开启这个特性。例如：
<properties resource="org/mybatis/example/config.properties">
  <property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/> <!-- 启用默认值特性 -->
</properties>
```

## settings 设置运行时行为

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项设置的含义、默认值等。

设置名 描述 有效值 默认值
`cacheEnabled`
`lazyLoadingEnabled`
`aggressiveLazyLoading` 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载（参考 lazyLoadTriggerMethods)。 true | false false （在 3.4.1 及之前的版本中默认为 true）
`multipleResultSetsEnabled`
`useColumnLabel`
`useGeneratedKeys`
`autoMappingUnknownColumnBehavior`
`defaultExecutorType`
`defaultStatementTimeout` 设置超时时间，它决定数据库驱动等待数据库响应的秒数。 任意正整数 未设置 (null)
`defaultFetchSize` 为驱动的结果集获取数量（fetchSize）设置一个建议值。此参数只可以在查询设置中被覆盖。 任意正整数 未设置 (null)
`defaultResultSetType` 指定语句默认的滚动策略。（新增于 3.5.2） FORWARD_ONLY | SCROLL_SENSITIVE | SCROLL_INSENSITIVE | DEFAULT（等同于未设置） 未设置 (null)
`safeRowBoundsEnabled` 是否允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为 false。 true | false False
`safeResultHandlerEnabled` 是否允许在嵌套语句中使用结果处理器（ResultHandler）。如果允许使用则设置为 false。 true | false True
`mapUnderscoreToCamelCase` 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 true | false False
`localCacheScope` MyBatis 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询。 默认值为 SESSION，会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存。 SESSION | STATEMENT SESSION
`jdbcTypeForNull` 当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型。 某些数据库驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 JdbcType 常量，常用值：NULL、VARCHAR 或 OTHER。 OTHER
`lazyLoadTriggerMethods` 指定对象的哪些方法触发一次延迟加载。 用逗号分隔的方法列表。 equals,clone,hashCode,toString
`defaultScriptingLanguage` 指定动态 SQL 生成使用的默认脚本语言。 一个类型别名或全限定类名。 org.apache.ibatis.scripting.xmltags.XMLLanguageDriver
`defaultEnumTypeHandler` 指定 Enum 使用的默认 TypeHandler 。（新增于 3.4.5） 一个类型别名或全限定类名。 org.apache.ibatis.type.EnumTypeHandler
`callSettersOnNulls` 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这在依赖于 Map.keySet() 或 null 值进行初始化时比较有用。注意基本类型（int、boolean 等）是不能设置成 null 的。 true | false false
`returnInstanceForEmptyRow` 当返回行的所有列都是空时，MyBatis 默认返回 null。 当开启这个设置时，MyBatis 会返回一个空实例。 请注意，它也适用于嵌套的结果集（如集合或关联）。（新增于 3.4.2） true | false false
`logPrefix` 指定 MyBatis 增加到日志名称的前缀。 任何字符串 未设置
`logImpl` 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING 未设置
`proxyFactory` 指定 Mybatis 创建可延迟加载对象所用到的代理工具。 CGLIB | JAVASSIST JAVASSIST （MyBatis 3.3 以上）
`vfsImpl` 指定 VFS 的实现 自定义 VFS 的实现的类全限定名，以逗号分隔。 未设置
`useActualParamName` 允许使用方法签名中的名称作为语句参数名称。 为了使用该特性，你的项目必须采用 Java 8 编译，并且加上 -parameters 选项。（新增于 3.4.1） true | false true
`configurationFactory` 指定一个提供 Configuration 实例的类。 这个被返回的 Configuration 实例用来加载被反序列化对象的延迟加载属性值。 这个类必须包含一个签名为 static Configuration getConfiguration() 的方法。（新增于 3.2.3） 一个类型别名或完全限定类名。 未设置

```xml
<settings>
   全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。	true | false	true
  <setting name="cacheEnabled" value="true"/>

  延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态。	true | false	false
  <setting name="lazyLoadingEnabled" value="true"/>

  是否允许单个语句返回多结果集（需要数据库驱动支持）。	true | false	true
  <setting name="multipleResultSetsEnabled" value="true"/>

  使用列标签代替列名。实际表现依赖于数据库驱动，具体可参考数据库驱动的相关文档，或通过对比测试来观察。	true | false	true
  <setting name="useColumnLabel" value="true"/>

  允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。尽管一些数据库驱动不支持此特性，但仍可正常工作（如 Derby）。	true | false	False
  <setting name="useGeneratedKeys" value="false"/>

  指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）。	NONE, PARTIAL, FULL	PARTIAL
  <setting name="autoMappingBehavior" value="PARTIAL"/>

  指定发现自动映射目标未知列（或未知属性类型）的行为。
    NONE: 不做任何反应
    WARNING: 输出警告日志（'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior' 的日志等级必须设置为 WARN）
    FAILING: 映射失败 (抛出 SqlSessionException)
    NONE, WARNING, FAILING	NONE
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>

  配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（PreparedStatement）； BATCH 执行器不仅重用语句还会执行批量更新。	SIMPLE REUSE BATCH	SIMPLE
  <setting name="defaultExecutorType" value="SIMPLE"/>

  <setting name="defaultStatementTimeout" value="25"/>

  <setting name="defaultFetchSize" value="100"/>

  <setting name="safeRowBoundsEnabled" value="false"/>

  <setting name="mapUnderscoreToCamelCase" value="false"/>

  <setting name="localCacheScope" value="SESSION"/>

  <setting name="jdbcTypeForNull" value="OTHER"/>

  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

## typeAliases 设置类型别名

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

```xml
<typeAliases>
  <!--
    typeAlias 对全限定类设置别名
      alias: 别名,如果不设置,默认为类型(不区分大小写)
      type: 全限定类名
  -->
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>

  <!--
    package 通过包设置类型别名,对该包下的类拥有默认的别名
  -->
  <package name="domain.blog"/>
</typeAliases>
```

```java
// @Alias 指定类的别名,
@Alias("author")
public class Author {
    ...
}
```

### java 内建类型别名

别名 映射的类型
\_byte byte
\_long long
\_short short
\_int int
\_integer int
\_double double
\_float float
\_boolean boolean
string String
byte Byte
long Long
short Short
int Integer
integer Integer
double Double
float Float
boolean Boolean
date Date
decimal BigDecimal
bigdecimal BigDecimal
object Object
map Map
hashmap HashMap
list List
arraylist ArrayList
collection Collection
iterator Iterator

## typeHandlers 类型处理器

MyBatis 在设置预处理语句（PreparedStatement）中的参数或从结果集中取出一个值时， 都会用类型处理器将获取到的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器。

提示 从 3.4.5 开始，MyBatis 默认支持 JSR-310（日期和时间 API） 。

类型处理器 Java 类型 JDBC 类型
BooleanTypeHandler java.lang.Boolean, boolean 数据库兼容的 BOOLEAN
ByteTypeHandler java.lang.Byte, byte 数据库兼容的 NUMERIC 或 BYTE
ShortTypeHandler java.lang.Short, short 数据库兼容的 NUMERIC 或 SMALLINT
IntegerTypeHandler java.lang.Integer, int 数据库兼容的 NUMERIC 或 INTEGER
LongTypeHandler java.lang.Long, long 数据库兼容的 NUMERIC 或 BIGINT
FloatTypeHandler java.lang.Float, float 数据库兼容的 NUMERIC 或 FLOAT
DoubleTypeHandler java.lang.Double, double 数据库兼容的 NUMERIC 或 DOUBLE
BigDecimalTypeHandler java.math.BigDecimal 数据库兼容的 NUMERIC 或 DECIMAL
StringTypeHandler java.lang.String CHAR, VARCHAR
ClobReaderTypeHandler java.io.Reader -
ClobTypeHandler java.lang.String CLOB, LONGVARCHAR
NStringTypeHandler java.lang.String NVARCHAR, NCHAR
NClobTypeHandler java.lang.String NCLOB
BlobInputStreamTypeHandler java.io.InputStream -
ByteArrayTypeHandler byte[] 数据库兼容的字节流类型
BlobTypeHandler byte[] BLOB, LONGVARBINARY
DateTypeHandler java.util.Date TIMESTAMP
DateOnlyTypeHandler java.util.Date DATE
TimeOnlyTypeHandler java.util.Date TIME
SqlTimestampTypeHandler java.sql.Timestamp TIMESTAMP
SqlDateTypeHandler java.sql.Date DATE
SqlTimeTypeHandler java.sql.Time TIME
ObjectTypeHandler Any OTHER 或未指定类型
EnumTypeHandler Enumeration Type VARCHAR 或任何兼容的字符串类型，用来存储枚举的名称（而不是索引序数值）
EnumOrdinalTypeHandler Enumeration Type 任何兼容的 NUMERIC 或 DOUBLE 类型，用来存储枚举的序数值（而不是名称）。
SqlxmlTypeHandler java.lang.String SQLXML
InstantTypeHandler java.time.Instant TIMESTAMP
LocalDateTimeTypeHandler java.time.LocalDateTime TIMESTAMP
LocalDateTypeHandler java.time.LocalDate DATE
LocalTimeTypeHandler java.time.LocalTime TIME
OffsetDateTimeTypeHandler java.time.OffsetDateTime TIMESTAMP
OffsetTimeTypeHandler java.time.OffsetTime TIME
ZonedDateTimeTypeHandler java.time.ZonedDateTime TIMESTAMP
YearTypeHandler java.time.Year INTEGER
MonthTypeHandler java.time.Month INTEGER
YearMonthTypeHandler java.time.YearMonth VARCHAR 或 LONGVARCHAR
JapaneseDateTypeHandler java.time.chrono.JapaneseDate DATE

你可以重写已有的类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。 具体做法为：实现 `org.apache.ibatis.type.TypeHandler` 接口， 或继承一个很便利的类 `org.apache.ibatis.type.BaseTypeHandler`， 并且可以（可选地）将它映射到一个 JDBC 类型。

```java
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
    return cs.getString(columnIndex);
  }
}
```

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```

使用上述的类型处理器将会覆盖已有的处理 Java String 类型的属性以及 VARCHAR 类型的参数和结果的类型处理器。 要注意 MyBatis 不会通过检测数据库元信息来决定使用哪种类型，所以你必须在参数和结果映射中指明字段是 VARCHAR 类型， 以使其能够绑定到正确的类型处理器上。这是因为 MyBatis 直到语句被执行时才清楚数据类型。

通过类型处理器的泛型，MyBatis 可以得知该类型处理器处理的 Java 类型，不过这种行为可以通过两种方法改变：

在类型处理器的配置元素（typeHandler 元素）上增加一个 javaType 属性（比如：javaType="String"）；
在类型处理器的类上增加一个 @MappedTypes 注解指定与其关联的 Java 类型列表。 如果在 javaType 属性中也同时指定，则注解上的配置将被忽略。
可以通过两种方式来指定关联的 JDBC 类型：

在类型处理器的配置元素上增加一个 jdbcType 属性（比如：jdbcType="VARCHAR"）；
在类型处理器的类上增加一个 @MappedJdbcTypes 注解指定与其关联的 JDBC 类型列表。 如果在 jdbcType 属性中也同时指定，则注解上的配置将被忽略。
当在 ResultMap 中决定使用哪种类型处理器时，此时 Java 类型是已知的（从结果类型中获得），但是 JDBC 类型是未知的。 因此 Mybatis 使用 javaType=[Java 类型], jdbcType=null 的组合来选择一个类型处理器。 这意味着使用 @MappedJdbcTypes 注解可以限制类型处理器的作用范围，并且可以确保，除非显式地设置，否则类型处理器在 ResultMap 中将不会生效。 如果希望能在 ResultMap 中隐式地使用类型处理器，那么设置 @MappedJdbcTypes 注解的 includeNullJdbcType=true 即可。 然而从 Mybatis 3.4.0 开始，如果某个 Java 类型只有一个注册的类型处理器，即使没有设置 includeNullJdbcType=true，那么这个类型处理器也会是 ResultMap 使用 Java 类型时的默认处理器。

最后，可以让 MyBatis 帮你查找类型处理器：

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <package name="org.mybatis.example"/>
</typeHandlers>
```

注意在使用自动发现功能的时候，只能通过注解方式来指定 JDBC 的类型。

你可以创建能够处理多个类的泛型类型处理器。为了使用泛型类型处理器， 需要增加一个接受该类的 class 作为参数的构造器，这样 MyBatis 会在构造一个类型处理器实例的时候传入一个具体的类。

```java
//GenericTypeHandler.java
public class GenericTypeHandler<E extends MyObject> extends BaseTypeHandler<E> {

  private Class<E> type;

  public GenericTypeHandler(Class<E> type) {
    if (type == null) throw new IllegalArgumentException("Type argument cannot be null");
    this.type = type;
  }
```

EnumTypeHandler 和 EnumOrdinalTypeHandler 都是泛型类型处理器，我们将会在接下来的部分详细探讨。

若想映射枚举类型 Enum，则需要从 EnumTypeHandler 或者 EnumOrdinalTypeHandler 中选择一个来使用。

比如说我们想存储取近似值时用到的舍入模式。默认情况下，MyBatis 会利用 EnumTypeHandler 来把 Enum 值转换成对应的名字。

注意 EnumTypeHandler 在某种意义上来说是比较特别的，其它的处理器只针对某个特定的类，而它不同，它会处理任意继承了 Enum 的类。
不过，我们可能不想存储名字，相反我们的 DBA 会坚持使用整形值代码。那也一样简单：在配置文件中把 EnumOrdinalTypeHandler 加到 typeHandlers 中即可， 这样每个 RoundingMode 将通过他们的序数值来映射成对应的整形数值。

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler" javaType="java.math.RoundingMode"/>
</typeHandlers>
```

自动映射器（auto-mapper）会自动地选用 EnumOrdinalTypeHandler 来处理枚举类型， 所以如果我们想用普通的 EnumTypeHandler，就必须要显式地为那些 SQL 语句设置要使用的类型处理器。

```xml
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.apache.ibatis.submitted.rounding.Mapper">
	<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap">
		<id column="id" property="id"/>
		<result column="name" property="name"/>
		<result column="funkyNumber" property="funkyNumber"/>
		<result column="roundingMode" property="roundingMode"/>
	</resultMap>

	<select id="getUser" resultMap="usermap">
		select * from users
	</select>
	<insert id="insert">
	    insert into users (id, name, funkyNumber, roundingMode) values (
	    	#{id}, #{name}, #{funkyNumber}, #{roundingMode}
	    )
	</insert>

	<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap2">
		<id column="id" property="id"/>
		<result column="name" property="name"/>
		<result column="funkyNumber" property="funkyNumber"/>
		<result column="roundingMode" property="roundingMode" typeHandler="org.apache.ibatis.type.EnumTypeHandler"/>
	</resultMap>
	<select id="getUser2" resultMap="usermap2">
		select * from users2
	</select>
	<insert id="insert2">
	    insert into users2 (id, name, funkyNumber, roundingMode) values (
	    	#{id}, #{name}, #{funkyNumber}, #{roundingMode, typeHandler=org.apache.ibatis.type.EnumTypeHandler}
	    )
	</insert>

</mapper>
注意，这里的 select 语句必须指定 resultMap 而不是 resultType。
```

## objectFactory 对象工厂

每次 MyBatis 创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成实例化工作。 默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认无参构造方法，要么通过存在的参数映射来调用带有参数的构造方法。 如果想覆盖对象工厂的默认行为，可以通过创建自己的对象工厂来实现。比如：

```java
// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  public Object create(Class type) {
    return super.create(type);
  }
  public Object create(Class type, List<Class> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```

ObjectFactory 接口很简单，它包含两个创建实例用的方法，一个是处理默认无参构造方法的，另外一个是处理带参数的构造方法的。 另外，setProperties 方法可以被用来配置 ObjectFactory，在初始化你的 ObjectFactory 实例后， objectFactory 元素体中定义的属性会被传递给 setProperties 方法。

## plugins （插件）

MyBatis 允许你在映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：

Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
ParameterHandler (getParameterObject, setParameters)
ResultSetHandler (handleResultSets, handleOutputParameters)
StatementHandler (prepare, parameterize, batch, update, query)
这些类中方法的细节可以通过查看每个方法的签名来发现，或者直接查看 MyBatis 发行包中的源代码。 如果你想做的不仅仅是监控方法的调用，那么你最好相当了解要重写的方法的行为。 因为在试图修改或重写已有方法的行为时，很可能会破坏 MyBatis 的核心模块。 这些都是更底层的类和方法，所以使用插件的时候要特别当心。

通过 MyBatis 提供的强大机制，使用插件是非常简单的，只需实现 Interceptor 接口，并指定想要拦截的方法签名即可。

```java
// ExamplePlugin.java
@Intercepts({@Signature(
  type= Executor.class,
  method = "update",
  args = {MappedStatement.class,Object.class})})
public class ExamplePlugin implements Interceptor {
  private Properties properties = new Properties();
  public Object intercept(Invocation invocation) throws Throwable {
    // implement pre processing if need
    Object returnObject = invocation.proceed();
    // implement post processing if need
    return returnObject;
  }
  public void setProperties(Properties properties) {
    this.properties = properties;
  }
}
```

```xml
<!-- mybatis-config.xml -->
<plugins>
  <plugin interceptor="org.mybatis.example.ExamplePlugin">
    <property name="someProperty" value="100"/>
  </plugin>
</plugins>
```

上面的插件将会拦截在 Executor 实例中所有的 “update” 方法调用， 这里的 Executor 是负责执行底层映射语句的内部对象。

> 提示 覆盖配置类

除了用插件来修改 MyBatis 核心行为以外，还可以通过完全覆盖配置类来达到目的。只需继承配置类后覆盖其中的某个方法，再把它传递到 SqlSessionFactoryBuilder.build(myConfig) 方法即可。再次重申，这可能会极大影响 MyBatis 的行为，务请慎之又慎。

## environments （环境配置）

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。还有许多类似的使用场景。

不过要记住：**尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory 实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：

每个数据库对应一个 SqlSessionFactory 实例
为了指定创建哪种环境，只要将它作为可选的参数传递给 SqlSessionFactoryBuilder 即可。可以接受环境配置的两个方法签名是：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);

// 默认环境
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

`environments` 元素定义了如何配置环境。

```xml
<!--
  default: 默认环境id
-->
<environments default="development">
  <!--
    id: 唯一id
  -->
  <environment id="development">
    <!--
      transactionManager: 设置事务管理器
      type: 事务管理的方式
      type="JDBC|MANAGED"
      JDBC: 使用jdbc原生的事务管理方式
      MANAGED: 被管理,例如:spring
    -->
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <!--
      dataSource: 设置数据源
      type: 设置数据源类型
        POOLED: 使用数据库连接池
        UNPOOLED: 不使用数据库连接池
        JNDI: 标识使用上下文中的数据源
    -->
    <dataSource type="POOLED">
      <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://localhost:3306/hello_mybatis?useSSL=false&amp;allowPublicKeyRetrieval=true&amp;serverTimezone=GMT%2B8"/>
      <property name="username" value="root"/>
      <property name="password" value="2241296634"/>
    </dataSource>
  </environment>
</environments>
```

### 事务管理器（transactionManager）

在 MyBatis 中有两种类型的事务管理器（也就是 `type="[JDBC|MANAGED]"`）：

- JDBC – 这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域。
- MANAGED – 这个配置几乎没做什么。它从不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会关闭连接。然而一些容器并不希望连接被关闭，因此需要将 closeConnection 属性设置为 false 来阻止默认的关闭行为。例如:

```xml
<transactionManager type="MANAGED">
  <property name="closeConnection" value="false"/>
</transactionManager>
```

提示 如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。

这两种事务管理器类型都不需要设置任何属性。它们其实是类型别名，换句话说，你可以用 TransactionFactory 接口实现类的全限定名或类型别名代替它们。

```java
public interface TransactionFactory {
  default void setProperties(Properties props) { // 从 3.5.2 开始，该方法为默认方法
    // 空实现
  }
  Transaction newTransaction(Connection conn);
  Transaction newTransaction(DataSource dataSource, TransactionIsolationLevel level, boolean autoCommit);
}
```

在事务管理器实例化后，所有在 XML 中配置的属性将会被传递给 setProperties() 方法。你的实现还需要创建一个 Transaction 接口的实现类，这个接口也很简单：

```java
public interface Transaction {
  Connection getConnection() throws SQLException;
  void commit() throws SQLException;
  void rollback() throws SQLException;
  void close() throws SQLException;
  Integer getTimeout() throws SQLException;
}
```

使用这两个接口，你可以完全自定义 MyBatis 对事务的处理。

### 数据源（dataSource）

dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。
有三种内建的数据源类型（也就是 `type="[UNPOOLED|POOLED|JNDI]"`）：

`UNPOOLED` – 这个数据源的实现会每次请求时打开和关闭连接。虽然有点慢，但对那些数据库连接可用性要求不高的简单应用程序来说，是一个很好的选择。 性能表现则依赖于使用的数据库，对某些数据库来说，使用连接池并不重要，这个配置就很适合这种情形。UNPOOLED 类型的数据源仅仅需要配置以下 5 种属性：

`driver` – 这是 JDBC 驱动的 Java 类全限定名（并不是 JDBC 驱动中可能包含的数据源类）。
`url` – 这是数据库的 JDBC URL 地址。
`username` – 登录数据库的用户名。
`password` – 登录数据库的密码。
`defaultTransactionIsolationLevel` – 默认的连接事务隔离级别。
`defaultNetworkTimeout` – 等待数据库操作完成的默认网络超时时间（单位：毫秒）。查看 java.sql.Connection#setNetworkTimeout() 的 API 文档以获取更多信息。

作为可选项，你也可以传递属性给数据库驱动。只需在属性名加上“driver.”前缀即可，例如：

`driver.encoding=UTF8` 这将通过 DriverManager.getConnection(url, driverProperties) 方法传递值为 UTF8 的 encoding 属性给数据库驱动。

`POOLED` – 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这种处理方式很流行，能使并发 Web 应用快速响应请求。

除了上述提到 UNPOOLED 下的属性外，还有更多属性用来配置 POOLED 的数据源：

`poolMaximumActiveConnections` – 在任意时间可存在的活动（正在使用）连接数量，默认值：10
`poolMaximumIdleConnections` – 任意时间可能存在的空闲连接数。
`poolMaximumCheckoutTime` – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
`poolTimeToWait` – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直失败且不打印日志），默认值：20000 毫秒（即 20 秒）。
`poolMaximumLocalBadConnectionTolerance` – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程。 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 poolMaximumIdleConnections 与 `poolMaximumLocalBadConnectionTolerance` 之和。 默认值：3（新增于 3.4.5）
`poolPingQuery` – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动出错时返回恰当的错误消息。
`poolPingEnabled` – 是否启用侦测查询。若开启，需要设置 poolPingQuery 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
`poolPingConnectionsNotUsedFor` – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。

`JNDI` – 这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用。这种数据源配置只需要两个属性：

`initial_context` – 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）。这是个可选属性，如果忽略，那么将会直接从 InitialContext 中寻找 data_source 属性。
`data_source` – 这是引用数据源实例位置的上下文路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。
和其他数据源配置类似，可以通过添加前缀“env.”直接把属性传递给 InitialContext。比如：
`env.encoding=UTF8` 这就会在 InitialContext 实例化时往它的构造方法传递值为 UTF8 的 encoding 属性。

你可以通过实现接口 org.apache.ibatis.datasource.DataSourceFactory 来使用第三方数据源实现：

```java
public interface DataSourceFactory {
  void setProperties(Properties props);
  DataSource getDataSource();
}
```

`org.apache.ibatis.datasource.unpooled.UnpooledDataSourceFactory` 可被用作父类来构建新的数据源适配器，比如下面这段插入 C3P0 数据源所必需的代码：

```java
import org.apache.ibatis.datasource.unpooled.UnpooledDataSourceFactory;
import com.mchange.v2.c3p0.ComboPooledDataSource;

public class C3P0DataSourceFactory extends UnpooledDataSourceFactory {

  public C3P0DataSourceFactory() {
    this.dataSource = new ComboPooledDataSource();
  }
}
```

为了令其工作，记得在配置文件中为每个希望 MyBatis 调用的 setter 方法增加对应的属性。 下面是一个可以连接至 PostgreSQL 数据库的例子：

```xml
<dataSource type="org.myproject.C3P0DataSourceFactory">
  <property name="driver" value="org.postgresql.Driver"/>
  <property name="url" value="jdbc:postgresql:mydb"/>
  <property name="username" value="postgres"/>
  <property name="password" value="root"/>
</dataSource>
```

## databaseIdProvider （数据库厂商标识）

MyBatis 可以根据不同的数据库厂商执行不同的语句，这种多厂商的支持是基于映射语句中的 databaseId 属性。 MyBatis 会加载带有匹配当前数据库 databaseId 属性和所有不带 databaseId 属性的语句。 如果同时找到带有 databaseId 和不带 databaseId 的相同语句，则后者会被舍弃。 为支持多厂商特性，只要像下面这样在 mybatis-config.xml 文件中加入 databaseIdProvider 即可：

```xml
<databaseIdProvider type="DB_VENDOR" />
```

databaseIdProvider 对应的 DB_VENDOR 实现会将 databaseId 设置为 `DatabaseMetaData#getDatabaseProductName()` 返回的字符串。 由于通常情况下这些字符串都非常长，而且相同产品的不同版本会返回不同的值，你可能想通过设置属性别名来使其变短：

```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="SQL Server" value="sqlserver"/>
  <property name="DB2" value="db2"/>
  <property name="Oracle" value="oracle" />
</databaseIdProvider>
```

在提供了属性别名时，databaseIdProvider 的 DB_VENDOR 实现会将 databaseId 设置为数据库产品名与属性中的名称第一个相匹配的值，如果没有匹配的属性，将会设置为 “null”。 在这个例子中，如果 getDatabaseProductName() 返回“Oracle (DataDirect)”，databaseId 将被设置为“oracle”。

你可以通过实现接口 org.apache.ibatis.mapping.DatabaseIdProvider 并在 mybatis-config.xml 中注册来构建自己的 DatabaseIdProvider：

```java
public interface DatabaseIdProvider {
  default void setProperties(Properties p) { // 从 3.5.2 开始，该方法为默认方法
    // 空实现
  }
  String getDatabaseId(DataSource dataSource) throws SQLException;
}
```

## mappers （映射器）

```xml
<!--
  使用相对于类路径的资源引用
-->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
<!--
  使用完全限定资源定位符（URL）
-->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
<!--
  使用映射器接口实现类的完全限定类名
-->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
<!--
  将包内的映射器接口实现全部注册为映射器 ,必须满足两个条件
  1. mapper 接口和映射文件所在的包必须一致
  2. mapper 接口的名字和映射文件的名字必须一致
-->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

# mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">

</mapper>
```

```java
package org.mybatis.example;

public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

## 获取参数的两种方式

### 占位符 #{} 和 字符串替换 ${}

1. `#{}` 占位符,会对字符串\日期类型自动加上单引号
2. `${}` 字符串替换,原样替换,有 sql 注入的风险,最好用来替换字段名; 注意单引号问题;常用于模糊查询|in|表名
3. 单个字面量参数,`(String params)` #{} ${} 里面填任何值都能获取参数
4. 多个字面量类型,
   1. 此时 mybatis 会将参数放到 map 集合中,以两种方式存储数据
      1. 以 arg0,arg1,...为键,以参数为值
      2. 以 param1,param2,...为键,以参数为值
      3. 使用时,通过 #{arg0} 或 #{params1} 获取相应的值
   2. 自定义键
      1. 将多个参数放入自定义的 map 中,将 map 作为唯一参数
      2. 使用#{} ${} 通过自定义的键来访问 map 中的数据,如 ${key1}
5. 单个实体类类型参数,使用#{}${} 通过属性名访问(属性名: getter 和 setter 方法去掉 get\set 后首字母小写)
6. 使用注解 `@params` 自定义键 `@params("key1")`,会将参数放到 map 中,键为自定义,值为参数值;另外也可以通过#{param1}访问
7. 如果参数只有一个,且类型是 list 集合,则以 list 为键,参数为值.

### 高级参数类型

1. 基础类型 int , #{id}
2. 对象,如 User , #{id} 相当于 user.id
3. 特殊的数据类型 `#{property,javaType=int,jdbcType=NUMERIC,typeHandler=MyTypeHandler}`
   1. 和 MyBatis 的其它部分一样，几乎总是可以根据参数对象的类型确定 javaType，除非该对象是一个 HashMap。这个时候，你需要显式指定 javaType 来确保正确的类型处理器（TypeHandler）被使用。
   2. JDBC 要求，如果一个列允许使用 null 值，并且会使用值为 null 的参数，就必须要指定 JDBC 类型（jdbcType）。阅读 PreparedStatement.setNull()的 JavaDoc 来获取更多信息。
   3. 数值类型,设置 numericScale 指定小数点后保留的位数 `#{height,javaType=double,jdbcType=NUMERIC,numericScale=2}`
   4. mode 属性允许你指定 IN，OUT 或 INOUT 参数。如果参数的 mode 为 OUT 或 INOUT，将会修改参数对象的属性值，以便作为输出参数返回。 如果 mode 为 OUT（或 INOUT），而且 jdbcType 为 CURSOR（也就是 Oracle 的 REFCURSOR），你必须指定一个 resultMap 引用来将结果集 ResultMap 映射到参数的类型上。要注意这里的 javaType 属性是可选的，如果留空并且 jdbcType 是 CURSOR，它会被自动地被设为 ResultMap。`#{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet, resultMap=departmentResultMap}`
   5. MyBatis 也支持很多高级的数据类型，比如结构体（structs），但是当使用 out 参数时，你必须显式设置类型的名称。比如（再次提示，在实际中要像这样不能换行）：`#{middleInitial, mode=OUT, jdbcType=STRUCT, jdbcTypeName=MY_TYPE, resultMap=departmentResultMap}`

## 查询的几种情况

1. 根据接口函数的返回值来调用内部的方法
   1. 单个对象调用`selectOne()`
   2. List `selectList()`
   3. Map `SelectMap()`
2. 若 sql 语句禅熏的结果为多条时,已经不能以实体类类型作为方法的返回值,否则会抛出异常 `TooManyResultsException`;但是,查询结果只有一条或没有时,可以用 List 接收
3. 查询一条数据为 map
4. 查询单个字段
5. 查询多条数据
   1. `List<Map<String,Object>>`
   2. 也可以用 `Map<String,Object>` 接收,但是接口上需要`@MapKey("列名")`注解,返回 `map{列名={记录},.....}`

### 模糊查询

1. `like %${param1}%` 不能使用#{} 只能使用${}
2. `like contact('%',#{},'%')`使用 mysql 字符串拼接函数
3. `like "%"#{}"%"` 使用双引号

## resultType – 结果类型

```xml
<select id="selectUsers" resultType="map">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
<select id="selectUsers" resultType="com.someapp.model.User">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
<typeAlias type="com.someapp.model.User" alias="User"/>
<select id="selectUsers" resultType="User">
  select
    user_id             as "id", <!--User属性名-->
    user_name           as "userName",
    hashed_password     as "hashedPassword"
  from some_table
  where id = #{id}
</select>
```

## resultMap – 自定义映射,处理一对多\多对多的映射关系

- `constructor` - 用于在实例化类时，注入结果到构造方法中
  - `idArg` - ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
    1. `column` 数据库字段名
    2. `javaType` java 类的全限定名
    3. `jdbcType` JDBC 类型
    4. `typeHandler` 覆盖默认的类型处理器,属性值是一个类型处理器实现类的全限定名，或者是类型别名
    5. `select` 用于加载复杂类型属性的映射语句的 ID
    6. `resultMap` 结果映射的 ID
    7. `name` 构造方法形参的名字
- `arg` - 将被注入到构造方法的一个普通结果

- `discriminator`
  - `case`

### 基本使用

```xml
<!--
  resultMap: 自定义映射,处理一对多\多对多的映射关系
    id	当前命名空间中的一个唯一标识，用于标识一个结果映射。
    type	类的完全限定名, 或者一个类型别名（关于内置的类型别名，可以参考上面的表格）。
    autoMapping	如果设置这个属性，MyBatis 将会为本结果映射开启或者关闭自动映射。 这个属性会覆盖全局的属性 autoMappingBehavior。默认值：未设置（unset）。
-->
<resultMap id="userResultMap" type="User">
  <!--
    `id` 标记主键
        1. `property` 属性名
        2. `column` 数据库字段名,必须是sql语句的查询字段
        3. `javaType` java类的全限定名
        4. `jdbcType` JDBC 类型
        5. `typeHandler` 覆盖默认的类型处理器,属性值是一个类型处理器实现类的全限定名，或者是类型别名
   `result` 标记普通字段,属性与id相同

   `jdbcType`:
      BIT	FLOAT	CHAR	TIMESTAMP	OTHER	UNDEFINED
      TINYINT	REAL	VARCHAR	BINARY	BLOB	NVARCHAR
      SMALLINT	DOUBLE	LONGVARCHAR	VARBINARY	CLOB	NCHAR
      INTEGER	NUMERIC	DATE	LONGVARBINARY	BOOLEAN	NCLOB
      BIGINT	DECIMAL	TIME	NULL	CURSOR	ARRAY
  -->
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
<select id="selectUsers" resultMap="userResultMap">
  select user_id, user_name, hashed_password
  from some_table
  where id = #{id}
</select>
```

### 级联处理多对一

多对一关系

```java
public class User {
    private Integer id;
    private String name;
    private Integer age;
}
public class Teacher {
    private Integer id;
    private String name;
    private Integer userId;
    private User user;
}
```

```xml
<resultMap id="teancherAndUserMap" type="com.lxh.entity.Teacher">
    <id property="id" column="id"></id>
    <result property="name" column="name"></result>
    <result property="userId" column="user_id"></result>
    <result property="user.id" column="user_id"></result>
    <result property="user.name" column="user_name"></result>
    <result property="user.age" column="user_age"></result>
</resultMap>
<select id="selectTeancherAndUser" resultMap="teancherAndUserMap">
    select teacher.*,user.name as user_name,user.age as user_age
    from teacher
    left join user
    on teacher.user_id = user.id
    where teacher.id = #{teacherId}
</select>
```

### association 处理多对一关系

```xml
<resultMap id="teancherAndUserMap" type="com.lxh.entity.Teacher">
    <id property="id" column="id"></id>
    <result property="name" column="name"></result>
    <result property="userId" column="user_id"></result>
    <!--
      association 处理处理多对一的映射关系(处理实体类类型的属性)
      property: 设置需要处理映射关系额属性的属性名
      javaType: 设置要处理的属性的类型
    -->
    <association property="user" javaType="com.lxh.entity.User">
      <result property="id" column="user_id"></result>
      <result property="name" column="user_name"></result>
      <result property="age" column="user_age"></result>
    </association>
</resultMap>
<select id="selectTeancherAndUser" resultMap="teancherAndUserMap">
    select teacher.*,user.name as user_name,user.age as user_age
    from teacher
    left join user
    on teacher.user_id = user.id
    where teacher.id = #{teacherId}
</select>
```

### 分步查询处理多对一

```xml
<!--
  分步查询 处理多对一
  1. 根据 id 查询teacher
  2. 根据 teacher 中的 userId 查询 user
-->
<resultMap id="teancherAndUserMapStepOne" type="com.lxh.entity.Teacher">
    <id property="id" column="id"></id>
    <result property="name" column="name"></result>
    <result property="userId" column="user_id"></result>
    <!--
      property: 查询的实体对象
      select: 第二步查询的方法的全限定名
      column: 将第一步查询结果的某个字段作为第二不查询方法的参数
      fetchType: lazy(开启延迟加载)|eager(立即加载);在开启了延迟加载的环境中,通过该属性设置是否开启延迟加载
    -->
    <association property="user"
      select="com.lxh.mapper.UserMapper.selectTeancherAndUserMapStepTwo"
      column="user_id"
      fetchType="lazy"></association>
</resultMap>
<select id="selectTeancherAndUser" resultMap="teancherAndUserMapStepOne">
    select *
    from teacher
    where teacher.id = #{teacherId}
</select>
<select id="selectTeancherAndUserMapStepTwo" resultType="com.lxh.entity.User">
    select * from user where id = #{userId}
</select>
```

分布查询的优点: 可以实现延迟加载

```xml
<!--全局配置-->
<!-- 延迟加载的全局开关 -->
<setting name="lazyLoadingEnabled" value="true"/>
<!-- 按需加载 true:任何方法的调用都会加载改对象的所有属性,否则,每个属性会按需加载 -->
<setting name="aggressiveLazyLoading" value="false"/>
```

按需加载后,如果值需要 teacher 的属性而不需要 user 的属性,则只会进行第一步的查询

### collection 处理一对多关系(处理集合类型的属性)

```java
public class User {
    private Integer id;
    private String name;
    private Integer age;
    /**
     * 一对多关系
     */
    private List<Teacher> teachers;
}
public class Teacher {
    private Integer id;
    private String name;
    private Integer userId;
    private User user;
}
```

```xml
<resultMap id="selectUserAndTeacherMap" type="com.lxh.entity.User">
    <id column="id" property="id"></id>
    <result column="name" property="name"></result>
    <result column="age" property="age"></result>
    <!-- ofType: 设置集合类型的属性中存储的数据的类型 -->
    <collection property="teachers" ofType="com.lxh.entity.Teacher">
        <id column="teacher_id" property="id"></id>
        <result column="teacher_name" property="name"></result>
        <result column="teacher_user_id" property="userId"></result>
    </collection>
</resultMap>
<select id="selectUserAndTeacher" resultMap="selectUserAndTeacherMap">
    select user.*,teacher.id as teacher_id ,teacher.name as teacher_name,teacher.user_id as teacher_user_id
    from user
    left join teacher
    on user.id = teacher.user_id
    where user.id = #{userId}
</select>
```

### 分步查询处理一对多

```xml
<!--
  1. 查询 user
  2. 查询 teacher
-->
<resultMap id="selectUserAndTeacherByStepOne" type="com.lxh.entity.User">
    <id column="id" property="id"></id>
    <result column="name" property="name"></result>
    <result column="age" property="age"></result>
    <collection property="teachers"
        select="com.lxh.mapper.TeacherMapper.selectUserAndTeacherByStepTwo"
        colume="id"
        fetchType="lazy"></collection>
</resultMap>
<select id="selectUserAndTeacherByStepOne" resultMap="selectUserAndTeacherByStepOne">
    select *
    from user
    where user.id = #{userId}
</select>
<select id="selectUserAndTeacherByStepTwo" resultType="com.lxh.entity.Teacher">
    select *
    from teacher
    where user_id = #{userId}
</select>
```

## sql – 可被其它语句引用的可重用语句块

```xml
<!--
  sql: 用来定义可重用的 SQL 代码片段，以便在其它语句中使用。
    id:

  include: 引入 sql 片段
    refid: sql id

    property 标签: 参数赋值
      alias: 参数名
      value: 参数值
-->

<sql id="userColumns">
  ${alias}.id,${alias}.username,${alias}.password
</sql>
<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>

也可以在 include 元素的 refid 属性或内部语句中使用属性值，例如：

<sql id="sometable">
  ${prefix}Table
</sql>

<sql id="someinclude">
  from
    <include refid="${include_target}"/>
</sql>

<select id="select" resultType="map">
  select
    field1, field2, field3
  <include refid="someinclude">
    <property name="prefix" value="Some"/>
    <property name="include_target" value="sometable"/>
  </include>
</select>
```

## insert,update, delete– 映射插入\更新\删除语句

属性 描述
`id` 在命名空间中唯一的标识符，可以被用来引用这条语句。
`parameterType` 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。
`parameterMap` 用于引用外部 parameterMap 的属性，目前已被废弃。请使用行内参数映射和 parameterType 属性。
`flushCache` 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：（对 insert、update 和 delete 语句）true。
`timeout` 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。
`statementType` 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。
`useGeneratedKeys` （仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false。
`keyProperty` （仅适用于 insert 和 update）指定能够唯一识别对象的属性，MyBatis 会使用 getGeneratedKeys 的返回值或 insert 语句的 `selectKey` 子元素设置它的值，默认值：未设置（unset）。如果生成列不止一个，可以用逗号分隔多个属性名称。
`keyColumn` （仅适用于 insert 和 update）设置生成键值在表中的列名，在某些数据库（像 PostgreSQL）中，当主键列不是表中的第一列的时候，是必须设置的。如果生成列不止一个，可以用逗号分隔多个属性名称。
`databaseId` 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有不带 databaseId 或匹配当前 databaseId 的语句；如果带和不带的语句都有，则不带的会被忽略。

```xml
<insert id="insertAuthor">
  insert into Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

<update id="updateAuthor">
  update Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>

<delete id="deleteAuthor">
  delete from Author where id = #{id}
</delete>

<!-- 数据插入后,获取自增主键,保存到参数中keyProperty -->
<insert id="insertAuthor" useGeneratedKeys="true" keyProperty="id">
  insert into Author (username,password,email,bio)
  values (#{username},#{password},#{email},#{bio})
</insert>

<!--  传入 Author 数组或集合 ,循环插入多组数据 -->
<insert id="insertAuthor" useGeneratedKeys="true" keyProperty="id">
  insert into Author (username, password, email, bio) values
  <foreach item="item" collection="list" separator=",">
    (#{item.username}, #{item.password}, #{item.email}, #{item.bio})
  </foreach>
</insert>

<!--随机生成id-->
<insert id="insertAuthor">
  <!--首先会运行 selectKey 元素中的语句，并设置 Author 的 id，然后才会调用插入语句-->
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    select CAST(RANDOM()*1000000 as INTEGER) a from SYSIBM.SYSDUMMY1
  </selectKey>
  insert into Author
    (id, username, password, email,bio, favourite_section)
  values
    (#{id}, #{username}, #{password}, #{email}, #{bio}, #{favouriteSection,jdbcType=VARCHAR})
</insert>
```

`selectKey` 元素的属性
属性 描述
`keyProperty` selectKey 语句结果应该被设置到的目标属性。如果生成列不止一个，可以用逗号分隔多个属性名称。
`keyColumn` 返回结果集中生成列属性的列名。如果生成列不止一个，可以用逗号分隔多个属性名称。
`resultType` 结果的类型。通常 MyBatis 可以推断出来，但是为了更加准确，写上也不会有什么问题。MyBatis 允许将任何简单类型用作主键的类型，包括字符串。如果生成列不止一个，则可以使用包含期望属性的 Object 或 Map。
`order` 可以设置为 BEFORE 或 AFTER。如果设置为 BEFORE，那么它首先会生成主键，设置 keyProperty 再执行插入语句。如果设置为 AFTER，那么先执行插入语句，然后是 selectKey 中的语句 - 这和 Oracle 数据库的行为相似，在插入语句内部可能有嵌入索引调用。
`statementType` 和前面一样，MyBatis 支持 STATEMENT，PREPARED 和 CALLABLE 类型的映射语句，分别代表 Statement, PreparedStatement 和 CallableStatement 类型。

## select – 映射查询语句

属性:

1. `id` 在命名空间中唯一的标识符，可以被用来引用这条语句。
2. `parameterType` 将会传入这条语句的参数的类全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。
3. `parameterMap` 用于引用外部 parameterMap 的属性，目前已被废弃。请使用行内参数映射和 parameterType 属性。
4. `resultType` 期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 resultType 和 resultMap 之间只能同时使用一个。
5. `resultMap` 对外部 resultMap 的命名引用。结果映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂的映射问题都能迎刃而解。 resultType 和 resultMap 之间只能同时使用一个。
6. `flushCache` 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false。
7. `useCache` 将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true。
8. `timeout` 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）。
9. `fetchSize` 这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值。 默认值为未设置（unset）（依赖驱动）。
10. `statementType` 可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。
11. `resultSetType` FORWARD_ONLY，SCROLL_SENSITIVE, SCROLL_INSENSITIVE 或 DEFAULT（等价于 unset） 中的一个，默认值为 unset （依赖数据库驱动）。
12. `databaseId` 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有不带 databaseId 或匹配当前 databaseId 的语句；如果带和不带的语句都有，则不带的会被忽略。
13. `resultOrdered` 这个设置仅针对嵌套结果 select 语句：如果为 true，将会假设包含了嵌套结果集或是分组，当返回一个主结果行时，就不会产生对前面结果集的引用。 这就使得在获取嵌套结果集的时候不至于内存不够用。默认值：false。
14. `resultSets` 这个设置仅适用于多结果集的情况。它将列出语句执行后返回的结果集并赋予每个结果集一个名称，多个名称之间以逗号分隔。

```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```

## 动态 sql

### if

```xml
<!--
  test: 判断是否有效,
  连接
-->
where 1=1
<if test="直接引用参数名,不需要#{}; and or">
and xxx
</if>
```

### where

`where` 元素只会在子元素返回任何内容的情况下才插入 `WHERE` 子句。
而且，若子句的开头为 `AND` 或 `OR` ,`where` 元素也会将它们去除。

```xml
<where>
  <if test="state != null">
        state = #{state}
  </if>
  <if test="title != null">
      AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
      AND author_name like #{author.name}
  </if>
</where>
```

### trim

如果 where 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：

```xml
<!--
  prefixOverrides: 在内容前面删除 指定内容属性会忽略通过管道符分隔的文本序列（注意此例中的空格是必要的）。
  prefix: 在内容前面加上 指定内容
  suffix: 在内容后面加上 指定内容
  suffixOverrides: 在内容后面删除 指定内容
-->
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

### choose (when, otherwise)

```xml
<choose>
  <when test="title != null">
    AND title like #{title}
  </when>
  <when test="author != null and author.name != null">
    AND author_name like #{author.name}
  </when>
  <otherwise>
    AND featured = 1
  </otherwise>
</choose>
```

### set 动态更新

用于动态更新语句的类似解决方案叫做 set。set 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

这个例子中，set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。

来看看与 set 元素等价的自定义 trim 元素吧：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

注意，我们覆盖了后缀值设置，并且自定义了前缀值。

### foreach 批量操作

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <!--
    collection: list 类型参数
    item: 集合中的每一条数据
    index: item 在 collection 的索引
    open: 循环的所用内容以什么开始
    close: 循环的所用内容以什么结束
    separator: 每次循环之间的分隔符
  -->
  <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
    #{item}
  </foreach>
</select>
```

foreach 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！

提示 你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象作为集合参数传递给 foreach。
当使用可迭代对象或者数组时，`index` 是当前迭代的序号，`item` 的值是本次迭代获取到的元素。当使用 `Map` 对象（或者 Map.Entry 对象的集合）时，`index` 是键，`item` 是值。

### script

要在带注解的映射器接口类中使用动态 SQL，可以使用 script 元素。比如:

```java
    @Update({"<script>",
      "update Author",
      "  <set>",
      "    <if test='username != null'>username=#{username},</if>",
      "    <if test='password != null'>password=#{password},</if>",
      "    <if test='email != null'>email=#{email},</if>",
      "    <if test='bio != null'>bio=#{bio}</if>",
      "  </set>",
      "where id=#{id}",
      "</script>"})
    void updateAuthorValues(Author author);
```

### bind

bind 元素允许你在 OGNL 表达式以外创建一个变量，并将其绑定到当前的上下文。比如：

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

### 多数据库支持

如果配置了 databaseIdProvider，你就可以在动态代码中使用名为 “\_databaseId” 的变量来为不同的数据库构建特定的语句。比如下面的例子：

```xml
<insert id="insert">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    <if test="_databaseId == 'oracle'">
      select seq_users.nextval from dual
    </if>
    <if test="_databaseId == 'db2'">
      select nextval for seq_users from sysibm.sysdummy1"
    </if>
  </selectKey>
  insert into users values (#{id}, #{name})
</insert>
```

### 动态 SQL 中的插入脚本语言

MyBatis 从 3.2 版本开始支持插入脚本语言，这允许你插入一种语言驱动，并基于这种语言来编写动态 SQL 查询语句。

可以通过实现以下接口来插入一种语言：

```java
public interface LanguageDriver {
  ParameterHandler createParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql);
  SqlSource createSqlSource(Configuration configuration, XNode script, Class<?> parameterType);
  SqlSource createSqlSource(Configuration configuration, String script, Class<?> parameterType);
}
```

实现自定义语言驱动后，你就可以在 mybatis-config.xml 文件中将它设置为默认语言：

```xml
<typeAliases>
  <typeAlias type="org.sample.MyLanguageDriver" alias="myLanguage"/>
</typeAliases>
<settings>
  <setting name="defaultScriptingLanguage" value="myLanguage"/>
</settings>
或者，你也可以使用 lang 属性为特定的语句指定语言：

<select id="selectBlog" lang="myLanguage">
  SELECT * FROM BLOG
</select>
```

或者，在你的 mapper 接口上添加 @Lang 注解：

```java
public interface Mapper {
  @Lang(MyLanguageDriver.class)
  @Select("SELECT * FROM BLOG")
  List<Blog> selectBlog();
}
```

提示 可以使用 Apache Velocity 作为动态语言，更多细节请参考 MyBatis-Velocity 项目。

你前面看到的所有 xml 标签都由默认 MyBatis 语言提供，而它由语言驱动 org.apache.ibatis.scripting.xmltags.XmlLanguageDriver（别名为 xml）所提供。

## 转义字符

```
<   &lt;
>   &gt;
<=  &lt;=
>=  &gt;=
&   &amp;
'   &apos;
"   &quot;
```

# 数据库字段映射

## 字段名与属性名不一致

1. 查询语句中,给查询字段名起别名
2. 当数据库字段符合数据库的命名规范(`_`),java 符合 java 的命名规范(小驼峰)时,可以通过配置核心配置文件将 (`_`)映射为小驼峰: emp_id +> empId

```xml
<settings>
  <!-- 将下划线映射为驼峰 -->
  <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

3. 使用 resultMap 自定义映射来实现

# 日志 log4j

1. 导包
2. 配置文件`log4j.xml`
3. 使用

```xml
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.2.17</version>
</dependency>


<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m(%F: %L) \n"/>
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug"></level>
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info"></level>
    </logger>
    <root>
        <level value="debug"></level>
        <appender-ref ref="STDOUT"/>
    </root>
</log4j:configuration>

```

# 缓存

## 一级缓存(默认开启)

一级缓存是 SqlSession 级别的,通过同一个 SqlSession 查询的数据会被缓存,下次查询相同的数据,就会从缓存中直接获取,不会从数据库重新访问.

使一级缓存失效的四种情况:

1. 不同的 SqlSession 对应不同的一级缓存
2. 同一个 SqlSession 但是查询条件不同
3. 同一个 SqlSession 两次查询期间执行了任何一次增删改操作,会清空缓存
4. 同一个 SqlSession 两次查询期间手动清空了缓存 `SqlSession.clearCache()`

## 二级缓存

二级缓存是 SqlSessionFactory 级别,通过同一个 SqlSessionFactory 创建的 SqlSession 查询的结果会被缓存;此后若再次执行相同的查询语句,结果就会从缓存中获取.

二级缓存开启的条件:

1. 在核心配置文件中,数值全局配置属性 cacheEnabled="true" ,默认为 true ,不需要设置
2. 在映射文件中设置标签 `<cache />`
3. 二级缓存脾虚在 SqlSession 关闭或提交之后有效
4. 查询的数据所转化的实体类类型必须实现序列化接口

二级缓存失效的情况:

1. 两次查询之间执行了任意的增删改,会使一级和二级缓存同时失效

相关配置:
在 mapper 配置文件中添加的 cache 标签可以设置一些属性

1. eviction : 缓存回收策略,默认为 LRU(least recently used);
   LRU – 最近最少使用：移除最长时间不被使用的对象。
   FIFO – 先进先出：按对象进入缓存的顺序来移除它们。
   SOFT – 软引用：基于垃圾回收器状态和软引用规则移除对象。
   WEAK – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。
2. flushInterval 刷新间隔 ms;默认不设置,不会自动刷新,知道使用一次增删改
3. size 引用数目,正整数,使用默认值即可,需要防止内存溢出
4. readOnly 默认 FALSE ,返回缓存的拷贝;为 true 时返回缓存的引用,速度较快,但不能修改

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>

```

## 缓存查询顺序

1. 先查询二级缓存
2. 二级缓存没有命中,查询一级缓存
3. 一级缓存没有命中,查询数据库

注意,SqlSession 关闭后,一级缓存中的数据才会保存到二级缓存中

## 整合第三方缓存 ehcache (二级缓存)

```xml
<!-- ehcache 整合包 -->
<dependency>
  <groupId>org.mybatis.cache</groupId>
  <artifactId>mybatis-ehcache</artifactId>
  <version>1.2.1</version>
</dependency>
<!-- slf4j 日志门面的一个具体实现  -->
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-classic</artifactId>
  <version>1.2.3</version>
</dependency>
```

配置文件 ehcache.xml

```xml
<?xmi version=1.0 encoding=utf-8 ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmi:noNamespaceSchemaLocation="../config/ehcache.xsd">
  <!-- 磁盘保存路径 -->
  <diskStore path="D:\temp\cacahe" />
  <defaultCache
    maxElementsInMemory="1000"
    maxElementsOnDisk="100000000"
    eternal="false"
    overflowToDisk="true"
    timeToIdleSeconds="120"
    timeToLiveSeconds="120"
    diskExpiryTheadIntervalSeconds="120"
    memoryStoreEvictionPolicy="LRU"
  ></defaultCache>
</ehcache>
```

在 mapper.xml 中设置二级缓存的类型

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

logback 配置文件 https://logback.qos.ch/documentation.html
存在 SLF4J 时,作为建议日志的 log4j 将失效,此时我们需要借助 SLF4j 的具体实现 logback 来打印日志,创建 logback 的配置文件 logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!--控制台日志， 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!--按照顺序: 时间,日志级别,线程名称,打印日志类,日志主体内容,换行 -->
            <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread]  [%logger] [%msg] %n</pattern>
        </encoder>
    </appender>
    <!-- 日志输出级别  DEBUG INFO WARN ERROR -->
    <root level="DEBUG">
        <appender-ref ref="STDOUT" />
    </root>

    <!-- 根据特殊要求指定局部日志级别 -->
    <logger name="com.atguigu.crowd.mapper" level="DEBUG">
</configuration>
```

使用时没有区别!!!

# 逆向工程

正向工程: 先创建 java 实体类,由框架负责根据实体类生成数据库表. Hibernate 是支持正向工程的
逆向工程: 项城建数据库表,由框架负责根据数据库表,反向生成如下资源:

- java 实体类
- Mapper 接口
- Mapper 映射文件

依赖:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.0</version>
            <dependencies>
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core</artifactId>
                    <version>1.3.2</version>
                </dependency>
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.22</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org/DTD Mybatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
        targetRuntime: 版本
        MyBatis3: 全面带参的CURD
        MyBatis3sSimple: 基本的CURD
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--数据库连接信息-->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql//localhost:3306/mubatis?serverTimezone=UTC"
                        userId="root"
                        password="123456"
        ></jdbcConnection>
        <!--实体类生成策略-->
        <javaModelGenerator targetPackage="com.lxh.entity" targetProject="./src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- mapper.xml 生成策略-->
        <sqlMapGenerator targetPackage="com.lxh.mapper" targetProject="./src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- mapper 接口-->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.lxh.mapper" targetProject="./src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 逆向分析的表
        tableName: 设置为 *,可以对应所有表,此时不写 domainObjectName
        domainObjectName: 指定生成的实体类的类名
        -->
        <table tableName="t_emp" domainObjectName="Emp"></table>
        <table tableName="t_dept" domainObjectName="Dept"></table>
    </context>
</generatorConfiguration>

```

# 分页

## 准备

**1. 添加依赖**

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
```

**2. 配置核心配置文件**

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

原理:

1. 先查询 `count(0)` 获取总数
2. 在拼接 `limit `

## 使用

```java
mapper = SQLSession.getMapper(xx.class);
// 在mapper定义后,在查询语句之前,设置分页
Page<Object> page = PageHelper.startPage(1,4); // 第一页,每页4条数据
      // page 包含一部分分页数据
List<Entity> list = mapper.selectByExample(null);
// 查询之后,可以通过 PageInfo 获取分页相关的所有数据
PageInfo<Entity> pageInfo = new PageInfo<>(list,5); // 5 : 前端分页导航显示的页码(3,4,5,6,7)
list.forEach(System.out::println)
```

# api

## sqlSession 接口

1. sql 增删查改( 不常用,常用 `getMapper()` 来获取 mapper 来调用 sql)
2. 事务管理 `commit() rollback()`
3. 获取配置信息 `getConfiguration()`
4. 获取连接 `getConnection()`
5. 关闭会话 `close()`
6. 清理会话缓存 `clearCache()`
7. 获取 Mapper 对象 `getMapper()`

```java
int insert(String statement);
int insert(String statement, Object parameter);

// statement : mapper接口的完全限定名.函数名(mapper.xml id)
// parameter : 接口使用的参数
```

实现类获取:

1. 加载配置文件 `InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");`
2. 生成工厂类对象 `SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);`
3. 获取实现类对象 `SqlSession sqlSession = sqlSessionFactory.openSession(true);`
