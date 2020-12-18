# mysql常用命令
mysql是非常常用的关系型数据库，很多时候需要用到mysql的命令，这里记录一些常用的命令。
主要是一次部署mysql用到的命令。

## 启动和停止
本次使用的是mysqld_safe
mysqld_safe相当与一个守候进程，当mysql挂掉的时候会自动把mysql拉起
```shell
admin    30700  0.0  0.0 106168  1556 pts/2    S    Dec17   0:00 /bin/sh /usr/bin/mysqld_safe --defaults-file=/etc/my.cnf --user=root
admin    30839  0.1  0.8 4695412 141584 pts/2  Sl   Dec17   2:07 /usr/libexec/mysqld --defaults-file=/etc/my.cnf --basedir=/usr --datadir=/var/lib/mysql --user=root
```
可以使用下面的命令进行连接
```
 mysql --defaults-file=/etc/my.cnformysql -S /tmp/mysql.sock
 mysql -P 3388 -u root -p
```
要停止mysql时需要先将守护进程停止，不然mysql停止后会被守候进程拉起来

## create命令
### 创建用户
`create user 'name'@'addr' identified by 'password'`
name表示用户名；addr表示这个用户可以被访问的范围 %表示全部，localhost表示本地；password表示这个用户的密码
`flush privileges` 在用户创建完成后刷新授权

### 创建数据库
`create database DBName DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`
DBNmame表示数据库名；DEFAULT CHARSET 表示默认的字符集； COLLATE：校对集

## 数据库连接
当数据库连不上的时候是需要修改用户的网络访问
首先看配置中的`bind-address`参数是不是127.0.0.1，修改这个然后重启服务
也可以在命令行修改：
```
登陆mysql
查看user表的信息
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| domino             |
| mysql              |
| test               |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql;
Database changed
mysql> select user,host from user;
+---------+-------------------------------+
| user    | host                          |
+---------+-------------------------------+
| editest | %                             |
| root    | %                             |
| server  | %                             |
| root    | 127.0.0.1                     |
| root    |  localhost                    |
| root    | localhost                     |
| server  | localhost                     |
+---------+-------------------------------+
9 rows in set (0.00 sec)
grant all privileges on *.* to 'root'@'[允许的ip]' identified by '[密码]' with grant option;
flush privileges;
```
使用grant进行修改，并刷新配置。

## 查看表
查看表结构
- desc table;
- describe table;
- show columns from table;
查看建表语句信息
show create table info;这样会比较乱在后面使用G替换
show create table info\G

## sql文件的导入和导出
### 导出
导出数据和表结构： mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
只导出表结构：mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql

### 导入
进入数据库中
```language
1、首先建空数据库
mysql>create database abc;
```
方法一、
```language
（1）选择数据库
mysql>use abc;
（2）设置数据库编码
mysql>set names utf8;
（3）导入数据（注意sql文件的路径）
mysql>source /home/abc/abc.sql;
```
方法二
```
mysql -u用户名 -p密码 数据库名 < 数据库名.sql
```

























