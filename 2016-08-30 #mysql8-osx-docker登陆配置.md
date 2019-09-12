# Mysql8 ERROR info

## error info 1

```txt
mysql_exceptions.OperationalError 2003 Can't connect to MySQL server on 127.0.0.1

ERROR: {"error": "Connection failed!\n\nThe error message returned was:\n(_mysql_exceptions.OperationalError) (2059, \"
Authentication plugin 'caching_sha2_password' cannot be loaded: /usr/lib/x86_64-linux-gnu/mariadb18/plugin/caching_sha2_password.so:
cannot open shared object file: No such file or directory\") (Background on this error at: http://sqlalche.me/e/e3q8)"}
```

## error info 2

```txt

Unable to connect to host 127.0.0.1, or the request timed out.

Be sure that the address is correct and that you have the necessary privileges, or try increasing the connection timeout (currently 10 seconds).

MySQL said: Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(/usr/local/lib/plugin/caching_sha2_password.so, 2): image not found
```

### docker run mysql

```shell
docker run -it --rm mysql mysql -h127.0.0.1 -uroot -p

### if config my.cnf
docker exec -it mysql sh
vim /etc/mysql/my.cnf

```

### TRY TO FIX

```shell

### hosts:127.0.0.1:8088

mysql://root:123456@127.0.0.1:3306/superset?charset=utf8
```

#### caching_sha2_password to mysql_native_password

``` sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'zhangjiqiang';

ALTER USER 'root'@'localhost' IDENTIFIED BY 'zhangjiqiang' PASSWORD EXPIRE NEVER; #修改加密规则
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root'; #更新一下用户的密码
FLUSH PRIVILEGES; #刷新权限
```

#### create new mysql user

```sql
-- 创建新的用户：root
create user root@'%' identified WITH mysql_native_password BY 'root';
grant all privileges on *.* to root@'%' with grant option;
flush privileges;
-- 在MySQL8.0创建用户并授权的语句则不被支持：
grant all privileges on *.* to root@'%' identified by 'root' with grant option;
```

#### 创建新用户 develop

```sql
create user develop@'127.0.0.1' identified WITH mysql_native_password BY 'develop';
grant all privileges on *.* to develop@'127.0.0.1' with grant option;
flush privileges;

mysql -h127.0.0.1 -udevelop -p


-- 在MySQL8.0创建用户并授权的语句则不被支持：
grant all privileges on *.* to root@'%' identified by 'root' with grant option;
```

#### list current user config

```sql
select user, host, plugin, authentication_string from mysql.user\G;
```
