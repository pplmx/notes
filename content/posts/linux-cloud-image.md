---
categories:
    - linux
date: 2020-04-18T20:56:44Z
description: how to customize cloud image config
keywords: cloud image, image, reset password
lastmod: 2026-08-14T00:00:00Z
tags:
    - linux
    - image
title: config in cloud image
---



# change cloud image default config

> **⚠ EOL (End-of-Life) notice**: RHEL 7 reached EOL on 2024-06-30. The examples below use
> `rhel-server-7.6.qcow2` — an EOL release, and `yum` is the RHEL 7-era package manager.
> `virt-customize` itself is unchanged and fully supported on RHEL 8/9, AlmaLinux and
> Rocky Linux (install via `dnf`, e.g. `sudo dnf install -y libguestfs-tools`). For a
> modern target, replace the image with an up-to-date cloud image, for example:
> - AlmaLinux 9: `AlmaLinux-9-GenericCloud-latest.x86_64.qcow2` (from
>   https://repo.almalinux.org/almalinux/9/cloud/x86_64/images/)
> - Rocky Linux 9: `Rocky-9-GenericCloud-Base.latest.x86_64.qcow2` (from
>   https://dl.rockylinux.org/pub/rocky/9/images/x86_64/)

## install package

```bash
# RHEL 7 / CentOS 7 era (EOL): yum
sudo yum install -y libguestfs-tools
# or
sudo yum install -y libguestfs-tools-c
# RHEL 8/9 / AlmaLinux / Rocky Linux: dnf
sudo dnf install -y libguestfs-tools
```

## Set root password

```bash
virt-customize -a rhel-server-7.6.qcow2 --root-password password:StrongRootPassword
```

## Register System

```bash
virt-customize -a overcloud-full.qcow2 --run-command 'subscription-manager register --username=[username] --password=[password]'

virt-customize -a rhel-server-7.6.qcow2 --run-command 'subscription-manager attach --pool [subscription-pool]'
```

## Install Software packages inside an image

```bash
virt-customize -a rhel-server-7.6.qcow2 --install [vim,bash-completion,wget,curl,telnet,unzip]

virt-customize -a rhel-server-7.6.qcow2 --install net-tools
```

## Upload SSH public key

```bash
# set ssh-key for a user(The user must exist in image)
virt-customize -a rhel-server-7.6.qcow2  --ssh-inject root:file:./id_rsa.pub
# or
virt-customize -a rhel-server-7.6.qcow2 --run-command 'useradd mystic' \
	--ssh-inject mystic:file:~/.ssh/id_rsa.pub
```

## Uploading files

```bash
virt-customize -a rhel-server-7.6.qcow2 --upload rhsm.conf:/etc/rhsm/rhsm.conf

virt-customize -a rhel-server-7.6.qcow2 --upload yum.conf:/etc/yum.conf

virt-customize -a rhel-server-7.6.qcow2 --upload proxy.sh:/etc/profile.d/
```

> The format: `local_file_path:image_file_path`

## Set Timezone

```bash
virt-customize -a rhel-server-7.6.qcow2 --timezone "Asia/Shanghai"
```

## Relabel SELinux

```bash
virt-customize -a rhel-server-7.6.qcow2 --selinux-relabel
```



