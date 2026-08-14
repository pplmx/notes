---
categories:
    - linux
date: 2020-05-09T17:15:26Z
description: Some Info about SUSE
keywords: opensuse, zypper
lastmod: 2026-08-14T00:00:00Z
tags:
    - opensuse
    - linux
title: Some Info about SUSE
---



# zypper

```bash
# update all packages
sudo zypper refresh
# or sudo zypper ref
sudo zypper update
# or sudo zypper up

# list repos
zypper lr
# add a repo
# NOTE: Leap 15.2 (EOL end of 2021) is kept as a historical example only.
# The classic Leap line ended at 15.6; the current release is openSUSE Leap 16.0.
# Aliyun's openSUSE update mirror currently hosts up to leap/15.6
# (e.g. https://mirrors.aliyun.com/opensuse/update/leap/15.6/oss/); a leap/16.0
# path is not published there yet, so on Leap 16 use the official repos.
zypper ar https://mirrors.aliyun.com/opensuse/update/leap/15.2/oss/ aliyun-suse-oss
# remove a repo
zypper rr aliyun-suse-oss

# enable the first repo
zypper mr -e 1
# disable the second repo
zypper mr -d 2
# enable caching for all repos
zypper mr -ka
# disable caching for all repos
zypper mr -Ka
# disable gpg check for all repos
zypper mr -Ga
```

## zypper some directories info

```text
/etc/zypp/zypper.conf
/etc/zypp/locks

# Directory containing repository definition (*.repo) files.
/etc/zypp/repos.d

# Directory containing service definition (*.service) files.
/etc/zypp/services.d

# System directory containing zypper extensions
/usr/lib/zypper/commands

# Directory for storing raw metadata contained in repositories.
/var/cache/zypp/raw

# Directory containing preparsed metadata in form of solv files.
/var/cache/zypp/solv

# If keeppackages property is set for a repository (see the modifyrepo command)
# all the RPM file downloaded during installation will be kept here.
/var/cache/zypp/packages

# Installation history log.
/var/log/zypp/history
```

