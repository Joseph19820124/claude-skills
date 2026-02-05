---
name: update-cc
description: 更新 Claude Code 到最新版本，并显示版本差异
allowed-tools: Bash(npm *), Bash(claude *), WebFetch
---

更新 Claude Code CLI 到最新版本，并展示新旧版本的变更内容。

## 执行步骤

### 1. 获取当前版本

```bash
claude --version
```

记录当前版本号。

### 2. 检查最新版本

```bash
npm view @anthropic-ai/claude-code version
```

### 3. 比较版本

- 如果当前版本已是最新，告知用户无需更新
- 如果有新版本可用，继续执行更新

### 4. 执行更新

```bash
npm update -g @anthropic-ai/claude-code
```

### 5. 确认更新成功

```bash
claude --version
```

### 6. 获取版本变更信息

访问 Claude Code 的 changelog 获取版本差异：

使用 WebFetch 工具访问：
- `https://github.com/anthropics/claude-code/releases`
- 或 `https://www.npmjs.com/package/@anthropic-ai/claude-code?activeTab=versions`

提取从旧版本到新版本之间的所有变更内容。

### 7. 输出结果

按以下格式展示：

```
## Claude Code 更新完成

| 项目 | 值 |
|------|-----|
| 旧版本 | x.x.x |
| 新版本 | y.y.y |
| 更新时间 | YYYY-MM-DD HH:MM |

## 版本变更摘要

### vY.Y.Y (最新)
- 新功能 1
- 修复 1
- ...

### vX.X.X → vY.Y.Y 之间的其他版本
- ...
```

## 注意事项

- 需要全局 npm 安装权限（可能需要 sudo）
- 如果使用 nvm 或其他 Node 版本管理器，确保在正确的环境下执行
- 更新后可能需要重启终端或 Claude Code 会话才能使用新版本
