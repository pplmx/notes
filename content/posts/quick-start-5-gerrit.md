---
categories:
    - docker
date: 2023-04-15T22:03:21Z
description: how to configure gerrit with https based on traefik
keywords: docker,traefik,proxy,gerrit,code review,review,https
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - traefik
    - proxy
    - gerrit
title: 'Quick Start: Gerrit'
---



# Quick Start: Gerrit

> **Note (2026-08-14):** Re-verified against current Gerrit `3.14.x`. The image tag is now pinned (`gerritcodereview/gerrit:3.14.2`); the two-step flow (`command: init`, then daemon) still matches the current image entrypoint. The `[index] type = LUCENE`, `[download] schema = http/ssh`, `[receive] enableSignedPush`, LDAP keys (`accountPattern`/`accountFullName`/`accountEmailAddress`) and `smtpEncryption = SSL` are all still valid settings in the 3.14 config docs. Note the current image runs as `gerrit` (uid 1000) by default and ships on ubuntu/almalinux bases — keep `user: root` here because the mounted `/opt/gerrit/*` volumes are root-owned. Plaintext `smtpPass` is a placeholder; use a real credential or docker secret.

## Prerequisite

> - [Traefik on HTTP](https://blog.yoooo.fun/quick-start-1-traefik.html)
>
> OR
>
> - [Traefik on HTTPS](https://blog.yoooo.fun/quick-start-1_1-traefik-ssl.html)
>
> Note: If using HTTP, remove the `tls: {}` in dynamic configuration.
>
> - [LDAP by Traefik](https://blog.yoooo.fun/quick-start-2-ldap.html)

## Preparation

### create some dirs and files

```shell
sudo install -d /opt/gerrit; cd /opt/gerrit; sudo install -d etc git db index cache plugins
```

#### vim /opt/gerrit/etc/gerrit.config

```ini
[gerrit]
	basePath = git

[index]
	type = LUCENE

[auth]
	type = ldap

[sshd]
	listenAddress = *:29418

[httpd]
	listenUrl = http://*:8080/

[cache]
	directory = cache

[container]
	user = root

[download]
	schema = http
	schema = ssh

[plugins]
	# for plugin-manager plugin
	allowRemoteAdmin = true

[ldap]
	# the second ldap is docker compose service name
	server = ldap://ldap
	# dc=chaos,dc=io is from ldap service environment: LDAP_DOMAIN=chaos.io
	username = cn=admin,dc=chaos,dc=io
	accountBase = dc=chaos,dc=io
	accountPattern = (&(objectClass=person)(uid=${username}))
	accountFullName = displayName
	accountEmailAddress = mail

[receive]
	enableSignedPush = false

[user]
	name = Gerrit Code Review
	email = webhook@example.com
	anonymousCoward = Gerrit Code Review

[sendemail]
	smtpServer = smtp.exmail.qq.com
	smtpServerPort = 465
	smtpEncryption = SSL
	sslVerify = true
	smtpUser = webhook@example.com
	smtpPass = YOUR_PASSWORD
	from = ${user} (Code Review) <webhook@example.com>

[commentlink "changeid"]
	match = (I[0-9a-f]{8,40})
	link = "#/q/$1"

[commentlink "gitee"]
	match = "gitee: #(.{6})"
	link = https://e.gitee.com/example_user/dashboard?issue=$1

```

#### vim /opt/gerrit/etc/secure.config

```ini
[ldap]
	# this value is from ldap service environment: LDAP_ADMIN_PASSWORD=secret
	password = secret

```

### compose.yml

```yaml
services:
    gerrit:
        # pinned to a release tag; 3.14.x is the current stable line
        image: gerritcodereview/gerrit:3.14.2
        # root matches the root-owned /opt/gerrit volumes and [container] user = root above
        user: root
        ports:
            - "29418:29418"
        expose:
            - 8080
        volumes:
            - /opt/gerrit/etc:/var/gerrit/etc
            - /opt/gerrit/git:/var/gerrit/git
            - /opt/gerrit/db:/var/gerrit/db
            - /opt/gerrit/index:/var/gerrit/index
            - /opt/gerrit/cache:/var/gerrit/cache
            - /opt/gerrit/plugins:/var/gerrit/plugins
        environment:
            - CANONICAL_WEB_URL=http://gerrit.x.internal
            - HTTPD_LISTEN_URL=proxy-http://*:8080
        networks:
            - traefik-net
        command: init

networks:
    traefik-net:
        external: true

```

### gerrit.yml in dir dynamic-conf

> You should touch `gerrit.yml` in traefik dir **dynamic-conf**.
>
> For much more information, please reference the [Prerequisite](#Prerequisite).

```yaml
http:
    routers:
        gerrit:
            rule: "Host(`gerrit.x.internal`)"
            service: "gerrit"
            tls: { }

    services:
        gerrit:
            loadBalancer:
                servers:
                    -   url: "http://gerrit:8080"

```

## DNS Configuration

Configure your DNS or modify your hosts file:

- For Unix-like systems: Edit `/etc/hosts`
- For Windows: Edit `C:\Windows\System32\drivers\etc\hosts`

Add the following line:

```
127.0.0.1 gerrit.x.internal
```

## Run

### STEP-1: Run Gerrit docker init setup from docker

**Uncomment** the `command: init` option in `compose.yml` and run Gerrit with docker-compose in foreground.

```shell
docker compose up gerrit
```

Wait until you see in the output the message `Initialized /var/gerrit` and then the container will exit.

### STEP-2: Start Gerrit in daemon mode

**Comment out** the `command: init` option in docker-compose.yaml and start all the docker-compose nodes:

```shell
docker compose up -d
```

Access: https://gerrit.x.internal

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
