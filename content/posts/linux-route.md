---
categories:
    - linux
date: 2019-05-31T20:13:55Z
description: some linux route operations
keywords: route, network, linux
lastmod: 2026-08-14T00:00:00Z
tags:
    - network
    - linux
title: set route on linux
---



# linux route

> Note: The ephemeral `ip route add/del/replace/change` commands below are still current standard tooling. However, the permanent-config files (`/etc/sysconfig/static-routes`, `/sbin/ifup-local`) and `service network` / `systemctl restart network` shown in the "Permanent" and "replace route" sections are the legacy RHEL/CentOS 7 inet-scripts approach — superseded by NetworkManager (`nmcli` / `/etc/NetworkManager/system-connections/`) on RHEL 8+ and modern distros. The `route` command is also a legacy alias; use `ip route` instead.

## add static route

- add default static route

```bash
# Permanent
echo "any net 0.0.0.0/0 gw 110.188.40.1" >> /etc/sysconfig/static-routes
# Temporary
ip route add default dev vlan7
ip route add default via 110.188.40.1
ip route add default via 110.188.40.1 dev vlan7
ip route add 0.0.0.0/0 dev vlan7
ip route add 0.0.0.0/0 via 110.188.40.1
ip route add 0.0.0.0/0 via 110.188.40.1 dev vlan7
```

<!-- more -->

- add specific net static route

```bash
echo "any net 110.188.40.0/24 gw 110.188.40.1" >> /etc/sysconfig/static-routes
```

## delete route

```bash
# delete default route
route del default gw 110.188.40.1
ip route del default via 110.188.18.1 dev vlan16
# delete a non-default route
ip route del 110.188.18.0/24 via 110.188.18.1 dev vlan16
```

## replace route

```bash
# work well after every network restart
# replace if exists, or add
echo "ip route replace default via 110.188.40.1 dev vlan7" >> /sbin/ifup-local
chmod +x /sbin/ifup-local
systemctl restart network
```

## change route

```bash
# change some params of existing route
ip route change 192.192.13.1/24 dev ens32
```

