# 版本

```go
// GORM 2.0
import (
  "gorm.io/gorm"
  "gorm.io/driver/sqlite"
)
```

# 文档

[官方文档](https://learnku.com/docs/gorm/v2/)

# 模型定义

​		模型是标准的 struct，由 Go 的基本数据类型、实现了 [Scanner](https://pkg.go.dev/database/sql/?tab=doc#Scanner) 和 [Valuer](https://pkg.go.dev/database/sql/driver#Valuer) 接口的自定义类型及其指针或别名组成。

## 1. 默认约定

​		GORM 倾向于约定，而不是配置。默认情况下，

- 使用 `ID` 作为主键
- 使用结构体名的 `蛇形复数` 作为表名
- 字段名(首字母大写)的 `蛇形` 作为列名
- 使用 `CreatedAt`、`UpdatedAt` 字段追踪创建、更新时间

> 蛇形法是全由小写字母和下划线组成，在两个单词之间用下滑线连接即可;
>
> 骆驼式命名法就是当变量名或函式名是由一个或多个单词连结在一起，而构成的唯一识别字时，第一个单词以小写字母开始；第二个单词的首字母大写或每一个单词的首字母都采用大写字母;

## 2. 自定义约定

### -主键

```yaml
主键： `gorm:"primaryKey"`
复合主键：多个属性同时增加标签，`gorm:"primaryKey"`
# 默认情况下，整型 PrioritizedPrimaryField 启用了 AutoIncrement，要禁用它，您需要为整型字段关闭 autoIncrement:false
关闭自增： `gorm:"primaryKey;autoIncrement:false"`
```

### -表名

您可以实现 `Tabler `接口来更改默认表名，例如：

```go
type Tabler interface {
    TableName() string
}

// TableName 会将 User 的表名重写为 `profiles`
func (User) TableName() string {
  return "profiles"
}
```

### -临时表名

# 连接数据库

GORM 官方支持的数据库类型有： MySQL, PostgreSQL, SQlite, SQL Server

## 1. `MySQL`

```go
db, err := gorm.Open(mysql.New(mysql.Config{
  DSN: "gorm:gorm@tcp(127.0.0.1:3306)/gorm?charset=utf8&parseTime=True&loc=Local", // DSN data source name
  DefaultStringSize: 256, // string 类型字段的默认长度
  DisableDatetimePrecision: true, // 禁用 datetime 精度，MySQL 5.6 之前的数据库不支持
  DontSupportRenameIndex: true, // 重命名索引时采用删除并新建的方式，MySQL 5.7 之前的数据库和 MariaDB 不支持重命名索引
  DontSupportRenameColumn: true, // 用 `change` 重命名列，MySQL 8 之前的数据库和 MariaDB 不支持重命名列
  SkipInitializeWithVersion: false, // 根据当前 MySQL 版本自动配置
}), &gorm.Config{})
```

