# jdbc

什么是 JDBC？JDBC 是 Java DataBase Connectivity 的缩写，它是 Java 程序访问数据库的标准接口。

使用 Java 程序访问数据库时，Java 代码并不是直接通过 TCP 连接去访问数据库，而是通过 **JDBC 接口**来访问，而 JDBC 接口则通过 **JDBC 驱动**来实现真正对数据库的访问。

jdbc 驱动:

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
    <!-- 注意到这里添加依赖的scope是runtime，因为编译Java程序并不需要MySQL的这个jar包，只有在运行期才需要使用。如果把runtime改成compile，虽然也能正常编译，但是在IDE里写程序的时候，会多出来一大堆类似com.mysql.jdbc.Connection这样的类，非常容易与Java标准库的JDBC接口混淆，所以坚决不要设置为compile -->
    <scope>runtime</scope>
</dependency>
```

# 数据库连接

使用 JDBC 时，我们先了解什么是 Connection。Connection 代表一个 JDBC 连接，它相当于 Java 程序到数据库的连接（通常是 TCP 连接）。打开一个 Connection 时，需要准备 URL、用户名和口令，才能成功连接到数据库。

URL 是由数据库厂商指定的格式，例如，MySQL 的 URL 是：`jdbc:mysql://<hostname>:<port>/<db>?key1=value1&key2=value2`

假设数据库运行在本机 localhost，端口使用标准的 3306，数据库名称是 learnjdbc，那么 URL 如下：`jdbc:mysql://localhost:3306/learnjdbc?useSSL=false&characterEncoding=utf8`

```java
// JDBC连接的URL, 不同数据库有不同的格式:
String JDBC_URL = "jdbc:mysql://localhost:3306/test";
String JDBC_USER = "root";
String JDBC_PASSWORD = "password";
// 获取连接,并自动关闭数据库
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    // 数据库自动操作
}
```

# 查询

获取到 JDBC 连接后，下一步我们就可以查询数据库了。查询数据库分以下几步：

1. 通过 `Connection` 提供的 `createStatement()`方法创建一个 `Statement` 对象，用于执行一个查询；
2. 执行 `Statement` 对象提供的 `executeQuery("SELECT * FROM students")` 并传入 SQL 语句，执行查询并获得返回的结果集，使用 `ResultSet` 来引用这个结果集；
3. 反复调用 `ResultSet` 的 `next()`方法并读取每一行结果。

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
  try (Statement stmt = conn.createStatement()) {
    try (ResultSet rs = stmt.executeQuery("SELECT id, grade, name, gender FROM students WHERE gender=\'M\'")) {
      // rs.next()用于判断是否有下一行记录，如果有，将自动把当前行移动到下一行（一开始获得ResultSet时当前行不是第一行）；
      while (rs.next()) {
          long id = rs.getLong(1); // 注意：索引从1开始
          long grade = rs.getLong(2);
          String name = rs.getString(3);
          String gender = rs.getString(4);
      }
    }
  }
}
```

`Statment` 和 `ResultSet` 都是需要关闭的资源，因此嵌套使用 `try (resource)`确保及时关闭

# SQL 注入

使用 Statement 拼字符串非常容易引发 SQL 注入的问题，这是因为 SQL 参数往往是从方法参数传入的。

要避免 SQL 注入攻击，一个办法是针对所有字符串参数进行转义，但是转义很麻烦，而且需要在任何使用 SQL 的地方增加转义代码。

还有一个办法就是使用 `PreparedStatement` ,使用 `PreparedStatement` 可以完全避免 SQL 注入的问题，因为 PreparedStatement 始终使用`?`作为占位符，并且把数据连同 SQL 本身传给数据库，这样可以保证每次传给数据库的 SQL 语句是相同的，只是占位符的数据不同，还能高效利用数据库本身对查询的缓存。

```java
String sql = "SELECT * FROM user WHERE login=? AND pass=?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setObject(1, name);
ps.setObject(2, pass);
```

PreparedStatement 比 Statement 更安全，而且更快

# 数据类型

使用 JDBC 的时候，我们需要在 Java 数据类型和 SQL 数据类型之间进行转换。JDBC 在 java.sql.Types 定义了一组常量来表示如何映射 SQL 数据类型，但是平时我们使用的类型通常也就以下几种：

```java
SQL数据类型       Java 数据类型
BIT, BOOL         boolean
INTEGER           int
BIGINT            long
REAL              float
FLOAT, DOUBLE     double
CHAR, VARCHAR     String
DECIMAL           BigDecimal
DATE              java.sql.Date, LocalDate
TIME              java.sql.Time, LocalTime

注意：只有最新的 JDBC 驱动才支持 LocalDate 和 LocalTime
```

# 更新

数据库操作总结起来就四个字：增删改查，行话叫 CRUD：Create，Retreive，Update 和 Delete。

## 插入

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    try (PreparedStatement ps = conn.prepareStatement(
            "INSERT INTO students (id, grade, name, gender) VALUES (?,?,?,?)")) {
        ps.setObject(1, 999); // 注意：索引从1开始
        ps.setObject(2, 1); // grade
        ps.setObject(3, "Bob"); // name
        ps.setObject(4, "M"); // gender
        int n = ps.executeUpdate(); // 1 插入的记录数量
    }
}
```

如果数据库的表设置了自增主键，那么在执行 INSERT 语句时，并不需要指定主键，数据库会自动分配主键。对于使用自增主键的程序，有个额外的步骤，就是如何获取插入后的自增主键的值。

要获取自增主键，不能先插入，再查询。因为两条 SQL 执行期间可能有别的程序也插入了同一个表。获取自增主键的正确写法是在创建 PreparedStatement 的时候，指定一个 RETURN_GENERATED_KEYS 标志位，表示 JDBC 驱动必须返回插入的自增主键。

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    try (PreparedStatement ps = conn.prepareStatement(
            "INSERT INTO students (grade, name, gender) VALUES (?,?,?)",
            Statement.RETURN_GENERATED_KEYS)) { //获取自增主键的正确写法是在创建PreparedStatement的时候，指定一个RETURN_GENERATED_KEYS标志位，表示JDBC驱动必须返回插入的自增主键。
        ps.setObject(1, 1); // grade
        ps.setObject(2, "Bob"); // name
        ps.setObject(3, "M"); // gender
        int n = ps.executeUpdate(); // 1
        try (ResultSet rs = ps.getGeneratedKeys()) {
            if (rs.next()) {
                long id = rs.getLong(1); // 注意：索引从1开始
            }
        }
    }
}

```

## 更新

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD) ){
    try (PreparedStatement ps = conn.prepareStatement("UPDATE students SET name=? WHERE id=?")) {
        ps.setObject(1, "Bob"); // 注意：索引从1开始
        ps.setObject(2, 999);
        int n = ps.executeUpdate(); // 返回更新的行数
    }
}
```

## 删除

```java
try (Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD)) {
    try (PreparedStatement ps = conn.prepareStatement("DELETE FROM students WHERE id=?")) {
        ps.setObject(1, 999); // 注意：索引从1开始
        int n = ps.executeUpdate(); // 删除的行数
    }
}
```

## 批量

```java
try (PreparedStatement ps = conn.prepareStatement("INSERT INTO students (name, gender, grade, score) VALUES (?, ?, ?, ?)")) {
    // 对同一个PreparedStatement反复设置参数并调用addBatch():
    for (String name : names) {
        ps.setString(1, name);
        ps.setBoolean(2, gender);
        ps.setInt(3, grade);
        ps.setInt(4, score);
        ps.addBatch(); // 添加到batch
    }
    // 执行batch:
    int[] ns = ps.executeBatch();
    for (int n : ns) {
        System.out.println(n + " inserted."); // batch中每个SQL执行的结果数量
    }
}

```

# 事务

```java
Connection conn = openConnection();
// 设定隔离级别为READ COMMITTED:
conn.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);
try {
    // 关闭自动提交:
    conn.setAutoCommit(false);
    // 执行多条SQL语句:
    insert(); update(); delete();
    // 提交事务:
    conn.commit();
} catch (SQLException e) {
    // 回滚事务:
    conn.rollback();
} finally {
    conn.setAutoCommit(true);
    conn.close();
}
```

# jdbc 连接池

在执行 JDBC 的增删改查的操作时，如果每一次操作都来一次打开连接，操作，关闭连接，那么创建和销毁 JDBC 连接的开销就太大了。为了避免频繁地创建和销毁 JDBC 连接，我们可以通过连接池（Connection Pool）复用已经创建好的连接。

JDBC 连接池有一个标准的接口 javax.sql.DataSource，注意这个类位于 Java 标准库中，但仅仅是接口。要使用 JDBC 连接池，我们必须选择一个 JDBC 连接池的实现。常用的 JDBC 连接池有：HikariCP C3P0 BoneCP Druid

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>2.7.1</version>
</dependency>
```

创建 HikariCP 线程池

```java
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:mysql://localhost:3306/test");
config.setUsername("root");
config.setPassword("password");
config.addDataSourceProperty("connectionTimeout", "1000"); // 连接超时：1秒
config.addDataSourceProperty("idleTimeout", "60000"); // 空闲超时：60秒
config.addDataSourceProperty("maximumPoolSize", "10"); // 最大连接数：10
DataSource ds = new HikariDataSource(config);
// 注意创建DataSource也是一个非常昂贵的操作，所以通常DataSource实例总是作为一个全局变量存储，并贯穿整个应用程序的生命周期。
```

使用

```Java
try (Connection conn = ds.getConnection()) { // 在此获取连接
    ...
} // 在此“关闭”连接
```

通过连接池获取连接时，并不需要指定 JDBC 的相关 URL、用户名、口令等信息，因为这些信息已经存储在连接池内部了（创建 HikariDataSource 时传入的 HikariConfig 持有这些信息）。一开始，连接池内部并没有连接，所以，第一次调用 ds.getConnection()，会迫使连接池内部先创建一个 Connection，再返回给客户端使用。当我们调用 conn.close()方法时（在 try(resource){…}结束处），不是真正“关闭”连接，而是释放到连接池中，以便下次获取连接时能直接返回。

# 连接数据库

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>
```

```Java
// MySQL 8.0 以下版本 - JDBC 驱动名及数据库 URL
static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB";
// MySQL 8.0 以上版本 - JDBC 驱动名及数据库 URL
//static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";
//static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC";

// 数据库的用户名与密码，需要根据自己的设置
static final String USER = "root";
static final String PASS = "123456";

public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        try{
            // 注册 JDBC 驱动
            Class.forName(JDBC_DRIVER);

            // 打开链接
            System.out.println("连接数据库...");
            conn = DriverManager.getConnection(DB_URL,USER,PASS);

            // 执行查询
            System.out.println(" 实例化Statement对象...");
            stmt = conn.createStatement();
            String sql;
            sql = "SELECT id, name, url FROM websites";
            ResultSet rs = stmt.executeQuery(sql);

            // 展开结果集数据库
            while(rs.next()){
                // 通过字段检索
                int id  = rs.getInt("id");
                String name = rs.getString("name");
                String url = rs.getString("url");

                // 输出数据
                System.out.print("ID: " + id);
                System.out.print(", 站点名称: " + name);
                System.out.print(", 站点 URL: " + url);
                System.out.print("\n");
            }
            // 完成后关闭
            rs.close();
        }catch(SQLException se){
            // 处理 JDBC 错误
            se.printStackTrace();
        }catch(Exception e){
            // 处理 Class.forName 错误
            e.printStackTrace();
        }finally{
            // 关闭资源
            try{
                if(stmt!=null) stmt.close();
            }catch(SQLException se2){
            }// 什么都不做
            try{
                if(conn!=null) conn.close();
            }catch(SQLException se){
                se.printStackTrace();
            }
        }
        System.out.println("Goodbye!");
    }
```

# DriverManager 类

此类用于装载驱动程序，它所有的成员都是静态成员，所以在程序中无须对它进行实例化，直接通过类名就可以访问它。
DriverManager 类是 JDBC 的管理层，作用于用户和驱动程序间加载驱动程序

```java
Class.forName("公司名.数据库名.驱动程序名")
//如：
Class.forName("sun.jdbc.odbc.jdbcOdbcDriver")
```

**建立连接**
加载 Driver 类并在 DriverManager 类注册后，就可用来与数据库建立连接。当调用 Driver.Manager.getConnection()发出连连接请求时，DriverManager 将检查每个驱动程序，看它是否可以建立连接。
方法：Connection getConnection(String url,String user,String password)
尝试建立与给定数据库 URL 的连接。

如：以下是通常用驱动程序（JDBC-ODBC 桥驱动程序），并连一个 student 数据源，用匿名登录的的示例：

```java
Class.forName("sun.jdbc.odbc.jdbcOdbcDriver");//加载驱动程序
String url="jdbc:odbc:student";
Connection cn=DriverManager.getConnection(url,"anonymous","");

//GetConnection()：返回一个连接类对象。若成功，此对象就指向此数据库的一个连接；否则，此对象将为空 null
```

# Connection 类

作用：管理指向数据库的连接，如：向数据库发送查询和接收数据库的查询结果都是在它基础上的；完成同数据库的连接的所有任务之后关闭此连接。

```java
createStatment()：新建一个 Statement 对象，此对象可以向数据库发送查询信息
void close()：关闭同数据库的连接并释放占有的 JDBC 资源
Boolean isClose()：判断是否仍与数据库连接
```

# Statement sql 语句类

Statement 对象用于将 SQL 语句发送到数据库中。

```java
ResultSet executeQuery(String sql)：用于产生单个结果集的语句，如：select 语句
int executeUpdate()：用于执行 insert、update 或 delete、语句等，返回值是一个整数，指示受影响的行数（即更新计数）
int execute(String sql)：用于执行返回多个结果集、多个更新计数或二者组合的语句

关闭 Statement 对象
Statement 对象将由 Java 垃圾收集程序自动关闭。但我们最好显示地关闭它们，因为会立即释放数据管理系统资源，有助避免潜在内存问题。
close()
```

## PreparedStatement 防注入 sql 语句

```Java
PreparedStatement pstmt = con.prepareStatement("UPDATE EMPLOYEES  SET SALARY = ? WHERE ID = ?");
pstmt.setBigDecimal(1, 153833.00);
pstmt.setInt(2, 110592) ;
int result = pstmt.executeUpdate();
```

# ResultSet 结果集

```Java
ResultSet rs = stmt.executeQuery(sql);

// 展开结果集数据库
while(rs.next()){
    // 通过字段检索
    int id  = rs.getInt("id");
    String name = rs.getString("name");
    String url = rs.getString("url");

    // 输出数据
    System.out.print("ID: " + id);
    System.out.print(", 站点名称: " + name);
    System.out.print(", 站点 URL: " + url);
    System.out.print("\n");
}
// 立即释放此 ResultSet对象的数据库和JDBC资源，而不是等待它自动关闭时发生。
rs.close();
```
