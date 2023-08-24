---
title: manjaro安装phpmyadmin
tags: []
id: '736'
categories:
  - - LINUX
  - - system
date: 2022-09-04 00:43:42
---

1～6 为环境搭建

7～9 为phpmyadmin的正式安装

## 1.更新系统

```shell
yay -Syu
```

## 2.安装**Apache**

```shell
yay -S apache
```

借助kate编辑httpd.conf（也可用vi，vim，nano）

```shell
kate /etc/httpd/conf/httpd.conf
```

注释 `LoadModule unique_id_module modules/mod_unique_id.so`

![Screenshot_20220903_231444](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_231444.png)

设置开机启动，并重新启动服务、检查服务运行状况，保证

```shell
systemctl enable httpd
systemctl restart httpd 
```

​ 正常：Active: **active (running)**

![Screenshot_20220903_231728](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_231728.png)

​ 异常：Active: **failed**

![Screenshot_20220903_232005](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_232005.png)

出现这种情况分析最后几行错误提示查找原因，每次修改需执行：

```shell
systemctl restart httpd
systemctl status httpd
```

## 3.测试Apache

创建页面

```shell
kate /srv/http/index.html
```

_index.html_

```html
<html>
 <title>Welcome</title>
  <body>
   <h2>Welcome to OSTechNix test page</h2>
  </body>
</html>
```

不出意外，出现以下内容：

```http
http://127.0.0.1/
```

![Screenshot_20220903_232840](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_232840.png)

## 4.安装测试mariadb

> [https://www.kozakemi.top/archives/733](https://www.kozakemi.top/archives/733)

## 5.安装PHP

```shell
yay -S php php-apache
```

编辑 `httpd.conf`

```shell
kate /etc/httpd/conf/httpd.conf
```

![Screenshot_20220903_233704](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_233704.png)

并添加以下内容_（注意，此处内容随PHP版本改变而变化_

_本机PHP版本：PHP Version 8.1.9）_

```python
LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
LoadModule php_module modules/libphp.so
AddHandler php-script php
Include conf/extra/php_module.conf
```

![Screenshot_20220903_233953](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_233953.png)

## 6.测试PHP

建立测试PHP文件

```shell
kate /srv/http/test.php
```

_test.php_

```
<?php
 phpinfo();
?>
```

重新启动httpd，检查修改是否有误

```shell
systemctl restart httpd
systemctl status httpd
```

如无误：

```http
http://127.0.0.1/test.php
```

![Screenshot_20220903_234529](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_234529.png)

## 7.安装phpmyadmin

```shell
yay -S phpmyadmin php-mcrypt
```

修改php.ini

```shell
kate /etc/php/php.ini
```

_php.ini_

添加：

```php
extension=mcrypt
```

修改：取消以下注释：

```php
extension=bz2
extension=mysqli
```

![Screenshot_20220903_235337](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_235337.png)

配置phpmyadmin

```
kate /etc/httpd/conf/extra/phpmyadmin.conf
```

添加以下内容：

```html
Alias /phpmyadmin "/usr/share/webapps/phpMyAdmin"
 <Directory "/usr/share/webapps/phpMyAdmin">
  DirectoryIndex index.php
  AllowOverride All
  Options FollowSymlinks
  Require all granted
 </Directory>
```

编辑 Apache的配置文件

```shell
kate /etc/httpd/conf/httpd.conf
```

添加：

```html
/etc/httpd/conf/httpd.conf
```

![Screenshot_20220903_235802](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220903_235802.png)

再次重新启动httpd，检查修改是否有误

```shell
systemctl restart httpd
systemctl status httpd
```

## 8.测试phpMyAdmin

```http
http://127.0.0.1/phpmyadmin/
```

![Screenshot_20220904_000139](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220904_000139.png)

输入密码，访问成功：

![Screenshot_20220904_000339](https://kozakemi.oss-cn-beijing.aliyuncs.com/Screenshot_20220904_000339.png)