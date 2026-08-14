---
categories:
    - general
date: 2020-04-26T15:21:04Z
description: Configure a modern Gradle build on GitHub Actions with gradle/actions/setup-gradle
keywords: gradle, github actions, java, build, setup-gradle
lastmod: 2026-08-13T00:00:00Z
tags:
    - gradle
    - github-actions
    - java
title: How to configure Gradle build on GitHub Actions
---



# How to configure Gradle build on GitHub Actions

> 更新说明：本文于 2026-08 重写。原示例使用已废弃的 `setup-java v1`、手写 `actions/cache` 缓存、默认分支 `master`，现已升级为官方推荐方案 `gradle/actions/setup-gradle`（内置 wrapper 校验与依赖缓存）。

```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    name: Gradle Automation Build
    runs-on: ubuntu-latest
    strategy:
      matrix:
        java: [11, 17, 21]
    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Set up JDK
        uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: ${{ matrix.java }}

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v6   # 内置 wrapper 完整性校验 + Gradle 依赖缓存，无需再手写 actions/cache

      - name: Build with Gradle
        run: ./gradlew clean build

      - name: Upload test results
        uses: actions/upload-artifact@v7
        with:
          name: test-results-${{ matrix.java }}
          path: build/reports/tests/
```

## 为什么这么写

- **`gradle/actions/setup-gradle` 取代手写缓存**：它会自动校验 Gradle wrapper（防供应链攻击）、缓存 dependencies/configuration cache，并支持依赖分析上报。旧示例里两段 `actions/cache@v1` 已不需要。
- **JDK 由 `actions/setup-java` 提供**：显式指定 `distribution: temurin`（Eclipse Temurin LTS）比依赖 runner 预装的 JDK 更可复现；旧版 `v1` 基于已废弃的 Node12 运行时，且自动装的是旧版。
- **分支 `master` → `main`**：GitHub 新仓库默认主分支已是 `main`。
- **矩阵 JDK 选 LTS**：建议 `[11, 17, 21]`（按你的项目实际最低版本裁剪）；最新 LTS 已是 JDK 25，可加进矩阵或作为推荐基线。想再精简可只跑 `21`。
- **上传测试报告（可选）**：`actions/upload-artifact` 便于在构建页查看测试结果。

按需裁剪：若你的项目用 Kotlin/Android，把 `build` 换成对应 task（如 `assemble` / `lint`）即可。
