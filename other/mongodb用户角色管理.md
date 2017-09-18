## 认证

MongoDB使用`db.auth()`认证用户。

它支持以下几种认证机制：

- SCRAM-SHA-1
- MongoDB Challenge and Response (MONGODB-CR)
- x.509 Certificate Authentication.
- LDAP proxy authentication
- Kerberos authentication

## 用户管理

MongoDB使用`db.createUser()`来创建用户，在创建的同时还可以指定用户角色。

> 注意：第一个创建的用户必须是管理员，以便管理其他用户。

### 数据库认证

当创建用户时，需要指定进行认证的数据库，这个数据库是针对该用户的认证数据库。一个用户可以拥有操作多个数据库的权限，这是通过指定用户角色实现的。

用户名和认证数据库唯一确定一个用户，所以，如果两个用户的名字相同，但是在不同的数据库创建，则它们分别属于两个不同的用户。

### 用户认证

认证用户有两种方法：

- 通过命令行连接数据库时指定用户名和密码(-u, -p , --authenticationDatabase)，以及认证数据库
- 首先连接数据库实例，然后调用`authenticate`命令或者`db.auth()`方法进行认证。

