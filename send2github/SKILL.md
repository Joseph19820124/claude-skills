---
name: send2github
description: 将当前聊天内容汇总并发布到新建的 GitHub 仓库
argument-hint: [public|private] [repo-name?]
allowed-tools: Bash(gh *), Bash(git *), Write, Read
disable-model-invocation: true
---

将当前对话内容汇总成 Markdown 文件，创建新的 GitHub 仓库并推送。

## 参数

- `$0`: 仓库可见性 - `public` 或 `private`（必填）
- `$1`: 仓库名称（可选，不传则自动生成）

## 执行步骤

### 1. 参数验证

检查参数是否有效：
- 第一个参数必须是 `public` 或 `private`
- 第二个参数可选

### 2. 获取 GitHub 用户名

```bash
gh api user --jq '.login'
```

### 3. 确定仓库名称

**如果提供了 `$1` 参数**：使用用户指定的名称

**如果未提供 `$1` 参数**：自动生成名称
1. 分析当前对话的主题，提取 2-4 个关键词
2. 生成格式为 `keyword1-keyword2-summary` 或 `topic-name-notes` 的名称
3. 名称规范：
   - 全小写
   - 使用连字符分隔
   - 不超过 50 个字符
   - 只包含字母、数字、连字符

**自动生成名称示例**：
- 对话关于 AWS 费用查询 → `aws-bedrock-billing-notes`
- 对话关于 React 组件开发 → `react-component-dev-summary`
- 对话关于 API 设计 → `api-design-discussion`

### 4. 检查仓库是否已存在

```bash
gh repo list --limit 100 --json name --jq '.[].name'
```

获取用户的所有仓库名称列表，检查生成的名称是否重复。

**如果名称已存在**：
- 自动添加日期后缀：`{name}-20260205`
- 或添加递增数字：`{name}-2`, `{name}-3`
- 继续检查直到找到可用名称

### 5. 创建新仓库

根据第一个参数决定可见性：

```bash
# 如果是 public
gh repo create {repo-name} --public --description "Chat summary from Claude Code" --clone

# 如果是 private
gh repo create {repo-name} --private --description "Chat summary from Claude Code" --clone
```

### 6. 汇总当前对话内容

回顾整个对话历史，生成一个结构化的 Markdown 文档，包含：

- **标题**：根据对话主题生成
- **概述**：简要描述对话的主要内容
- **主要内容**：
  - 用户提出的问题/需求
  - 讨论的关键点
  - 执行的操作和结果
  - 代码片段（如果有）
- **结论/成果**：对话的最终结果或产出
- **时间戳**：记录生成时间

### 7. 写入文件

将汇总内容写入 `README.md` 文件。

如果是 public 仓库，还需要创建 `_config.yml` 用于 GitHub Pages：

```yaml
title: {根据对话主题生成}
description: Chat summary from Claude Code
theme: jekyll-theme-cayman
```

### 8. 提交并推送

```bash
cd {repo-name}
git add -A
git commit -m "Add chat summary

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
git push -u origin master
```

### 9. 启用 GitHub Pages（仅 public 仓库）

```bash
gh api repos/{username}/{repo-name}/pages -X POST -F source[branch]=master -F source[path]=/
```

### 10. 返回结果

输出：
- 使用的仓库名称（特别是自动生成时要明确告知）
- 仓库链接：`https://github.com/{username}/{repo-name}`
- GitHub Pages 链接（如果是 public）：`https://{username}.github.io/{repo-name}/`
- 确认信息

## 示例

```bash
# 指定仓库名
/send2github public my-chat-summary
/send2github private project-notes

# 自动生成仓库名
/send2github public
/send2github private
```

## 注意事项

- 确保已登录 GitHub CLI (`gh auth status`)
- private 仓库不支持 GitHub Pages（除非是 Pro 账户）
- 仓库名应符合 GitHub 命名规范（小写、连字符分隔）
- 自动生成的名称会基于对话主题，确保语义清晰
