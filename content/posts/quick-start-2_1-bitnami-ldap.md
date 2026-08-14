---
categories:
    - docker
date: 2023-04-15T23:16:36Z
description: how to configure bitnami openldap based on traefik
keywords: docker,traefik,proxy,ldap,bitnami,openldap,user,https
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - traefik
    - proxy
    - ldap
    - login
title: 'Quick Start: LDAP by Bitnami'
---



# Quick Start: LDAP by Bitnami

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
        image: bitnami/openldap
        restart: always
        environment:
            - LDAP_ADMIN_USERNAME=admin
            - LDAP_ADMIN_PASSWORD=secret  # Change this in production!
            - LDAP_ROOT=dc=chaos,dc=io
            # For phpLDAPadmin compatibility
            - LDAP_PORT_NUMBER=389
            - LDAP_LDAPS_PORT_NUMBER=636
        volumes:
            - openldap:/bitnami/openldap
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
    openldap:

networks:
    traefik-net:
        external: true

```

> Note: In production, use Docker secrets or environment variables for sensitive information like passwords.
>
> Note (2025+): Bitnami stopped publishing updates to its Docker Hub images in 2025. `bitnami/openldap` on Docker Hub is now the legacy (Debian-based) line — still pullable and functional, but no longer updated; Bitnami's current containers are commercial "Secure Images" (Photon-based) served from their own registry. The environment-variable interface used above (`LDAP_ADMIN_USERNAME`, `LDAP_ROOT`, `LDAP_PORT_NUMBER`, ...) is unchanged. For reproducibility, pin a specific legacy tag (format `bitnami/openldap:<version>-debian-12-r<N>`), or use an actively maintained alternative such as [osixia/openldap](https://hub.docker.com/r/osixia/openldap).

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
