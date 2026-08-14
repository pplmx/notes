---
categories:
    - docker
date: 2023-04-13T23:45:36Z
description: how to configure ldap based on traefik
keywords: docker,traefik,proxy,ldap,user,https
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - traefik
    - proxy
    - ldap
    - login
title: 'Quick Start: LDAP'
---



# Quick Start: LDAP

> **Era note (2026):** The `osixia/openldap` and `osixia/phpldapadmin` images below are effectively unmaintained - frozen since ~Feb 2021 and shipping OpenLDAP 2.4 (EOL). The Traefik routing shown here works with any OpenLDAP image, but for new projects prefer a maintained one such as `vegardit/openldap` or `bitnami/openldap` (the free Bitnami image now lives in `bitnamilegacy` after the Broadcom change).

If you want to use `bitnamic/openldap`,
please follow this [Quick Start: LDAP by Bitnami](https://blog.yoooo.fun/quick-start-2_1-bitnami-ldap.html).

## Prerequisite

> - [Traefik on HTTP](https://blog.yoooo.fun/quick-start-1-traefik.html)
>
> OR
>
> - [Traefik on HTTPS](https://blog.yoooo.fun/quick-start-1_1-traefik-ssl.html)
>
> Note: If using HTTP, remove the `tls: {}` in dynamic configuration.

## Preparation

### compose.yml

```yaml
services:
    ldap:
        image: osixia/openldap
        restart: always
        environment:
            - LDAP_ORGANISATION=Chaos Inc.
            # if LDAP_DOMAIN=chaos.io, the login DN will be "cn=admin,dc=chaos,dc=io"
            # LDAP_DOMAIN default value is "example.org"
            # so default login DN is "cn=admin,dc=example,dc=org"
            - LDAP_DOMAIN=chaos.io
            - LDAP_ADMIN_PASSWORD=secret
        volumes:
            - ldap:/var/lib/ldap
            - slapd:/etc/ldap/slapd.d
        networks:
            - traefik-net

    ldapadmin:
        image: osixia/phpldapadmin
        restart: always
        environment:
            - PHPLDAPADMIN_LDAP_HOSTS=ldap
            # if configure https by traefik, you need to configure the following two lines
            # if not, remove them
            - VIRTUAL_HOST=ldap.x.internal
            - PHPLDAPADMIN_HTTPS=false
        networks:
            - traefik-net
volumes:
    ldap:
    slapd:

networks:
    traefik-net:
        external: true

```

### ldap.yml in dir dynamic-conf

> You should touch `ldap.yml` in traefik dir **dynamic-conf**.
>
> For much more information, please reference the [Prerequisite](#Prerequisite).

```yaml
http:
    routers:
        ldap:
            rule: "Host(`ldap.x.internal`)"
            service: "ldap"
            tls: { }

    services:
        ldap:
            loadBalancer:
                servers:
                    -   url: "http://ldapadmin"

```

## DNS Configuration

Configure your DNS or modify your hosts file:

- For Unix-like systems: Edit `/etc/hosts`
- For Windows: Edit `C:\Windows\System32\drivers\etc\hosts`

Add the following line:

```
127.0.0.1 ldap.x.internal
```

## Run

```shell
docker compose up -d
# Alternative commands:
# docker compose -p ldap up -d
# docker compose -f ./compose.yml -p ldap up -d
```

Access: https://ldap.x.internal

## 本系列（Quick Start）
- [Quick Start: Traefik Dashboard with Custom Domain](https://blog.yoooo.fun/quick-start-1-traefik.html)
- [Quick Start: Traefik with HTTPS](https://blog.yoooo.fun/quick-start-1_1-traefik-ssl.html)
- [Quick Start: Traefik with HTTP/3](https://blog.yoooo.fun/quick-start-1_2-traefik-http3.html)
- [Quick Start: LDAP](https://blog.yoooo.fun/quick-start-2-ldap.html)
- [Quick Start: LDAP by Bitnami](https://blog.yoooo.fun/quick-start-2_1-bitnami-ldap.html)
- [Quick Start: Jenkins](https://blog.yoooo.fun/quick-start-3-jenkins.html)
- [Quick Start: SonarQube](https://blog.yoooo.fun/quick-start-4-sonar.html)
- [Quick Start: Gerrit](https://blog.yoooo.fun/quick-start-5-gerrit.html)
- [Quick Start: SSP](https://blog.yoooo.fun/quick-start-6-ldap-ssp.html)
