---
categories:
    - linux
date: 2017-10-13T08:33:23Z
description: install mysql 5.7 on centos 7 (historical, EOL); use mysql 8.4 LTS for new systems
keywords: mysql, linux, mysql5.7, centos
lastmod: 2026-08-14T00:00:00Z
tags:
    - mysql
    - linux
    - centos
title: linux服务器初建之mysql安装
---



# 安装mysql

> **注意（2026 更新）：** 本文是 2017 年针对 CentOS 7 + MySQL 5.7 的历史教程，两者均已 EOL（MySQL 5.7 于 2023-10 停止支持，CentOS 7 于 2024-06 停止维护），仅作参考。新服务器请改用 MySQL 8.4 LTS：仓库初始化包为 `mysql84-community-release-el9-4.noarch.rpm`（RHEL/CentOS 9 及以上请到 https://dev.mysql.com/downloads/repo/yum/ 选择对应平台），并按 8.x 的认证与密码重置方式操作（见下文）。

## 下载mysql的repo源

```bash
$ wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

> 提示：`dev.mysql.com/get/` 仍会 302 跳转到 `repo.mysql.com`，旧包文件还能获取，但它只适配 EOL 的 el7 + MySQL 5.7。当前 LTS 是 MySQL 8.4，仓库初始化包为 `mysql84-community-release-{el7|el8|el9}-*.noarch.rpm`，请按系统选择。

## 安装mysql的rpm包

```bash
$ sudo rpm -ivh mysql57-community-release-el7-11.noarch.rpm
```

## 安装mysql

```bash
$ sudo yum install mysql-server
```

<!-- more -->

## 重置mysql密码

```bash
$ mysql -u root
```

登录时有可能报这样的错：ERROR 2002 (HY000): Can‘t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock‘
原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：

```bash
$ sudo chown -R root:root /var/lib/mysql
```

重启mysql服务

```bash
$ service mysqld restart
```

接下来是登录mysql(有两种情况)

### 直接登录成功

```bash
$ mysql -u root  //直接回车进入mysql控制台
mysql > use mysql;
mysql > update user set password=password('123456') where user='root';
mysql > exit;
```

> 注意：`PASSWORD()` 函数与 `mysql_native_password` 认证插件已在 MySQL 8.0+ 移除（改为 `caching_sha2_password`），上述 5.7 写法仅适用于本教程版本。8.x 请用 `ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';` 重置密码。

### 登录失败

登录失败:是因为密码错误,不是默认的空密码,
而是在安装时,mysql默认分配了随机密码
如果你的安装信息是详细显示的,那么你是可以在之前的安装信息中,找到随机密码
找不到,那就继续如下操作:

1.修改MySQL的登录设置：

```bash
# vi /etc/my.cnf // 在[mysqld]的段中加上一句：skip-grant-tables
```

2.重新启动mysql,并登录(mysql5.7,password字段不存在了,而是authentication_string)

```bash
# service mysqld restart
# mysql -uroot -p//回车
mysql> use mysql;
mysql> update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';
mysql> flush privileges;
mysql> quit;
```

3.还原/etc/my.cnf(将skip-grant-tables删除)

4.重启mysql,即可使用新密码登录了

## 相关文章

- [linux服务器初建之java环境](https://blog.yoooo.fun/build-server-java.html)
- [linux服务器初建之tomcat安装](https://blog.yoooo.fun/build-server-tomcat.html)
- [linux服务器初建之zsh安装](https://blog.yoooo.fun/build-server-zsh.html)

