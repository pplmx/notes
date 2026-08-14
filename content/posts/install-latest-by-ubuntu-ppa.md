---
categories:
    - linux
date: 2021-03-18T11:12:08Z
description: By PPA, to install the latest
keywords: apt, apt-get, git, jdk, gradle
lastmod: 2026-08-14T00:00:00Z
tags:
    - ubuntu
    - linux
title: Install the Latest Packages by Ubuntu PPA
---



# Ubuntu

## Use PPA source(Latest Version)

### Git

```bash
# after this, it will generate a new list in /etc/apt/sources.list.d
sudo add-apt-repository ppa:git-core/ppa

# change "http://ppa.launchpad.net" to "https://launchpad.proxy.ustclug.org"
# NOTE: USTCLUG PPA proxy is now retired (redirects to launchpad.net) and the
# generated filename embeds the release codename -- "focal" (20.04) here, but
# on a newer release it will be the current codename (e.g. noble for 24.04).
❯ ll /etc/apt/sources.list.d
.rw-r--r-- root root 137 B Fri Feb  5 17:49:07 2021 git-core-ubuntu-ppa-focal.list
❯ cat /etc/apt/sources.list.d/git-core-ubuntu-ppa-focal.list
deb https://launchpad.proxy.ustclug.org/git-core/ppa/ubuntu focal main

# install the latest git
sudo apt update
sudo apt install git
```

### Gradle

> ⚠️ `ppa:cwchien/gradle` is stale/unmaintained on modern Ubuntu. Today Gradle is normally installed via **SDKMAN!** (`sdk install gradle`) or the official distribution zip from `gradle.org`; the PPA below is kept as a historical example.

```bash
sudo add-apt-repository ppa:cwchien/gradle
```

### OpenJDK

> ⚠️ `ppa:openjdk-r/ppa` targeted old releases (14.04/16.04 era) and is obsolete — OpenJDK now ships in Ubuntu main repos, so just `sudo apt install openjdk-21-jdk` (LTS) or the current LTS.

```bash
sudo add-apt-repository ppa:openjdk-r/ppa
```

### Python

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
```

## Change the Default PPA Source

> ⚠️ **USTCLUG PPA mirror retired**: `https://launchpad.proxy.ustclug.org` has been retired (it now redirects to launchpad.net), so the host substitution below is historical. The default `http://ppa.launchpad.net` works in most regions; if a mirror is required, use Aliyun's apt mirror (`mirrors.aliyun.com/ubuntu/`) instead. The last two sed lines (replace http with https / use aliyun for archive.ubuntu.com) remain current best practice.

```bash
# historical: USTCLUG PPA proxy rename (proxy now retired, keep ppa.launchpad.net instead)
sed -i "s@http://ppa.launchpad.net@https://launchpad.proxy.ustclug.org@g" /etc/apt/sources.list.d/*.list

sed -i "s@http://mirrors.aliyun.com@https://mirrors.aliyun.com@g" /etc/apt/sources.list
sed -i "s@http://archive.ubuntu.com@https://mirrors.aliyun.com@g" /etc/apt/sources.list
```
