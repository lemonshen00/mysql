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
mysql> drop database lm_test_db1;
```

### 方法2
```mysql
root@host# mysqladmin -u root -p drop lm_test_db_2;
```

注意，是 `drop ` 而不是 `drop database`
