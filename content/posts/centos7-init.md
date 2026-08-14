---
categories:
    - linux
date: 2024-01-13T11:18:20Z
description: Bootstrap a CentOS 7 server — 历史参考（CentOS 7 已于 2024-06-30 EOL）
keywords: zsh, linux, centos7, eol, historical
lastmod: 2026-08-14T00:00:00Z
tags:
    - linux
    - centos
title: Bootstrap a CentOS 7 server
---



# bootstrap a CentOS 7 server

> ⚠️ **CentOS 7 已于 2024-06-30 停止维护（EOL）**
>
> 本文是 2023/2024 年在 CentOS 7 上的初始化实录，**仅为历史参考**。
> CentOS 7 的官方仓库已冻结于 [vault.centos.org](https://vault.centos.org/)，不再有安全更新；SCL/devtoolset、docker 的 el7 仓库等均处于"可用但停更"状态。
>
> **新装服务器请勿使用 CentOS 7**，建议改用 **Rocky Linux 9 / AlmaLinux 9**（RHEL 9 兼容，维护到 2032 年）或 **Ubuntu 20.04 LTS+**。
> 在 Rocky/Alma 9 上的等价操作（`yum` → `dnf`，SCL → 系统 GCC）：
>
> ```bash
> # 基础工具链（无需 SCL，GCC 默认即为现代版本）
> sudo dnf install -y gcc gcc-c++ make git zsh
>
> # Node.js：官方 LTS 直接可用（现代 Node 需要 glibc >= 2.28，Rocky 9 自带 glibc 2.34）
> sudo dnf install -y nodejs
>
> # Python：直接用 dnf 或 uv，无需源码编译
> sudo dnf install -y python3
> curl -LsSf https://astral.sh/uv/install.sh | sh
>
> # Docker：官方仓库直接支持，一套命令装完
> sudo dnf install -y dnf-plugins-core
> sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
> sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
> ```

## install gcc 11

```bash
sudo yum install -y centos-release-scl
sudo yum install -y devtoolset-11

# enable gcc 11 temporarily
scl enable devtoolset-11 bash
scl enable devtoolset-11 zsh

# enable gcc 11 permanently
echo "[ -f /opt/rh/devtoolset-11/enable ] && source /opt/rh/devtoolset-11/enable" >> ~/.bashrc
echo "[ -f /opt/rh/devtoolset-11/enable ] && source /opt/rh/devtoolset-11/enable" >> ~/.zshrc

# check the version
gcc --version
g++ --version
```

> 注：SCL（Software Collections）仓库随 CentOS 7 一并冻结，仍可从 vault 拉到 `devtoolset-11`，但不再有更新。

## install zsh

```bash
sudo yum groupinstall -y "Development tools"
sudo yum install -y gettext-devel openssl-devel perl-CPAN perl-devel zlib-devel ncurses-devel
wget --no-check-certificate https://www.zsh.org/pub/zsh-5.9.tar.xz
tar -xf zsh-5.9.tar.xz
cd zsh-5.9
./configure
make
sudo make install
echo "/usr/local/bin/zsh" | sudo tee -a /etc/shells

chsh -s /usr/local/bin/zsh
```

## install git

```bash
# check the old git version
git --version

# remove the old git
sudo yum remove -y git
sudo yum remove -y git-*

# install a repo
sudo yum install -y https://packages.endpointdev.com/rhel/7/os/x86_64/endpoint-repo.x86_64.rpm
sudo yum install -y git

# check git version
git --version
```

## install oh-my-zsh

Follow this [link](https://blog.yoooo.fun/build-server-zsh.html)

## install cmake

```bash
# download and extract
CMAKE_VERSION=3.28.3
wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}.tar.gz
tar -xf cmake-${CMAKE_VERSION}.tar.gz

# compile
cd cmake-${CMAKE_VERSION}
./configure
make -j 8
sudo make install

# check cmake version
cmake --version

# remove cmake source code
cd .. && rm -rf cmake-${CMAKE_VERSION}*
```

### install docker

FYI: [install docker](https://docs.docker.com/engine/install/centos/)

> 注：Docker 官方现已不再将 CentOS 7 列为支持平台（安装文档只剩 CentOS Stream 9/10）；
> el7 仓库冻结在 `docker-ce 26.1.4`（2024-06），`get.docker.com` 脚本对 el7 仍会给出 EOL 警告并继续安装。

```bash
# 先运行官方脚本：它会自动配置 docker-ce 仓库，并安装
# docker-ce / docker-ce-cli / containerd.io / docker-buildx-plugin / docker-compose-plugin
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 若脚本版本较旧、未自动带 buildx，仓库已在位，此时再单独补装：
sudo yum install -y docker-buildx-plugin

# 验证
docker --version
docker buildx version

# start docker
sudo systemctl enable docker
sudo systemctl start docker

# post installation
sudo /usr/sbin/groupadd docker
sudo /usr/sbin/usermod -aG docker $USER
newgrp docker
```

### install java

```bash
wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.rpm
sudo yum install -y ./jdk-17_linux-x64_bin.rpm
rm -f jdk-17_linux-x64_bin.rpm

```

### install go

Follow this [link](https://blog.yoooo.fun/install-binary-file.html)

## install python from source

> 注：**Python 3.8 已于 2024-10-07 停止安全维护（EOL）**，示例已改为仍受支持的 3.12.x。
> 3.12 需要 **OpenSSL 1.1.1+** 才能编译出 `_ssl` 模块，而 CentOS 7 自带 openssl 1.0.2（不满足，
> 编译时会跳过 ssl 模块）；如需 ssl 支持，请先升级 openssl（可用 SCL 的 openssl11）。
> 新系统上建议直接用 [uv](https://docs.astral.sh/uv/)，无需源码编译。

```bash
# download and extract
PYTHON_VERSION=3.12.14
wget -O /var/tmp/python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tar.xz"
install -d /var/tmp/python; tar -xf /var/tmp/python.tar.xz -C /var/tmp/python --strip-components 1

# compile
cd /var/tmp/python
./configure --enable-optimizations
make -j 8
# altinstall will not replace the system python,
# which means you need to use python3.12 and pip3.12 instead of python3 and pip3
sudo make altinstall

# of course, you can use ln -s to make python3.12 as default python3,
# because the centos 7 system python is 2.7
sudo ln -s /usr/local/bin/python3.12 /usr/local/bin/python3
sudo ln -s /usr/local/bin/pip3.12 /usr/local/bin/pip3
```

## install node

> 版本说明（2026-08 校正）：
>
> - **Node 16（lts/gallium）是最后一个官方二进制可直接跑在原生 CentOS 7（glibc 2.17）上的 LTS**；
>   Node 17 同样基于 glibc 2.17 构建，但并非 LTS 且早已 EOL。
> - **Node 18+ 官方二进制要求 glibc >= 2.28** —— 需要先按
>   [centos7-upgrade-libc](https://blog.yoooo.fun/centos7-upgrade-libc.html) 把 glibc 升到 2.31，
>   之后就能跑 Node 18/20/22：见
>   [centos7-install-node-18-20](https://blog.yoooo.fun/centos7-install-node-18-20.html)。
> - 当前 Node 官方 LTS 线为 Node 24（active）/ Node 22（maintenance），均需更现代的系统（如 Rocky/Alma 9）。

```bash
# install nvm（当前最新 v0.40.x）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.6/install.sh | bash

# install node
nvm ls-remote --lts
nvm install lts/gallium   # Node 16 —— 原生 CentOS 7 下最后可用的 LTS

# check node version
node -v

# config npm mirror
npm config set registry https://registry.npmmirror.com

# or config .npmrc manually
cat << EOF > ~/.npmrc
home="https://npmmirror.com"
registry="https://registry.npmmirror.com/"
electron_mirror="https://npmmirror.com/mirrors/electron/"
electron_custom_dir="{{ version }}"
electron_builder_binaries_mirror="http://npmmirror.com/mirrors/electron-builder-binaries/"

EOF
```

### install nvim

> 版本说明：较新的 nvim 预编译包对 glibc 要求更高（官方为旧系统提供 "unsupported" 的 older-glibc 构建）。
> 在 CentOS 7 上保持 0.9.5 即可与当前 LunarVim release-1.4 配套使用。

```bash
NVIM_VERSION=0.9.5
wget https://github.com/neovim/neovim/releases/download/v${NVIM_VERSION}/nvim.appimage

chmod u+x nvim.appimage && ./nvim.appimage

# if no error, move it to /usr/local/bin
sudo mv nvim.appimage /usr/local/bin/nvim

# if error
./nvim.appimage --appimage-extract
./squashfs-root/usr/bin/nvim
sudo mv squashfs-root/usr/bin/nvim /usr/local/bin/nvim

# check nvim version
nvim --version
```

### install LunarVim

```bash
LV_BRANCH='release-1.4/neovim-0.9' bash <(curl -s https://raw.githubusercontent.com/LunarVim/LunarVim/release-1.4/neovim-0.9/utils/installer/install.sh)

# add the following to ~/.config/lvim/config.lua
vim.opt.shiftwidth = 4 -- the number of spaces inserted for each indentation
vim.opt.tabstop = 4 -- insert spaces for a tab
```

## 相关文章

- [Use node 18/20 on CentOS 7](https://blog.yoooo.fun/centos7-install-node-18-20.html)
- [How to upgrade glibc on CentOS 7](https://blog.yoooo.fun/centos7-upgrade-libc.html)
