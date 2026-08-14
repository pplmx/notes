---
categories:
    - docker
date: 2023-07-28T10:44:11Z
description: copy a wget or curl binary to distroless image to healthcheck
keywords: docker,distroless,healthcheck,wget,curl
lastmod: 2026-08-14T00:00:00Z
tags:
    - docker
    - healthcheck
    - curl
title: How to healthcheck distroless image
---



# How to healthcheck in distroless image

## Dockerfile

```dockerfile
### For HEALTHCHECK
FROM busybox AS wgeter

# wget grpc-health-probe
RUN wget -O /tmp/hc https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v0.4.55/grpc_health_probe-linux-amd64 \
    && chmod +x /tmp/hc \
    && mv /tmp/hc /bin/hc

### Deploy
FROM gcr.io/distroless/static
# CN network fallback (gcr.io may be slow/blocked; gcr.dockerproxy.com is dead):
# FROM gcr.m.daocloud.io/distroless/static
ENV TZ=Asia/Shanghai

# copy wget and health, one for http healthcheck, one for grpc healthcheck
COPY --from=wgeter /bin/wget /bin/wget
COPY --from=wgeter /bin/hc /bin/hc

HEALTHCHECK --interval=5s --timeout=3s --start-period=5s --retries=3 CMD ["hc", "-addr=localhost:9000"]
```

> **Note (2026-08-14):** `grpc-health-probe` re-pinned to `v0.4.55`; the `-addr=` flag is unchanged. Base image switched back to the official `gcr.io/distroless/static` (the `gcr.dockerproxy.com` third-party mirror no longer resolves). For CN users whose network blocks `gcr.io`, prefer `gcr.m.daocloud.io` as the mirror.
