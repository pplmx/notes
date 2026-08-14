---
categories:
    - docker
date: 2023-04-14T14:18:21Z
description: how to configure sonarqube with https based on traefik
keywords: docker,traefik,proxy,sonarqube,sonar,security,https
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - traefik
    - proxy
    - sonar
    - security
title: 'Quick Start: SonarQube'
---



# Quick Start: SonarQube

## Prerequisite

> - [Traefik on HTTP](https://blog.yoooo.fun/quick-start-1-traefik.html)
>
> OR
>
> - [Traefik on HTTPS](https://blog.yoooo.fun/quick-start-1_1-traefik-ssl.html)
>
> Note: If using HTTP, remove the `tls: {}` in dynamic configuration.

## Preparation

### Configure host sysctl

Add the following lines to `/etc/sysctl.conf`:

```shell
vm.max_map_count = 524288  # SonarQube requires at least 262144
fs.file-max = 131072
```

Apply changes with: `sudo sysctl -p`

### compose.yml

```yaml
services:
    sonarqube:
        image: sonarqube:community  # for a stable LTS, use sonarqube:lts-community (current LTA: 2026.1)
        hostname: sonarqube
        container_name: sonarqube
        depends_on:
            - db
        environment:
            SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
            SONAR_JDBC_USERNAME: sonar
            SONAR_JDBC_PASSWORD: sonar  # Change this in production!
            # LDAP configuration (optional)
            SONAR_SECURITY_REALM: LDAP
            LDAP_URL: ldap://ldap
            LDAP_BINDDN: cn=admin,dc=chaos,dc=io
            LDAP_BINDPASSWORD: secret
            LDAP_USER_BASEDN: dc=chaos,dc=io
            LDAP_USER_REQUEST: (&(objectClass=person)(uid={login}))
        volumes:
            - sonarqube_data:/opt/sonarqube/data
            - sonarqube_extensions:/opt/sonarqube/extensions
            - sonarqube_logs:/opt/sonarqube/logs
        ulimits:
            nofile:
                soft: 131072
                hard: 131072
            nproc:
                soft: 8192
                hard: 8192
        expose:
            - 9000
        restart: unless-stopped
        networks:
            - traefik-net
    db:
        image: postgres:16  # SonarQube supports Postgres 13-17; 14 reaches EOL 2026-11-12
        hostname: postgresql
        container_name: postgresql
        environment:
            POSTGRES_USER: sonar
            POSTGRES_PASSWORD: sonar  # Change this in production!
            POSTGRES_DB: sonar
        volumes:
            - postgresql:/var/lib/postgresql
            - postgresql_data:/var/lib/postgresql/data
        restart: unless-stopped
        networks:
            - traefik-net

volumes:
    sonarqube_data:
    sonarqube_extensions:
    sonarqube_logs:
    postgresql:
    postgresql_data:

networks:
    traefik-net:
        external: true

```

> Note: In production, use Docker secrets or environment variables for sensitive information like passwords.

### sonar.yml in dir dynamic-conf

> You should touch `sonar.yml` in traefik dir **dynamic-conf**.
>
> For much more information, please reference the [Prerequisite](#Prerequisite).

```yaml
http:
    routers:
        sonarqube:
            rule: "Host(`sonar.x.internal`)"
            service: "sonarqube"
            tls: { }

    services:
        sonarqube:
            loadBalancer:
                servers:
                    -   url: "http://sonarqube:9000"

```

## DNS Configuration

Configure your DNS or modify your hosts file:

- For Unix-like systems: Edit `/etc/hosts`
- For Windows: Edit `C:\Windows\System32\drivers\etc\hosts`

Add the following line:

```
127.0.0.1 sonar.x.internal
```

## Run

```shell
docker compose up -d
# Alternative commands:
# docker compose -p sonar up -d
# docker compose -f ./compose.yml -p sonar up -d
```

Access: https://sonar.x.internal

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
