---
categories:
    - linux
date: 2017-10-13T09:48:25Z
description: install java on linux
keywords: java, linux, jdk, centos
lastmod: 2026-08-14T00:00:00Z
tags:
    - java
    - centos
title: linux服务器初建之java环境
---



# 安装JDK

如果命令显示无权限,请加sudo,或是进入root用户;

若是目录无权限访问,请给相关用户设置相应的访问权限-rwx

## 查看是否存在jdk环境

(根据需求决定是否卸载已存在的环境)

```bash
yum list installed |grep java
yum -y remove java-1.8.0-openjdk*  # 匹配所有以java-1.8.0-openjdk开头的文件,然后卸载
```

<!-- more -->

> ⚠️ **时代提示 (2017-era)**: 本文针对 CentOS 7 (已于 2024-06-30 EOL) 上的 JDK 8 (2014 发布,公共更新早已停止)。这里的 yum 命令仅适用于仍在运行的 CentOS 7 老机器。新装机建议安装**当前 LTS JDK**——截至 2026 年 8 月最新 LTS 是 **JDK 25** (2025-09 发布),也可用 **JDK 21**;例如 `yum install java-21-openjdk java-21-openjdk-devel` (CentOS 7 老机器可直接 `yum install java-17-openjdk*`)。

## 安装jdk

```bash
yum -y list java*  # 查看jdk软件包列表
yum install java-1.8.0-openjdk*  # 安装所有java程序
# 如果不是所有程序都需要,也可仅执行如下命令
yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel
java -version  # 查看jdk版本号
```

## 配置环境变量

```bash
vi /etc/profile # 编辑该文件
```

```text
# set java environment
# 推荐:自动推导 JAVA_HOME (不依赖具体 rpm 版本路径,升级后依然有效)
JAVA_HOME="$(readlink -f /usr/bin/java | sed 's:/bin/java::')"
# 旧写法示例 (2017 年某个具体 rpm 的路径,版本升级后即失效):
# JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.144-0.b01.el7_4.x86_64
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME  CLASSPATH  PATH
```

退出并保存

```bash
. /etc/profile   # 注意:那里是需要空格的
echo $JAVA_HOME  # 查看
echo $CLASSPATH  # 查看
```

## 相关文章

- [linux服务器初建之mysql安装](https://blog.yoooo.fun/build-server-mysql.html)
- [linux服务器初建之tomcat安装](https://blog.yoooo.fun/build-server-tomcat.html)
- [linux服务器初建之zsh安装](https://blog.yoooo.fun/build-server-zsh.html)
