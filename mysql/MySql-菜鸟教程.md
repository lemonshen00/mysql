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
