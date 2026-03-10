---
author: HONG
pubDatetime: 2026-03-04T14:28:00+08:00
title: OpenClaw 完整安装教程（Windows/Linux/macOS）
slug: openclaw-installation-guide
featured: false
draft: false
tags:
  - openclaw
  - installation
  - tutorial
  - devops
description: 详细的 OpenClaw 安装教程，覆盖 Windows、Linux、macOS 三大平台，包含多种安装方法、配置指南和常见问题解决方案。
---

> 🦞 **OpenClaw** — 自托管 AI 网关，将 WhatsApp、Telegram、Discord、iMessage 等聊天应用连接到 AI 助手
> 
> 本文档基于官方文档和实际部署经验编写，确保所有步骤真实有效。

---

## 📋 目录

1. [系统要求](#系统要求)
2. [macOS 安装](#macos-安装)
3. [Linux 安装](#linux-安装)
4. [Windows 安装](#windows-安装)
5. [首次配置](#首次配置)
6. [验证安装](#验证安装)
7. [常见问题](#常见问题)

---

## 系统要求

### 硬件要求
- **CPU**: 任意 64 位处理器（Intel/AMD/Apple Silicon）
- **内存**: 最低 2GB，推荐 4GB+
- **存储**: 至少 500MB 可用空间

### 软件要求
- **Node.js**: 22.0 或更高版本（LTS）
- **操作系统**: 
  - macOS 12.0 (Monterey) 或更高
  - Linux (Ubuntu 20.04+, Debian 11+, CentOS 8+ 等)
  - Windows 10/11（推荐 WSL2）
- **包管理器**: npm、pnpm 或 yarn

### 检查 Node.js 版本

```bash
node --version
npm --version
```

如果版本低于 22.0 或未安装，请先安装 Node.js。推荐使用版本管理工具（nvm/fnm）来管理 Node.js 版本。

---

## macOS 安装

### 步骤 1：安装 Node.js

根据你的需求选择以下任一方法：

#### 方法 A：使用 nvm（推荐，适合开发者）

nvm（Node Version Manager）允许你轻松切换和管理多个 Node.js 版本。

```bash
# 1. 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 2. 重启终端或执行
source ~/.zshrc

# 3. 安装 Node.js 22 LTS
nvm install 22
nvm alias default 22

# 4. 验证安装
node --version
npm --version
```

**常用 nvm 命令：**
```bash
nvm list              # 查看已安装的版本
nvm use 22            # 切换到 Node.js 22
nvm install 20        # 安装其他版本
nvm uninstall 18      # 卸载旧版本
```

#### 方法 B：使用 fnm（快速替代方案）

fnm 是 Rust 编写的快速 Node.js 版本管理器。

```bash
# 1. 使用 Homebrew 安装 fnm
brew install fnm

# 2. 配置 shell（添加到 ~/.zshrc）
echo 'eval "$(fnm env --use-on-cd --shell zsh)"' >> ~/.zshrc
source ~/.zshrc

# 3. 安装 Node.js 22
fnm install 22
fnm default 22

# 4. 验证
node --version
```

#### 方法 C：使用 Homebrew（简单直接）

```bash
# 安装 Node.js 22 LTS
brew install node@22

# 验证安装
node --version
npm --version
```

> ⚠️ **注意**：Homebrew 安装的 Node.js 可能需要管理员权限才能安装全局 npm 包。

#### 方法 D：官方安装包

1. 访问 [https://nodejs.org](https://nodejs.org)
2. 下载 macOS 安装包（.pkg）
3. 双击运行，按提示完成安装
4. 验证：`node --version`

### 步骤 2：安装 OpenClaw

```bash
# 方法一：使用 npm（推荐）
npm install -g openclaw@latest

# 方法二：使用 pnpm（更快）
pnpm add -g openclaw@latest

# 方法三：一键安装脚本（自动处理依赖）
curl -fsSL https://openclaw.ai/install.sh | bash
```

### 解决常见问题

#### Sharp 构建错误（macOS 常见）

```bash
# 忽略全局 libvips 检查
SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm install -g openclaw@latest

# 或安装 Xcode 命令行工具
xcode-select --install
```

#### 权限错误

```bash
# 配置 npm 使用用户目录
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

---

## Linux 安装

### Ubuntu/Debian 系统

#### 步骤 1：安装 Node.js

选择以下任一方法：

**方法 A：使用 nvm（推荐，无需 sudo）**

```bash
# 1. 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 2. 重启终端或执行
source ~/.bashrc  # 或 source ~/.zshrc

# 3. 安装 Node.js 22 LTS
nvm install 22
nvm alias default 22

# 4. 验证
node --version
npm --version
```

**方法 B：使用 NodeSource 仓库（系统级安装）**

```bash
# 1. 下载并运行设置脚本
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# 2. 安装 Node.js
sudo apt-get install -y nodejs

# 3. 验证
node --version
npm --version
```

**方法 C：使用官方二进制文件**

```bash
# 1. 下载 Node.js 22 LTS（根据架构选择）
cd /tmp
wget https://nodejs.org/dist/v22.12.0/node-v22.12.0-linux-x64.tar.xz

# 2. 解压
tar -xf node-v22.12.0-linux-x64.tar.xz

# 3. 移动到系统目录
sudo cp -r node-v22.12.0-linux-x64/* /usr/local/

# 4. 验证
node --version
npm --version
```

#### 步骤 2：安装 OpenClaw

```bash
# 方法 A：使用安装脚本（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方法 B：使用 npm（nvm 安装时无需 sudo）
npm install -g openclaw@latest

# 方法 C：使用 pnpm
curl -fsSL https://get.pnpm.io/install.sh | sh -
pnpm add -g openclaw@latest
```

### CentOS/RHEL/Fedora 系统

#### 步骤 1：安装 Node.js

**方法 A：使用 nvm**

```bash
# 1. 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc

# 2. 安装 Node.js 22
nvm install 22
nvm alias default 22
```

**方法 B：使用 NodeSource 仓库**

```bash
# CentOS/RHEL
curl -fsSL https://rpm.nodesource.com/setup_22.x | sudo bash -
sudo yum install -y nodejs

# Fedora
curl -fsSL https://rpm.nodesource.com/setup_22.x | sudo bash -
sudo dnf install -y nodejs
```

#### 步骤 2：安装 OpenClaw

```bash
# 使用 npm
npm install -g openclaw@latest

# 或使用安装脚本
curl -fsSL https://openclaw.ai/install.sh | bash
```

### Arch Linux/Manjaro

```bash
# 1. 安装 Node.js（使用 AUR 或官方仓库）
sudo pacman -S nodejs npm

# 或使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc
nvm install 22

# 2. 安装 OpenClaw
npm install -g openclaw@latest
```

### 解决常见问题

#### 权限错误（EACCES）

```bash
# 方案 1：使用 nvm（推荐，避免 sudo）
# 参考上面的 nvm 安装步骤

# 方案 2：更改 npm 默认目录
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

#### 缺少构建依赖

```bash
# Ubuntu/Debian
sudo apt-get install -y build-essential python3

# CentOS/RHEL
sudo yum groupinstall "Development Tools"
sudo yum install -y python3

# Fedora
sudo dnf groupinstall "Development Tools"
sudo dnf install -y python3
```

---

## Windows 安装

### 推荐方案：使用 WSL2

Windows 用户强烈建议使用 WSL2 (Windows Subsystem for Linux) 以获得最佳兼容性和性能。

#### 步骤 1：安装 WSL2

```powershell
# 在 PowerShell（管理员）中运行
wsl --install

# 重启电脑后，WSL2 会自动安装 Ubuntu
# 首次启动时需要设置用户名和密码
```

> 💡 **提示**：如果 `wsl --install` 不工作，手动启用：
> ```powershell
> # 启用 WSL 功能
> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
> 
> # 启用虚拟机平台
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
> 
> # 重启后设置 WSL2 为默认
> wsl --set-default-version 2
> ```

#### 步骤 2：在 WSL2 中安装 Node.js

打开 Ubuntu（从开始菜单），然后：

**方法 A：使用 nvm（推荐）**

```bash
# 1. 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 2. 重启终端或执行
source ~/.bashrc

# 3. 安装 Node.js 22
nvm install 22
nvm alias default 22

# 4. 验证
node --version
npm --version
```

**方法 B：使用 NodeSource**

```bash
# 1. 更新包列表
sudo apt update

# 2. 安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# 3. 验证
node --version
```

#### 步骤 3：在 WSL2 中安装 OpenClaw

```bash
# 方法 A：使用安装脚本（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方法 B：使用 npm
npm install -g openclaw@latest
```

### 原生 Windows 安装（PowerShell/CMD）

#### 步骤 1：安装 Node.js

**方法 A：官方安装程序（推荐）**

1. 访问 [https://nodejs.org](https://nodejs.org)
2. 下载 **Windows Installer (.msi)** - Node.js 22 LTS
3. 双击运行，按提示完成安装
4. 验证：
   ```powershell
   node --version
   npm --version
   ```

**方法 B：使用 Winget（Windows 包管理器）**

```powershell
# 安装 Node.js 22 LTS
winget install OpenJS.NodeJS.LTS

# 验证
node --version
```

**方法 C：使用 Chocolatey**

```powershell
# 安装 Chocolatey（如果未安装）
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# 安装 Node.js
choco install nodejs-lts

# 验证
node --version
```

#### 步骤 2：安装 OpenClaw

```powershell
# 方法 A：使用 npm
npm install -g openclaw@latest

# 方法 B：使用 PowerShell 脚本
iwr -useb https://openclaw.ai/install.ps1 | iex

# 方法 C：使用 pnpm
npm install -g pnpm
pnpm add -g openclaw@latest
```

### 解决常见问题

#### `openclaw` 命令不可用

```powershell
# 1. 查找 npm 全局目录
npm prefix -g
# 输出示例：C:\Users\YourName\AppData\Roaming\npm

# 2. 将该路径添加到系统 PATH
# 方法 A：临时添加（当前会话）
$env:Path += ";C:\Users\YourName\AppData\Roaming\npm"

# 方法 B：永久添加（PowerShell）
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Users\YourName\AppData\Roaming\npm", "User")

# 方法 C：通过系统设置
# 1. 搜索"环境变量"
# 2. 编辑"Path"变量
# 3. 添加 npm 全局目录路径
```

#### Sharp 构建错误

```powershell
# 安装 Windows 构建工具
npm install -g windows-build-tools

# 或设置环境变量后重新安装
$env:SHARP_IGNORE_GLOBAL_LIBVIPS = "1"
npm install -g openclaw@latest
```

#### PowerShell 执行策略限制

```powershell
# 允许执行脚本（管理员权限）
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# 确认更改
Y
```

---

## 首次配置

### 运行配置向导

安装完成后，运行：

```bash
openclaw onboard --install-daemon
```

向导会引导你完成：
1. **API 密钥配置** - 选择 AI 提供商（OpenAI、Anthropic、Google 等）
2. **网关设置** - 端口、监听地址等
3. **渠道配置** - WhatsApp、Telegram 等（可选）
4. **服务安装** - 将 Gateway 安装为系统服务

### 手动配置（跳过向导）

```bash
# 1. 创建配置文件
mkdir -p ~/.openclaw

# 2. 编辑配置
cat > ~/.openclaw/openclaw.json << 'EOF'
{
  "pi": {
    "provider": "your-provider",
    "apiKey": "your-api-key"
  },
  "gateway": {
    "port": 18789
  }
}
EOF
```

---

## 验证安装

### 检查 Gateway 状态

```bash
# 查看服务状态
openclaw gateway status

# 如果未运行，启动 Gateway
openclaw gateway --port 18789
```

### 打开控制面板

```bash
# 在浏览器中打开控制面板
openclaw dashboard

# 或手动访问
# http://127.0.0.1:18789/
```

### 运行诊断

```bash
# 检查配置问题
openclaw doctor

# 查看整体状态
openclaw status
```

### 发送测试消息

```bash
# 需要已配置的渠道
openclaw message send --target "+15555550123" --message "Hello from OpenClaw"
```

---

## 常见问题

### 1. `openclaw: command not found`

**原因**: npm 全局目录不在 PATH 中

**解决方案**:

```bash
# macOS/Linux
export PATH="$(npm prefix -g)/bin:$PATH"
echo 'export PATH="$(npm prefix -g)/bin:$PATH"' >> ~/.zshrc

# Windows PowerShell
$env:Path += ";$(npm prefix -g)"
```

### 2. Sharp 构建错误

**错误信息**: `sharp: Please add node-gyp to your dependencies`

**解决方案**:

```bash
# macOS
SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm install -g openclaw@latest

# 或安装构建工具
npm install -g node-gyp
```

### 3. 权限错误（Linux）

**错误信息**: `EACCES: permission denied`

**解决方案**:

```bash
# 使用 npm 配置（不推荐 sudo）
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH

# 或使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 22
nvm use 22
```

### 4. Gateway 无法启动

**检查步骤**:

```bash
# 1. 查看端口是否被占用
lsof -i :18789  # macOS/Linux
netstat -ano | findstr :18789  # Windows

# 2. 更换端口
openclaw gateway --port 18790

# 3. 查看详细日志
openclaw gateway --port 18789 --verbose
```

### 5. Node.js 版本不兼容

**使用 nvm 管理多个 Node 版本：**

```bash
# 安装 Node.js 22
nvm install 22

# 设置为默认版本
nvm alias default 22

# 切换到 Node.js 22
nvm use 22

# 验证
node --version  # 应该显示 v22.x.x
```

**使用 fnm：**

```bash
# 安装 Node.js 22
fnm install 22

# 设置为默认
fnm default 22

# 验证
node --version
```

### 6. 验证 Node.js 安装

```bash
# 检查版本
node --version  # 应该是 v22.x.x
npm --version   # 应该是 10.x 或更高

# 检查安装路径
which node      # macOS/Linux
where node      # Windows

# 测试运行
node -e "console.log('Node.js is working!')"
```

---

## 高级配置

### 环境变量

```bash
# 自定义配置和状态目录
export OPENCLAW_HOME="/path/to/home"
export OPENCLAW_STATE_DIR="/path/to/state"
export OPENCLAW_CONFIG_PATH="/path/to/config.json"
```

### 配置文件位置

- **macOS/Linux**: `~/.openclaw/openclaw.json`
- **Windows**: `C:\Users\YourName\.openclaw\openclaw.json`

### 日志文件

```bash
# 查看 Gateway 日志
openclaw logs

# 日志位置
# ~/.openclaw/logs/
```

---

## 更新与卸载

### 更新 OpenClaw

```bash
# 使用 npm
npm update -g openclaw

# 使用安装脚本
curl -fsSL https://openclaw.ai/install.sh | bash

# 强制重新安装
npm install -g openclaw@latest --force
```

### 卸载 OpenClaw

```bash
# 1. 停止服务
openclaw gateway stop

# 2. 卸载 npm 包
npm uninstall -g openclaw

# 3. 删除配置（可选）
rm -rf ~/.openclaw
```

---

## 下一步

安装完成后，你可以：

1. **连接聊天渠道** - [WhatsApp](/channels/whatsapp) | [Telegram](/channels/telegram) | [Discord](/channels/discord)
2. **配置 AI 提供商** - 设置你的首选 AI 模型
3. **探索控制面板** - `openclaw dashboard`
4. **阅读完整文档** - https://docs.openclaw.ai

---

## 获取帮助

- **官方文档**: https://docs.openclaw.ai
- **GitHub 仓库**: https://github.com/openclaw/openclaw
- **Discord 社区**: https://discord.com/invite/clawd
- **问题反馈**: https://github.com/openclaw/openclaw/issues

---

*最后更新：2026 年 3 月 10 日*
*基于 OpenClaw 官方文档和实际部署经验编写*

---

## 📚 附录：Node.js 版本管理工具对比

| 工具 | 平台 | 优点 | 缺点 |
|------|------|------|------|
| **nvm** | macOS/Linux/WSL | 成熟稳定，社区广泛 | Windows 支持有限 |
| **fnm** | 全平台 | 速度快，Rust 编写 | 相对较新 |
| **nvm-windows** | Windows | 原生 Windows 支持 | 功能较少 |
| **Volta** | 全平台 | 自动切换版本，项目级配置 | 学习曲线稍陡 |
| **Homebrew** | macOS | 简单易用 | 可能需要 sudo |

**推荐选择：**
- **macOS/Linux 开发者** → nvm
- **Windows 用户** → WSL2 + nvm 或 fnm
- **追求速度** → fnm
- **企业环境** → Volta（项目级版本锁定）
