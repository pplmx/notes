---
categories:
    - python
date: 2020-04-25T21:55:12Z
description: Use uv to install and switch between multiple Python versions in Linux
keywords: uv, python, version management, pyenv, multi versions
lastmod: 2026-08-13T00:00:00Z
tags:
    - python
    - uv
title: Use uv to manage multiple Python versions on Linux
---



# Use uv to manage multiple Python versions

> 更新说明：本文于 2026-08 重写。原方案（Python 2.7 + 3.8 源码编译并手写 alternatives）已过时——Python 2 已于 2020-01 停止维护。如今用 [uv](https://docs.astral.sh/uv/) 一条命令安装、切换多版本并管理虚拟环境，无需编译源码。

## 1. 安装 uv

```bash
# 官方推荐：curl 脚本安装
curl -LsSf https://astral.sh/uv/install.sh | sh
```

备选：`pipx install uv`、`brew install uv`、`apt install uv`。

验证并查看版本：

```bash
uv --version
# 自更新
uv self update
```

## 2. 安装多个 Python 版本

```bash
# 查看可安装的版本
uv python list
# 安装多个版本（uv 自动下载官方预编译解释器，无需 gcc / openssl-dev 等）
uv python install 3.12 3.13 3.14
# 查看已安装
uv python list --only-installed
```

## 3. 为项目锁定版本

```bash
# 在项目根目录创建 .python-version 并写版本号
uv python pin 3.14
```

或在 `pyproject.toml` 中声明：

```toml
[project]
name = "my-project"
requires-python = ">=3.11"
```

之后 `uv run` / `uv venv` 会自动选择 `.python-version` 指定的解释器。

## 4. 创建虚拟环境并运行

```bash
uv venv                                  # 使用锁定版本建 .venv
uv run python -c "import sys; print(sys.version)"
uv run pytest                            # 在虚拟环境中运行任意命令
uvx black --check .                      # 临时运行工具（等价 pipx）
```

## 5. 常用速查

| 目标 | 命令 |
|------|------|
| 安装 Python 版本 | `uv python install <ver>` |
| 项目锁定版本 | `uv python pin <ver>` |
| 创建虚拟环境 | `uv venv` |
| 环境内运行命令 | `uv run <cmd>` |
| 添加/移除依赖 | `uv add <pkg>` / `uv remove <pkg>` |
| 同步依赖 | `uv sync` |
| 卸载解释器 | `uv python uninstall <ver>` |

## 常见问题

- 首次下载解释器较慢时，可配置镜像加速下载：`export UV_PYTHON_INSTALL_MIRROR=<镜像地址>`（国内用 github/ghproxy 或内网加速镜像）。
- 发行版自带的系统 Python 不要删除（系统工具依赖它）；uv 管理的是独立安装的解释器，互不干扰。
