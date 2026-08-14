---
categories:
    - linux
date: 2017-10-13T10:31:14Z
description: install tomcat on linux
keywords: tomcat, linux, centos7
lastmod: 2026-08-14T00:00:00Z
tags:
    - java
    - linux
    - centos
title: linux服务器初建之tomcat安装
---



# 安装tomcat

> 注意：本文基于 CentOS 7 上 yum 安装的 Tomcat 7（2017 年）。Tomcat 7 已于 2021-03 停止维护（EOL），CentOS 7 也已于 2024-06 EOL。新建环境建议改用最新 Tomcat LTS（10.1.x 或 11.x）的二进制 tarball 或容器镜像（如 `tomcat:11`），并用 systemd（`systemctl`）管理服务。manager-gui 与 tomcat-users.xml 的配置思路仍然成立。

## 安装tomcat

```bash
sudo yum install tomcat
```

## 安装管理包

```bash
sudo yum install tomcat-webapps tomcat-admin-webapps
```

<!-- more -->

## 安装在线文档

```bash
sudo yum install tomcat-docs-webapp tomcat-javadoc
```

## 配置tomcat管理页面

```bash
sudo vi /usr/share/tomcat/conf/tomcat-users.xml
```

```text
<tomcat-users>
    <user username="admin" password="admin" roles="manager-gui,admin-gui"/>
</tomcat-users>
```

## 重启

```bash
sudo service tomcat restart
```

## 访问试试

```text
http://yourIP:8080
http://yourIP:8080/manager/html
```

## 相关文章

- [linux服务器初建之java环境](https://blog.yoooo.fun/build-server-java.html)
- [linux服务器初建之mysql安装](https://blog.yoooo.fun/build-server-mysql.html)
- [linux服务器初建之zsh安装](https://blog.yoooo.fun/build-server-zsh.html)
