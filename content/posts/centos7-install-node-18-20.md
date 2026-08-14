---
categories:
    - linux
date: 2024-02-28T21:47:20Z
description: How to use node 18/20 on CentOS 7
keywords: node, 18, 20, glibc, linux, centos7, libstdc++
lastmod: 2026-08-14T00:00:00Z
tags:
    - linux
    - centos
title: Use node 18/20 on CentOS 7
---



# how to use node 18/20 on centos7

> **Note (2026-08-14):** era-specific workaround. Both node lines pinned here are now EOL: node 18 (2025-04-30) and node 20 (2026-04-30), and CentOS 7 itself reached EOL 2024-06-30. The glibc-2.31 patch only raises the ceiling enough for the node 18/20-era official prebuilt binaries — node 22+ prebuilts typically require a newer glibc, so this trick will not carry future LTS lines. For any new work prefer upgrading the OS or running node inside a container. nvm tag refreshed to `v0.40.6`.

## install node by nvm

```bash
# install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.6/install.sh | bash

# install node
nvm ls-remote --lts

# node 18 is lts/hydrogen, node 20 is lts/iron
nvm install lts/hydrogen
nvm install lts/iron

# check node version
nvm alias default lts/hydrogen
node -v

nvm alias default lts/iron
node -v
```

You will find that node 18 works well, but node 20 will not work, because the `glibc` version is too low.
That's so bad, but don't worry; we can fix it by the following steps.

## install glibc 2.31 and libstdc++.so.6.0.25

Follow my another article [centos7-upgrade-libc](https://blog.yoooo.fun/centos7-upgrade-libc.html) to upgrade `glibc` to 2.31.

## test

```bash
nvm alias default lts/iron
node -v # it works well
```

## 相关文章

- [Bootstrap a CentOS 7 server](https://blog.yoooo.fun/centos7-init.html)
- [How to upgrade glibc on CentOS 7](https://blog.yoooo.fun/centos7-upgrade-libc.html)
