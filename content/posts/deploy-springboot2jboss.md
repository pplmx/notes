---
categories:
    - java
date: 2019-10-09T20:21:45Z
description: deploy springboot war to jboss
keywords: webflux, gradle, jboss, wildfly, spring boot
lastmod: 2026-08-14T00:00:00Z
tags:
    - spring
    - gradle
    - jboss
title: deploy springboot to external container(JBoss)
---



> **Era note (2026):** This 2019 walkthrough pins WildFly 18.0.1.Final / JDK 11 / Gradle 5.6 - all long EOL (WildFly 18 is unsupported; current is WildFly 41, released Jul 2026). Packaging a Spring Boot WebFlux app as a WAR for an external app server is no longer the recommended deployment model - prefer a self-executable JAR or a GraalVM native image. The mechanics below still work for legacy environments.

> Wildfly: 18.0.1.Final
>
> JDK: 11.0.2
>
> Gradle: 5.6.2
>
> Web: Webflux

[Source Code](https://github.com/pplmx/DeploySpringboot2JBoss)

<!-- more -->

## create springboot demo project by initializer

![1569844794129](assets/1569844794129.png)

![1569844973320](assets/1569844973320.png)

![1569845032989](assets/1569845032989.png)

![1569845140007](assets/1569845140007.png)

![1569846771715](assets/1569846771715.png)

## write a test case

![1569846460212](assets/1569846460212.png)

## build package

![1570622045669](assets/1570622045669.png)

## deploy it to JBoss

You can put `boot.war` to `$JBOSS_HOME/standalone/deployments/`, then run `$JBOSS_HOME/bin/standalone.bat` by administrator.

[https://localhost:8443/boot/](https://localhost:8443/boot/)

[http://localhost:8080/boot/](http://localhost:8080/boot/)

It will output `hello world, springboot`.
