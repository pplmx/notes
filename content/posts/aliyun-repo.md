---
categories:
    - general
date: 2020-04-18T20:02:12Z
description: Some aliyun repo sources
keywords: pip source, rpm repo, centos, ubuntu
lastmod: 2026-08-14T00:00:00Z
tags:
    - repo
    - pip
    - centos
    - ubuntu
title: Some repos in aliyun
---



# aliyun repo

## pip Repo

 ```bash
# ====== linux ======
mkdir ~/.pip
cat > ~/.pip/pip.conf << EOF
[global]
index-url=https://mirrors.aliyun.com/pypi/simple/
extra-index-url=https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/

[list]
format=columns

EOF
# ====== windows ======
mkdir $APPDATA/pip
touch $APPDATA/pip/pip.ini
# or (This is a legacy configuration)
mkdir $HOME/pip
touch $HOME/pip/pip.ini
 ```

## centos8 Repo

> ⚠️ **EOL**: CentOS 8 reached end-of-life on 2021-12-31. The repo URLs below only work against the archived (vault) mirror (`https://mirrors.aliyun.com/centos-vault/`), so this config is for legacy boxes only. For a maintained RHEL-compatible distro use **AlmaLinux** or **Rocky Linux** (aliyun mirrors: `https://mirrors.aliyun.com/almalinux/`, `https://mirrors.aliyun.com/rockylinux/`), or **Alibaba Cloud Linux / Anolis OS**.

```
[AppStream]
name=CentOS-$releasever - aliyun - AppStream
baseurl=https://mirrors.aliyun.com/centos/$releasever/AppStream/$basearch/os/
gpgcheck=0
enabled=1

[BaseOS]
name=CentOS-$releasever - aliyun - Base
baseurl=https://mirrors.aliyun.com/centos/$releasever/BaseOS/$basearch/os/
gpgcheck=0
enabled=1

[extras]
name=CentOS-$releasever - aliyun - extras
baseurl=https://mirrors.aliyun.com/centos/$releasever/extras/$basearch/os/
gpgcheck=0
enabled=1

[centosplus]
name=CentOS-$releasever - aliyun - centosplus
baseurl=https://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/os/
gpgcheck=0
enabled=1

[HighAvailability]
name=CentOS-$releasever - aliyun - HighAvailability
baseurl=https://mirrors.aliyun.com/centos/$releasever/HighAvailability/$basearch/os/
gpgcheck=0
enabled=1

[PowerTools]
name=CentOS-$releasever - aliyun - PowerTools
baseurl=https://mirrors.aliyun.com/centos/$releasever/PowerTools/$basearch/os/
gpgcheck=0
enabled=1

[epel]
name=CentOS-$releasever - aliyun - epel
baseurl=https://mirrors.aliyun.com/epel/$releasever/Everything/$basearch/
gpgcheck=0
enabled=1
```

## ubuntu18.04 Repo

> ⚠️ **EOL**: Ubuntu 18.04 "bionic" standard support ended on 2023-05-31 (ESM via Ubuntu Pro only). For a maintained install, replace `bionic` in the lines below with the current LTS codename, e.g. `noble` (24.04 LTS, supported to 2029) or the newer 26.04 LTS (released 2026-04). The `mirrors.aliyun.com/ubuntu/` path itself is unchanged.

```
deb https://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
```

