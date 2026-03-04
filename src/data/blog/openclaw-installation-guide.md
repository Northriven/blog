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
- **Node.js**: 22.0 或更高版本
- **操作系统**: 
  - macOS 12.0 (Monterey) 或更高
  - Linux (Ubuntu 20.04+, Debian 11+, CentOS 8+ 等)
  - Windows 10/11（推荐 WSL2）
- **包管理器**: npm 或 pnpm

### 检查 Node.js 版本

```bash
node --version
```

如果版本低于 22.0 或未安装，请先安装 Node.js。

---

## macOS 安装

### 方法一：一键安装脚本（推荐）

适合：所有 macOS 用户，特别是新手

```bash
# 1. 运行安装脚本
curl -fsSL https://openclaw.ai/install.sh | bash
```

安装脚本会自动：
- 检测并安装 Node.js 22+
- 安装 OpenClaw CLI
- 启动配置向导

### 方法二：npm 手动安装

适合：已有 Node.js 环境的用户

```bash
# 1. 确认 Node.js 版本
node --version  # 必须是 v22.x 或更高

# 2. 安装 OpenClaw
npm install -g openclaw@latest

# 3. 如果遇到 sharp 构建错误（macOS 常见）
SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm install -g openclaw@latest
```

### 方法三：使用 Homebrew 安装 Node.js

如果未安装 Node.js：

```bash
# 安装 Node.js 22
brew install node@22

# 验证安装
node --version
npm --version

# 安装 OpenClaw
npm install -g openclaw@latest
```

---

## Linux 安装

### Ubuntu/Debian 系统

#### 步骤 1：安装 Node.js

```bash
# 使用 NodeSource 仓库安装 Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node --version
npm --version
```

#### 步骤 2：安装 OpenClaw

```bash
# 方法 A：使用安装脚本（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方法 B：使用 npm
sudo npm install -g openclaw@latest
```

### CentOS/RHEL/Fedora 系统

```bash
# 1. 安装 Node.js 22
curl -fsSL https://rpm.nodesource.com/setup_22.x | sudo bash -
sudo yum install -y nodejs

# 2. 安装 OpenClaw
sudo npm install -g openclaw@latest
```

### 使用 pnpm（可选）

```bash
# 安装 pnpm
curl -fsSL https://get.pnpm.io/install.sh | sh -

# 安装 OpenClaw
pnpm add -g openclaw@latest

# 批准构建脚本（首次安装需要）
pnpm approve-builds -g
```

---

## Windows 安装

### 推荐方案：使用 WSL2

Windows 用户强烈建议使用 WSL2 (Windows Subsystem for Linux) 以获得最佳兼容性。

#### 步骤 1：安装 WSL2

```powershell
# 在 PowerShell（管理员）中运行
wsl --install

# 重启电脑后，WSL2 会自动安装 Ubuntu
```

#### 步骤 2：在 WSL2 中安装

打开 Ubuntu（从开始菜单），然后：

```bash
# 1. 更新包列表
sudo apt update

# 2. 安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# 3. 安装 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash
```

### 原生 Windows 安装（PowerShell）

```powershell
# 1. 安装 Node.js（如果未安装）
# 从 https://nodejs.org 下载并安装 Node.js 22 LTS

# 2. 验证安装
node --version
npm --version

# 3. 使用 PowerShell 脚本安装 OpenClaw
iwr -useb https://openclaw.ai/install.ps1 | iex

# 或使用 npm
npm install -g openclaw@latest
```

### 解决 PATH 问题

如果安装后 `openclaw` 命令不可用：

```powershell
# 查找 npm 全局目录
npm prefix -g

# 将输出路径添加到系统 PATH
# 例如：C:\Users\YourName\AppData\Roaming\npm
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

```bash
# 使用 nvm 管理多个 Node 版本
nvm install 22
nvm alias default 22
nvm use 22

# 验证
node --version  # 应该显示 v22.x.x
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

*最后更新：2026 年 3 月 4 日*
*基于 OpenClaw 官方文档和实际部署经验编写*
