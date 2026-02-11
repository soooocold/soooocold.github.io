---
title: OpenCode：开源 AI 编程助手完全指南
tags:
  - OpenCode
  - AI
  - 编程工具
date: 2026-02-11 21:34:14
---
## OpenCode 是什么？

OpenCode 是一个开源的 AI 编程助手，可以运行在终端、桌面应用或 IDE 扩展中。它不是另一个闭源的 AI 编程工具，而是真正开源透明的选择。

**核心特点：**
- **100,000+ GitHub Stars** — 开源社区的认可
- **700+ 贡献者** — 活跃的开发者社区
- **2,500,000+ 月活开发者** — 被广泛使用
- **9,000+ Commits** — 持续迭代

## 为什么选择 OpenCode？

### 1. 隐私优先

OpenCode 的核心理念之一是隐私。它**不存储任何代码或上下文数据**，这意味着你可以在敏感环境中安全使用它。对于处理商业机密或个人项目的开发者来说，这一点至关重要。

### 2. 任意模型支持

OpenCode 不绑定特定模型提供商，你可以：

- **免费内置模型** — 开箱即用
- **主流模型** — Claude、GPT-4、Gemini 等
- **75+ 提供商** — 通过 Models.dev 支持，包括本地模型
- **GitHub Copilot** — 直接登录使用你的 Copilot 账户
- **ChatGPT Plus/Pro** — 直接使用你的 OpenAI 账户

### 3. 多端可用

- **终端界面** — 键盘操作，高效编程
- **桌面应用** — 独立窗口，更好的视觉体验
- **IDE 扩展** — 无缝融入你的开发环境

### 4. 企业级功能

- **多会话** — 在同一项目中并行启动多个 agent
- **会话分享** — 生成链接分享给团队成员，便于协作和 debug
- **Zen 模式** — 经过验证和基准测试的优化模型列表

## 安装方式

OpenCode 支持多种安装方式，总有一种适合你。

### 方式一：安装脚本（推荐）

```bash
curl -fsSL https://opencode.ai/install | bash
```

### 方式二：Node.js

```bash
npm install -g opencode-ai
```

### 方式三：Homebrew（macOS/Linux）

```bash
brew install anomalyco/tap/opencode
```

### 方式四：Docker

```bash
docker run -it --rm ghcr.io/anomalyco/opencode
```

### 方式五：其他包管理器

**Arch Linux：**
```bash
paru -S opencode-bin
```

**Windows Chocolatey：**
```bash
choco install opencode
```

**Windows Scoop：**
```bash
scoop install opencode
```

**Windows npm：**
```bash
npm install -g opencode-ai
```

**使用 Mise：**
```bash
mise use -g github:anomalyco/opencode
```

## 快速上手

### 1. 配置 API Key

首次使用需要配置模型提供商的 API Key：

```bash
opencode
```

在 TUI 中运行：
```
/connect
```

然后访问 https://opencode.ai/auth 登录并获取 API Key。

如果你不确定选择哪个模型，推荐使用 [OpenCode Zen](https://opencode.ai/zen)，它是团队经过测试验证的优化模型列表。

### 2. 初始化项目

进入你的项目目录并初始化 OpenCode：

```bash
cd /path/to/your/project
opencode
/init
```

初始化会分析你的项目结构并创建 `AGENTS.md` 文件，帮助 OpenCode 理解项目架构和编码规范。

### 3. 开始使用

现在你可以开始与 OpenCode 对话了！

## 使用场景示例

### 场景一：理解代码库

```
How is authentication handled in @packages/functions/src/api/index.ts
```

OpenCode 会分析指定文件并解释认证逻辑，非常适合接手他人项目时快速了解代码。

### 场景二：添加新功能

**Step 1：创建计划（Plan Mode）**

按 Tab 键切换到 Plan 模式，然后描述你想要的功能：

```
When a user deletes a note, we'd like to flag it as deleted in the database.
Then create a screen that shows all the recently deleted notes.
From this screen, the user can undelete a note or permanently delete it.
```

OpenCode 会生成详细的实现计划，你可以迭代修改。

**Step 2：添加设计参考**

```
Take a look at this image and use it as a reference.
```

直接拖拽图片到终端，OpenCode 会扫描图像并将其纳入提示词。

**Step 3：构建功能（Build Mode）**

确认计划后，按 Tab 切换回 Build 模式：

```
Sounds good! Go ahead and make the changes.
```

### 场景三：直接修改代码

对于简单的修改，可以直接让 OpenCode 执行：

```
We need to add authentication to the /settings route.
Take a look at how this is handled in the /notes route in
@packages/functions/src/notes.ts and implement the same logic in
@packages/functions/src/settings.ts
```

### 场景四：撤销更改

如果 OpenCode 的修改不符合预期，可以使用撤销：

```
/undo  # 撤销上次修改
/redo  # 重做
```

## 进阶配置

### LSP 自动加载

OpenCode 会自动为你的项目加载正确的语言服务器（LSP），无需手动配置。

### 会话分享

与团队成员分享当前对话：

```
/share
```

这会生成一个链接，你可以发送给队友，他们可以查看完整的对话历史和上下文。

### 自定义选项

OpenCode 提供了丰富的自定义选项：

- **主题** — 切换外观配色
- **快捷键** — 自定义按键绑定
- **代码格式化** — 配置格式化工具
- **自定义命令** — 创建常用命令
- **配置文件** — 详细的行为配置

## Zen：经过验证的模型

如果你对模型选择感到困惑，Zen 提供了经过 OpenCode 团队严格测试和基准测试的优化模型列表。这些模型在编程任务中表现一致且高质量，省去了你自己筛选和测试的麻烦。

## 与其他工具对比

| 特性 | OpenCode | GitHub Copilot | Cursor |
|------|----------|----------------|--------|
| 开源 | ✅ | ❌ | ❌ |
| 隐私优先 | ✅ | ⚠️ | ⚠️ |
| 多模型支持 | ✅ 75+ | 仅 OpenAI | 主要 Claude |
| 自托管 | ✅ | ❌ | ❌ |
| 免费使用 | ✅ | ❌ | 有限制 |
| 多会话 | ✅ | ❌ | ❌ |

## 适用场景

OpenCode 特别适合以下场景：

1. **隐私敏感项目** — 金融、医疗、企业内部应用
2. **多模型需求** — 需要根据任务切换不同模型
3. **团队协作** — 会话分享功能便于代码审查
4. **预算有限** — 免费内置模型可用
5. **技术极客** — 开源、可定制、可自托管

## 总结

OpenCode 代表了 AI 编程工具的一个新方向：开源、隐私优先、灵活自由。它不是要取代你的思考，而是像一位随时待命的初级开发者，听从你的指令，执行具体任务。

如果你正在寻找一个可以完全掌控、隐私安全、多模型支持的 AI 编程助手，OpenCode 值得一试。

---

*参考资料：*
- [OpenCode 官网](https://opencode.ai)
- [OpenCode 文档](https://opencode.ai/docs)
- [OpenCode GitHub](https://github.com/opencode-ai/opencode)