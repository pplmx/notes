---
categories:
    - docker
date: 2023-03-10T16:58:34Z
description: build cpp project with gcc 11 on CentOS7 image
keywords: cpp,gcc11,centos7
lastmod: 2026-08-14T00:00:00Z
tags:
    - cpp
    - linux
    - centos
    - gcc
    - docker
title: Build cpp with gcc11 on CentOS7
---



> **⚠ EOL (End-of-Life) notice**: CentOS 7 reached EOL on 2024-06-30. The `centos:7`
> image is frozen, and its yum repos now point at the retired `mirror.centos.org`, so
> `yum install` inside the build may fail unless yum is reconfigured to use
> `mirror/vault.centos.org`. The `devtoolset-11` SCL packages are also part of the EOL
> CentOS 7 ecosystem, so their availability is no longer guaranteed. The pinned CMake
> `3.24.2` release tarball still resolves (Kitware keeps old releases).
>
> For anything new, use a supported base image instead: e.g. `FROM almalinux:9` /
> `FROM rockylinux:9`, where a modern compiler (gcc 12+, via the built-in
> `gcc-toolset-12`/`13`) installs with `dnf install -y gcc g++ make`. The Dockerfile
> below is kept as a self-contained historical build target.

```dockerfile
### BUILDING
FROM centos:7 AS builder
LABEL author="Mystic"
ARG CMAKE_VERSION="3.24.2"

WORKDIR /var/tmp
RUN curl -LO https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz &&\
    tar -zxf cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz && \
    \cp -fr cmake-${CMAKE_VERSION}-linux-x86_64/* /usr/local
RUN yum upgrade -y
# install gcc 11
RUN yum install -y centos-release-scl &&  \
    yum install -y devtoolset-11

WORKDIR /app

# To ensure the symlink works fine on Windows, you must do the following:
# 1. `git config --global core.symlinks true`
# 2. Enable `Developer Mode` to authorize `mklink` permission
# 3. reclone your repo
COPY . .

WORKDIR build

# Run cmake with gcc 11
RUN scl enable devtoolset-11 'cmake .. && cmake --build . --target sample --config Release --parallel 8'

### DEPLOYING
FROM centos:7

COPY --from=builder /app/build/sample /sample
COPY test .

CMD ["/sample"]
```
