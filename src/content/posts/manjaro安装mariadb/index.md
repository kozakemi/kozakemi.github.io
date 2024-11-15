---
title: manjaro安装mariadb
published: 2022-09-04 00:41:51
tags: [Manjaro,mariadb,数据库]
category: Linux操作系统
draft: false
---

注意，前，最好设置linux的root用户密码，

标准/管理员用户下执行：

```shell
sudo passwd
```

来修改root用户密码通过：

```shell
su
```

命令，输入密码后进入root用户（超级用户）

除了步骤1 以外，其他命令在root中执行

## 1.安装mariadb

```shell
yay -S mariadb
```

检查安装情况：

```shell
mysqladmin version status 
```

我这里配置过，需要输入密码

![Screenshot_20220904_001301](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220904_001301.png)

## 2.配置mariadb

```shell
mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

### 启动mariadb

```shell
systemctl status mariadb
```

![Screenshot_20220904_001559](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220904_001559.png)

### 配置数据库

```shell
mysql_secure_installation
```

接下来准备好翻译工具，根据内容选择 y/n

粘下我的选择，按需选择，不必完全和我一样

```shell
[kemibaka-tm1707 kemibaka]# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] n
 ... skipping.

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] n
 ... skipping.

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] 
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] n
 ... skipping.

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

## 3.其它问题

### root无密码

连接数据库

```shell
mysql -uroot -p
```

你会发现，root用户下，输入密码也好，不输入密码也罢，都可登录，这在部分操作时会发生错误，我们应避免这个问题

依次执行，注意 ' ; ' 结尾

```sql
mysql -u root -p
flush privileges;
ALTER user 'root'@'localhost' IDENTIFIED BY 'root';
```

现在就可确保需密码登录

### 支持root用户允许远程连接数据库

一般来说，不要这样操作，危险，开发环境随意

```sql
grant all privileges on *.* to 'root'@'%' indentified by 'yourpassword' with grant option;
```

刷新权限

```sql
flush privileges;
```

## 4.mysql服务相关

### 停止mysql服务

```shell
systemctl stop mysqld.service  
```

### 重启mysql服务

```shell
systemctl restart mysqld.service  
```

### 查看mysql服务当前状态

```shell
systmctl status mysqld.service
```

### 设置mysql服务开机自启动

```shell
systemctl enable mysqld.service  
```

### 停止mysql服务开机自启动

```shell
systemctl disable mysqld.service 
```