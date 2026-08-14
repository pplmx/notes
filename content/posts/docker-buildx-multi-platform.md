---
categories:
    - docker
date: 2023-03-10T17:00:42Z
description: build amd64 and arm64 images for multi-platform support
keywords: linux,amd64,arm64,docker
lastmod: 2026-08-14T00:00:00Z
tags:
    - linux
    - amd64
    - arm64
    - docker
title: Build amd64 and arm64 for docker images
---



# docker buildx for multiarch support

## create a builder

Newer Docker (Docker Desktop / Engine 29.0+ with the containerd image store) can build multi-platform images with the default `docker` builder. On older setups the default builder still rejects `--platform`, so create a `docker-container` builder like this — it is also required when you use a custom `buildkitd.toml` or push straight to a registry:

```shell
docker buildx create  --use --name multiarch --driver docker-container
```

if your self-build registry is not https, please touch a `buildkitd.toml`

```toml
[registry."your-registry-domain or ip"]
http = true
insecure = true

```

> Note: `http = true` is for plain-HTTP registries; `insecure = true` is for HTTPS with a self-signed certificate (usually pick one, not both).

and then use this config to create a builder

```shell
# if your builder has already existed, delete it and create
docker buildx rm multiarch
docker buildx create  --use --name multiarch --driver docker-container --config ./buildkitd.toml
```

## build the multiarch image and push it to your registry

Make sure you are logged in to the target registry first (pushing images requires authentication):

```shell
docker login your-registry-domain or ip
```

`docker buildx inspect --bootstrap` to get the supported platform information

On Linux, if you build for a platform you don't have natively (e.g. arm64 on an amd64 host), register the emulator first — QEMU/binfmt is bundled with Docker Desktop, but manual setups need:

```shell
docker run --privileged --rm tonistiigi/binfmt --install all
```

```shell
# don't ignore the trailing dot, it's to search the current directory's Dockerfile file
docker buildx build --push --platform linux/amd64,linux/arm64 -t pplmx/demo:v2.0.0 .

# if you wanna to specify a Dockerfile
docker buildx build --push --platform linux/amd64,linux/arm64 -t pplmx/demo:v2.0.0 -f ./Dockerfile
```
