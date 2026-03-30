---
title: "Claude Code 创始人亲授：15 个隐藏功能让你效率翻倍"
mathjax: false
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Claude Code
  - AI
  - 开发工具
  - 效率
---

> 本文翻译自 Claude Code 创始人 Boris Cherny（[@bcherny](https://x.com/bcherny)）于 2026 年 3 月 30 日发布的 Twitter/X 长推文，原文链接：[https://x.com/bcherny/status/2038454336355999749](https://x.com/bcherny/status/2038454336355999749)

Boris Cherny 是 Claude Code 的创造者，目前担任 Anthropic Claude Code 负责人。在这条推文里，他分享了自己最喜欢的 15 个隐藏和未被充分利用的功能——都是他日常高频使用的。

<!--more-->

---

## 1. Claude Code 有移动端 App

很多人不知道，iOS / Android 的 Claude App 内置了一个专属的 **Code 标签页**。

你可以直接在手机上：
- 查看代码改动（diff）
- 审批 Pull Request
- 编写代码

不需要打开笔记本电脑，Boris 表示他有相当一部分代码就是在 iOS App 上写的。

---

## 2. 在移动端 / 网页 / 桌面 / 终端之间无缝切换会话

Claude Code 支持跨设备接管会话，核心是两个命令：

| 命令 | 作用 |
|------|------|
| `claude --teleport` 或 `/teleport` | 将云端会话拉取到本地终端继续 |
| `/remote-control` | 在手机或网页上远程控制本地正在运行的会话 |

Boris 在 `/config` 中开启了「所有会话默认启用 Remote Control」，这样他可以随时随地从手机接管本地的 Claude 实例。

---

## 3. `/loop` 和 `/schedule`——最强大的两个功能

这两个命令可以让 Claude 按照设定的时间间隔**自动执行任务**，最长可以持续一周。

Boris 自己正在跑的循环任务举例：

```
/loop 5m /babysit        # 每 5 分钟自动处理代码审查、推动 PR 合入
/loop 30m /slack-feedback  # 每 30 分钟从 Slack 反馈自动创建 PR
/loop /post-merge-sweeper  # 合并后自动处理遗漏的 review 评论
/loop 1h /pr-pruner       # 每小时清理过期的 PR
```

这相当于让 Claude 在后台持续帮你做那些重复性的工程师日常工作。

---

## 4. 用 Hooks 确定性地执行自定义逻辑

Hooks 允许你在 Agent 生命周期的关键节点注入自己的逻辑：

| Hook 类型 | 触发时机 | 使用示例 |
|-----------|---------|---------|
| `SessionStart` | 会话启动时 | 动态加载上下文信息 |
| `PreToolUse` | 每次工具调用前 | 记录 Claude 执行的每一条 bash 命令 |
| `PermissionRequest` | 需要权限审批时 | 将审批请求路由到 WhatsApp |
| `Stop` | Claude 停止时 | 提示 Claude 继续未完成的任务 |

Hooks 的核心价值是**确定性**——不依赖 Claude 自己判断，而是强制执行你的业务逻辑。

---

## 5. Cowork Dispatch：远程遥控桌面 App

Cowork Dispatch 是 Claude Desktop App 的安全远程控制功能。

当你不在电脑前时，它可以让 Claude 帮你处理：
- Slack 消息追赶（catch-up）
- 邮件管理
- 文件操作

相当于给你的电脑配了一个永远在线的 AI 助理。

---

## 6. 前端开发用 Chrome 扩展

Boris 的核心原则：**给 Claude 一种方式验证它自己的输出**。

对于前端开发，浏览器访问能力是关键——让 Claude 能看到页面实际渲染的样子，并不断迭代直到结果满意。Boris 偏好使用 Chrome 扩展，因为稳定性更好。

---

## 7. 用 Claude Desktop App 自动启动并测试 Web 服务器

Desktop App 会自动启动 Web 服务器，并在内置浏览器中测试，大幅简化前端开发流程。

你不再需要手动 `npm run dev` 然后切窗口查看效果——Claude 会自己跑起来、自己看。

---

## 8. 分叉（Fork）你的会话

当你想在不影响当前进度的情况下尝试不同方向，可以分叉会话：

**方法一**：在会话中运行 `/branch`（之后你就处于分支会话中）

**方法二**：CLI 命令：
```bash
claude --resume <session-id> --fork-session
```

想回到原始会话：
```bash
claude -r <original-session-id>
```

这对于探索性编程非常有用，就像 git branch 一样，试错没有任何代价。

---

## 9. 用 `/btw` 插入临时提问

`/btw` 命令允许你在 Agent 正在执行任务的过程中，**提一个临时问题而不打断当前工作流**。

例如 Claude 正在重构一个模块，你突然想问一个不相关的问题，用 `/btw` 即可，不会打乱它的进度。

---

## 10. 使用 Git Worktrees

Claude Code 对 git worktree 有深度集成。Boris 用这个功能同时跑**几十个并行的 Claude 实例**：

```bash
claude -w                              # 启动一个 worktree 会话
claude --resume <id> --fork-session    # 分叉到新 worktree
```

Desktop App 也有 worktree 勾选框。对于非 git 版本控制系统，还支持 `WorktreeCreate` Hook。

---

## 11. 用 `/batch` 处理大规模变更

`/batch` 命令将任务**分散到数十、数百甚至数千个 worktree agent** 上并行执行。

每个 agent 在自己独立的代码库副本上工作，特别适合：
- 大规模代码迁移（比如升级 API 版本）
- 批量重构
- 跨仓库的统一改动

---

## 12. 用 `--bare` 加速 SDK 启动最多 10 倍

`--bare` 标志跳过搜索本地配置文件的过程。

对于**非交互式 SDK 使用场景**（比如在 CI 中调用 Claude Code），显式指定 system prompt、MCP 配置和 settings，然后加上 `--bare`，可以将启动速度提升最多 10 倍。

---

## 13. 用 `--add-dir` 给 Claude 访问更多目录

```bash
claude --add-dir /path/to/other/repo
```

或在会话中：
```
/add-dir /path/to/other/repo
```

也可以在团队的 `settings.json` 中添加 `additionalDirectories` 字段，让所有人默认共享同样的目录访问权限。

---

## 14. 用 `--agent` 给 Claude Code 定制系统提示和工具

在 `.claude/agents/` 目录下定义自定义 Agent，然后用以下命令启动：

```bash
claude --agent=<agent-name>
```

自定义 Agent 支持：
- **限制可用工具**（比如只读模式）
- **自定义 system prompt**
- **指定特定模型**
- **领域专属配置**

适合打造针对特定任务的专用 Agent，比如「只能读、不能写」的代码审查 Agent。

---

## 15. 用 `/voice` 开启语音输入

Boris 表示他**主要靠说话而不是打字来编程**：

| 平台 | 操作方式 |
|------|---------|
| CLI | 运行 `/voice`，按住空格键说话 |
| Desktop App | 点击语音按钮 |
| iOS | 在系统设置中启用听写（Dictation） |

语音输入让你在走路、站立或者不方便打字的时候也能继续写代码。

---

## 总结

Boris 这 15 个功能，涵盖了**移动化**、**自动化**、**并行化**和**定制化**四大方向：

| 方向 | 核心功能 |
|------|---------|
| 移动化 | Mobile App、Teleport、Remote Control、Voice |
| 自动化 | Loop/Schedule、Hooks、Cowork Dispatch |
| 并行化 | Git Worktrees、/batch、并行 Session |
| 定制化 | --agent、--bare、--add-dir、自定义 Hooks |

Claude Code 的设计哲学是「没有唯一正确的用法」，这些功能都是积木，组合方式完全取决于你的工作流。

---

*原文：[Boris Cherny (@bcherny) on X — 2026-03-30](https://x.com/bcherny/status/2038454336355999749)*
