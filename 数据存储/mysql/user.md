# dcl 操作用户权限

- **dcl（data control language）**：数据控制语言，用来定义访问权限和安全级别。

## 1. 创建用户

```sql
create user 用户名@指定ip identified by 密码;

create user 用户名@客户端ip identified by 密码; #指定ip才能登陆

create user 用户名@‘% ’ identified by 密码; #任意ip均可登陆
```

## 2. 用户授权

```sql
grant 权限1,权限2,........,权限n on 数据库名.* to 用户名@ip; #给指定用户授予指定指定数据库指定权限

grant all on . to 用户名@ip; 给指定用户授予所有数据库所有权限

```

> 权限 ： select,insert,update,delete,create
>
> 数据库名.\*：该数据库的所有表

## 3. 用户权限查询

```sql
show grants for 用户名@ip;
```

## 4. 撤销用户权限

```sql
revoke 权限1,权限2,........,权限n on 数据库名.* from 用户名@ip;
```

## 5. 删除用户

```sql
drop user 用户名@ip;
```

## 修改用户密码

```sql
alter user 'root'@'localhost' identified with mysql_native_password by '新密码';
alter user 'root'@'localhost' identified with mysql_native_password by '123456';
```
