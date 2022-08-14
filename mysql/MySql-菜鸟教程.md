# 第一章 MySql安装

## Linux/UNIX 上安装 MySQL

安装前，我们可以检测系统是否自带安装 MySQL:
```mysql
rpm -qa | grep mysql
```

如果你系统有安装，那可以选择进行卸载:
```mysql
rpm -e mysql　　// 普通删除模式
rpm -e --nodeps mysql　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除
```

安装 MySQL：
```mysql
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
yum install mysql-server
```

权限设置：
```mysql
chown -R mysql:mysql /var/lib/mysql/
```

> 如果报错：invalid user: ‘mysql:mysql’，执行如下命令
```mysql
chattr -ia /etc/passwd
chattr -ia /etc/group
chattr -ia /etc/shadow
chattr -ia /etc/gshadow

yum install mysql-server
chown -R mysql:mysql /var/lib/mysql
```



初始化 MySQL：
```mysql
mysqld --user=root --initialize
```

启动 MySQL：
```mysql
systemctl start mysqld
```

查看 MySQL 运行状态：
```mysql
systemctl status mysqld
```

## 验证 MySQL 安装结果

查看 MySQL 服务器版本：
```mysql
[root@host]# mysqladmin --version
```

linux上该命令将输出以下结果，该结果基于你的系统信息：
```mysql
mysqladmin  Ver 8.42 Distrib 5.6.51, for Linux on x86_64
```

## 使用 MySQL Client(Mysql客户端) 执行简单的SQL命令
```mysql
[root@host]# mysql
```

> 说明：你可以在 MySQL Client(Mysql客户端) 使用 mysql 命令连接到 MySQL 服务器上，默认情况下 MySQL 服务器的登录密码为空，所以本实例不需要输入密码。
> 直接使用mysql命令登录服务器时，用户名是空的，即 ''@'localhost'

## Mysql安装后需要做的
```mysql
[root@host]# mysqladmin -u root password "new_password";
```

> 说明：使用mysqladmin命令，给root用户，通过password关键字，设置了一个新的密码 
> 说明：首次登陆无密码，在输入密码环节，直接回车即可，此后root 用户的密码就已经是 "new_password"

```mysql
[root@host]# mysqladmin -u root "old_passorod" password "new_password";
```

> 说明：使用mysqladmin命令，给root用户，通过password关键字，更换了一个新的密码 

注意：
mysql ---> 连接mysql服务器，而不是服务器上的数据库

mysqladmin ----> 管理mysql服务器，比如修改账号密码，关闭/开启mysql服务器

# 第二章 MySql管理

## 启动及关闭 MySQL 服务器

开启mysql服务器
```mysql
root@host# cd /usr/bin
./mysqld_safe &
```

关闭mysql服务器
```mysql
root@host# cd /usr/bin
./mysqladmin -u root -p shutdown
Enter password: ******
```

## MySQL 用户设置

### 方式1 在user表中插入数据
```mysql
root@host# mysql -u root -p
mysql> INSERT INTO user 
    -> (host, user, password, 
    -> select_priv, insert_priv, update_priv) 
    -> VALUES ('localhost', 'guest', 
    -> PASSWORD('guest123'), 'Y', 'Y', 'Y');
mysql> FLUSH PRIVILEGES;    
```

> 说明：上述方式报错，`ERROR 1364 (HY000): Field 'ssl_cipher' doesn't have a default value`，即user表中有一些字段没有缺省值，导致无法insert 成功；
> 需要额外使用命令：`FLUSH PRIVILEGES;`，否则需要重启mysql服务器后，guest才能登录mysql服务器。 

### 方式2 使用grant 命令

```mysql
mysql> grant all privileges on *.* to guest@localhost identified by 'guest';
mysql> select user();
mysql> select database();
```

> 说明，上述方式直接生效，即guest用户可以立即登录mysql服务器；后可以查看当前用户；

```mysql
mysql> select database();
+------------+
| database() |
+------------+
| NULL       |
+------------+
1 row in set (0.00 sec)
```
> 说明：如果没有use + 数据库，则`select database();` 将展示 NULL
> select 后面加方法，所以方法后面必须要有()，表明这是一个方法；而`show database; show tables;` 则无需加()

# 第三章 创建/删除数据库

## 创建数据库

### 方法1
```mysql
root@host# mysql -u root -p
mysql> create database if not exists lm_test_db1;
```

创建的时候，可以加上`if not exists`，即如果不存在才创建

### 方法2
```mysql
root@host# mysqladmin -u root -p create lm_test_db_2;
```

创建的时候，不能加上`if not exists`，即没那么灵活；并且，是 `create` 而不是 `create database`


## 删除数据库

### 方法1
```mysql
root@host# mysql -u root -p
mysql> drop database lm_test_db1;
```

### 方法2
```mysql
root@host# mysqladmin -u root -p drop lm_test_db_2;
```

注意，是 `drop ` 而不是 `drop database`

# 第四章 数据类型

## 数值类型

类型 | 大小 | 范围（有符号） | 范围（无符号）| 用途
---|---|---|---|---
TINYINT | 1 Bytes |	(-128，127)	| (0，255) |	小整数值
SMALLINT | 2 Bytes | (-32 768，32 767) | (0，65 535) | 大整数值
MEDIUMINT | 3 Bytes | (-8 388 608，8 388 607) | (0，16 777 215) | 大整数值
INT或INTEGER | 4 Bytes | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295) | 大整数值
BIGINT | 8 Bytes | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807) | (0，18 446 744 073 709 551 615) | 极大整数值
FLOAT | 4 Bytes | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 单精度浮点数值
DOUBLE | 8 Bytes | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度浮点数值
DECIMAL | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值 | 依赖于M和D的值 | 小数值

## 日期和时间

类型 | 大小（Bytes） | 范围（有符号） | 范围（无符号）| 用途
---|---|---|---|---
DATE | 3 | 1000-01-01/9999-12-31 | YYYY-MM-DD | 日期值
TIME | 3 | '-838:59:59'/'838:59:59' | HH:MM:SS | 时间值或持续时间
YEAR | 1 | 1901/2155 | YYYY	年份值
DATETIME | 8 | '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59' | YYYY-MM-DD hh:mm:ss | 混合日期和时间值
TIMESTAMP | 4 | '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC，结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYY-MM-DD hh:mm:ss | 混合日期和时间值，时间戳

## 字符串类型
类型 | 大小（Bytes） | 用途
---|---|---
CHAR | 0-255 bytes | 定长字符串
VARCHAR | 0-65535 bytes | 变长字符串
TINYBLOB | 0-255 bytes | 不超过 255 个字符的二进制字符串
TINYTEXT | 0-255 bytes | 短文本字符串
BLOB | 0-65 535 bytes | 二进制形式的长文本数据
TEXT | 0-65 535 bytes | 长文本数据
MEDIUMBLOB | 0-16 777 215 bytes | 二进制形式的中等长度文本数据
MEDIUMTEXT | 0-16 777 215 bytes | 中等长度文本数据
LONGBLOB | 0-4 294 967 295 bytes | 二进制形式的极大文本数据
LONGTEXT | 0-4 294 967 295 bytes | 极大文本数据


注意：char(n) 和 varchar(n) 中括号中 n 代表字符的个数，并不代表字节个数，比如 CHAR(30) 就可以存储 30 个字符。
CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。


# 第五章 创建/删除数据库表

## 创建表
```mysql
root@host# mysql -u root -p
Enter password:*******
mysql> use mysql;
Database changed
mysql> CREATE TABLE runoob_tbl(
   -> runoob_id INT NOT NULL AUTO_INCREMENT,
   -> runoob_title VARCHAR(100) NOT NULL,
   -> runoob_author VARCHAR(40) NOT NULL,
   -> submission_date DATE,
   -> PRIMARY KEY ( runoob_id )
   -> )ENGINE=InnoDB DEFAULT CHARSET=utf8;
 ```
 
 注意：use mysql == use database mysql。列名字后面先跟数据类型，再跟默认值，再跟其他；主键可以由多列组成；默认字符集 = utf8
 
## 删除表
```mysql
root@host# mysql -u root -p
Enter password:*******
mysql> use RUNOOB;
Database changed
mysql> DROP TABLE runoob_tbl;
mysql>
```

# 第六章 添加/删除/更新/查询

## 插入数据 
### 插入1条数据
```mysql
mysql> INSERT INTO table_name  (field1, field2,...fieldN)
    -> VALUES
    -> (valueA1,valueA2,...valueAN);
```

说明：mysql语句，以符号 `;`作为命令结束符，即只有遇到符号 `;`才会提交执行sql预计

### 插入多条数据
```mysql
mysql> INSERT INTO table_name  (field1, field2,...fieldN)  
    -> VALUES
    -> (valueA1,valueA2,...valueAN),
    -> (valueB1,valueB2,...valueBN),
    -> (valueC1,valueC2,...valueCN);
```

说明：多条数据

### 如果所有列都要添加数据，可以不规定列进行添加数据：
```mysql
INSERT INTO table_name VALUES  (valueA1,valueA2,...valueAN);
```
