---
categories:
    - python
date: 2020-09-27T13:32:10Z
description: Install and manage Python with uv — no compiling from source needed
keywords: uv, python, install python, no compile
lastmod: 2026-08-13T00:00:00Z
tags:
    - python
    - uv
title: Use uv to manage Python (no more compiling from source)
---



# Use uv to manage Python (no more compiling from source)

> 更新说明：本文于 2026-08 重写，以 uv 取代原文的源码编译方案。日常开发不再需要「下载 tar → configure → make → make install → 手写 PATH」，uv 直接拉取官方预编译解释器，开箱即用。

## 为什么不再需要源码编译

- 原方案：`wget` 源码包 → `./configure --enable-optimizations` → `make` → `sudo make install` → 手动管理 PATH。
- 现方案：`uv python install 3.14` 下载预编译 CPython（python-build-standalone），自带 pip，秒级完成，无需 gcc 与一堆依赖库。
- 源码编译如今仅在定制功能（自定义 feature set、嵌入式场景）时才有意义，普通项目管理完全不需要。

## 1. 安装 uv 并安装 Python

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv python install 3.14
uv python list --only-installed
```

## 2. 让 uv 自动选择解释器

uv 的「按需解析」会自动找到合适的 Python：

```bash
# 指定解释器运行（无需先建 venv）
uv run --python 3.12 python -c "import sys; print(sys.version)"

# 用环境变量固定版本
export UV_PYTHON=3.14
uv venv        # 用 UV_PYTHON 建环境
```

## 3. 项目级使用

```bash
uv init my-project && cd my-project
uv python pin 3.14        # 写入 .python-version
uv add requests pytest    # 添加依赖并生成锁文件
uv run python main.py     # 在锁定环境里运行
```

## 4. 常用速查

| 目标 | 命令 |
|------|------|
| 安装 Python | `uv python install <ver>` |
| 按需使用解释器 | `uv run --python <ver>` |
| 固定项目版本 | `uv python pin <ver>` / `UV_PYTHON=` |
| 建环境/装依赖 | `uv venv` / `uv add` / `uv sync` |
| 临时代理进程 | `uvx` |

## 5. 遇到下载慢/失败？

配置镜像并重试：

```bash
export UV_PYTHON_INSTALL_MIRROR=<国内镜像>
uv python install 3.14
```

> 小贴士：需要「开箱即用的完整工具链」时也可以直接用 `uv` 管理依赖而不装全局 Python——它会在需要时自动拉取。
