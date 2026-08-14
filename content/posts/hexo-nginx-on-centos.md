---
categories:
    - linux
date: 2019-02-12T19:47:51Z
description: deploy hexo with nginx on centos
keywords: nginx, centos, hexo, nodejs
lastmod: 2026-08-14T00:00:00Z
tags:
    - hexo
    - nginx
    - centos
title: Hexo with Nginx on CentOS
---



# deploy hexo by nginx on centos

> **Era note (2026):** This guide targets CentOS 7, which reached EOL on 2024-06-30. On CentOS 8/9 use the same flow but replace `yum` with `dnf`, and pick the nginx.org repo matching your major version. Consider a maintained distro such as Rocky `/` AlmaLinux 9 instead.

- create some dirs and enter pkgs dir

```shell
mkdir /home/packages && cd /home/packages
```

- download npm compiled source code and unzip it (use a current LTS; check https://nodejs.org/en/download)

```shell
wget https://nodejs.org/dist/v24.19.0/node-v24.19.0-linux-x64.tar.xz
tar -xvf node-v24.19.0-linux-x64.tar.xz -C /usr/share/
```

> **CentOS 7 caveat:** Node ≥ 18 prebuilt binaries require glibc ≥ 2.28, while CentOS 7 ships glibc 2.17 — so on CentOS 7 only older Node (e.g. 16) runs natively.
> The era note above already redirects new installs to maintained distros (e.g. Rocky `/` AlmaLinux 9), where this v24 build works.

> Note: Node v11 (pinned in the original 2019 guide) is long EOL with known CVEs. As of Aug 2026 the Active LTS is v24 "Krypton"; on modern distros you may prefer the NodeSource repo or your distro's `nodejs` package.

<!-- more -->

- create soft link

```shell
mv /usr/share/node-v24.19.0-linux-x64 /usr/share/nodejs
ln -s /usr/share/nodejs/bin/node /usr/local/bin/
ln -s /usr/share/nodejs/bin/npm /usr/local/bin/
```

- install hexo

```shell
npm install -g hexo-cli
ln -s /usr/share/nodejs/bin/hexo /usr/local/bin/
```

---

## install nginx

- configure yum repo

```shell
vi /etc/yum.repos.d/my.repo
```

```xml
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```

- install nginx and start it

```shell
yum install -y nginx
systemctl start nginx && systemctl enable nginx
```

---

- init hexo project

```shell
mkdir /home/website && cd /home/website && hexo init && hexo g
```

- refer to hexo public dir

```shell
vi /etc/nginx/conf.d/default.conf
```

```text
location / {
    root   /home/website/public;
    index  index.html index.htm;
}
```

```shell
systemctl restart nginx
```

---
---
Now, you can access your blog by your IP address.
