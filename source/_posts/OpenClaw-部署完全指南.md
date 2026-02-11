---
title: OpenClaw 部署完全指南：从零搭建你的个人 AI 助手
tags:
  - OpenClaw
  - AI Assistant
  - 部署
  - Docker
date: 2026-02-11 21:00:00
description: OpenClaw 是一个运行在自有设备上的个人 AI 助手，支持多平台、多渠道（WhatsApp、Telegram、Slack、Discord 等）。本文详细介绍几种主流部署方式。
---

OpenClaw 是一个运行在自有设备上的个人 AI 助手，支持多平台、多渠道（WhatsApp、Telegram、Slack、Discord 等），可以在 macOS、Linux、Windows、iOS、Android 上运行。本文详细介绍几种主流部署方式，帮助你快速上手。

## OpenClaw 是什么？

OpenClaw 是本地优先（local-first）的 AI 助手框架，核心是 **Gateway** 控制平面 + **Agent** 运行时。它不是云服务，而是跑在你自己的机器上，所有数据、配置、密钥都在本地。

**核心特性：**
- 多渠道接入：WhatsApp、Telegram、Slack、Discord、Google Chat、Signal、iMessage、Teams、Matrix 等
- 跨平台：macOS/Linux/Windows/iOS/Android
- 多 Agent 路由：可为一对一/群组/不同场景配置不同 Agent
- 安全模型：DM 配对机制、Agent 沙箱、工具权限控制
- 语音唤醒 + Talk 模式：支持 macOS/iOS/Android

## 安装前准备

**运行时要求：**
- Node.js ≥ 22
- 推荐 pnpm（也可以用 npm 或 bun，但 Bun 对 Gateway 有兼容问题）

```bash
# 检查 Node 版本
node -v  # 需要 v22+

# 安装 pnpm（推荐）
npm install -g pnpm
```

## 方式一：本地 CLI 快速部署（最简单）

适合个人使用，本机运行。

### 1. 安装 OpenClaw

```bash
# 全局安装
npm install -g openclaw@latest
# 或
pnpm add -g openclaw@latest
```

### 2. 运行向导

```bash
# 一键安装 Gateway 守护进程并启动向导
openclaw onboard --install-daemon
```

向导会引导你完成：
- Gateway 配置（端口、认证）
- 模型选择（推荐 Anthropic Pro/Max + Opus 4.6）
- 渠道登录（WhatsApp 扫码、Telegram Bot Token 等）
- 工作区初始化

### 3. 启动 Gateway

```bash
# 默认端口 18789
openclaw gateway --port 18789 --verbose

# 或者使用已安装的 daemon
openclaw gateway
```

### 4. 验证部署

```bash
# 发送测试消息
openclaw message send --to +1234567890 --message "Hello from OpenClaw"

# 与 Agent 对话
openclaw agent --message "Ship checklist" --thinking high
```

## 方式二：Docker 容器化部署

适合需要隔离环境、一次性体验、或在 VPS 上运行。

### 快速启动

```bash
# 从仓库根目录运行
./docker-setup.sh

# 脚本会自动：
# 1. 构建 Gateway 镜像
# 2. 运行向导
# 3. 启动 Docker Compose
# 4. 生成 token 写入 .env
```

完成后访问 http://127.0.0.1:18789/，在 Control UI 中粘贴 token。

### 手动 Docker 部署

```bash
# 构建镜像
docker build -t openclaw:local -f Dockerfile .

# 运行向导
docker compose run --rm openclaw-cli onboard

# 启动 Gateway
docker compose up -d openclaw-gateway
```

### 常用 Docker 命令

```bash
# 查看日志
docker compose logs -f openclaw-gateway

# 重启服务
docker compose restart openclaw-gateway

# 获取 Dashboard 链接
docker compose run --rm openclaw-cli dashboard --no-open

# 配置 WhatsApp（扫码）
docker compose run --rm openclaw-cli channels login

# 配置 Telegram
docker compose run --rm openclaw-cli channels add --channel telegram --token "<token>"
```

### Docker 环境变量

```bash
# 持久化容器 home 目录
export OPENCLAW_HOME_VOLUME="openclaw_home"

# 挂载额外目录
export OPENCLAW_EXTRA_MOUNTS="$HOME/.codex:/home/node/.codex:ro,$HOME/github:/home/node/github:rw"

# 安装系统依赖
export OPENCLAW_DOCKER_APT_PACKAGES="ffmpeg git curl jq"

./docker-setup.sh
```

### 权限问题

Docker 镜像以 non-root 用户 node（uid 1000）运行。如果遇到权限错误：

```bash
# Linux 主机上确保挂载目录属于 uid 1000
sudo chown -R 1000:1000 /path/to/openclaw-config /path/to/openclaw-workspace
```

## 方式三：Linux VPS 远程部署

适合 7x24 小时运行，远程访问。

### 1. 安装 Node 22+

```bash
# 使用 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 22
nvm use 22

# 或直接安装
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### 2. 安装并配置 OpenClaw

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

### 3. 设置 systemd 服务

```bash
# 向导会自动安装 systemd user unit
# 或手动创建
cat > ~/.config/systemd/user/openclaw-gateway.service <<EOF
[Unit]
Description=OpenClaw Gateway
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/openclaw gateway --port 18789
Restart=always
RestartSec=5

[Install]
WantedBy=default.target
EOF

# 启用服务
systemctl --user enable --now openclaw-gateway

# 查看状态
systemctl --user status openclaw-gateway
```

### 4. 远程访问（SSH 隧道）

从本地 laptop 端口转发：

```bash
ssh -N -L 18789:127.0.0.1:18789 user@vps-ip
```

然后本地浏览器访问 http://127.0.0.1:18789/ 即可控制远程 Gateway。

### 5. 公网暴露（可选）

通过 Tailscale Serve/Funnel 安全暴露：

```bash
# 配置 gateway.tailscale.mode = "serve" 或 "funnel"
# 需要设置 gateway.auth.mode = "password"
```

## 方式四：从源码开发部署

适合需要修改源码、贡献 PR、或深度定制。

```bash
# 克隆仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 安装依赖
pnpm install

# 构建 UI
pnpm ui:build

# 构建项目
pnpm build

# 运行向导并安装 daemon
pnpm openclaw onboard --install-daemon

# 开发模式（自动重载）
pnpm gateway:watch
```

## Agent 沙箱（Sandboxing）

**重要安全特性**：在群聊/频道中使用 Agent 时，可启用沙箱隔离。

```json
{
  "agents": {
    "defaults": {
      "sandbox": {
        "mode": "non-main",
        "scope": "agent",
        "workspaceAccess": "none",
        "docker": {
          "image": "openclaw-sandbox:bookworm-slim",
          "memory": "1g",
          "memorySwap": "2g"
        },
        "prune": {
          "idleHours": 24,
          "maxAgeDays": 7
        }
      }
    }
  }
}
```

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `openclaw gateway` | 启动 Gateway |
| `openclaw onboard` | 运行设置向导 |
| `openclaw agent --message "..."` | 发送消息给 Agent |
| `openclaw message send --to xxx --message "..."` | 发送渠道消息 |
| `openclaw channels login` | WhatsApp 扫码 |
| `openclaw doctor` | 健康检查与修复 |
| `openclaw status` | 查看状态 |

## 常见问题

**1. Node 版本不满足要求？**
使用 nvm 安装 Node 22+：`nvm install 22`

**2. Docker 权限错误？**
确保挂载目录属于 uid 1000：`sudo chown -R 1000:1000 /path/to/openclaw`

**3. 远程访问 Gateway？**
使用 SSH 隧道或 Tailscale Serve/Funnel

**4. 升级 OpenClaw？**
```bash
npm update -g openclaw@latest
```

## 总结

OpenClaw 提供了灵活多样的部署方式，选择适合你的方式，开始打造属于你的个人 AI 助手吧。

---

*参考资料：*
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw 文档](https://docs.openclaw.ai)