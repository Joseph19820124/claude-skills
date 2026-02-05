# Claude Code Skills Collection

My custom skills for [Claude Code](https://claude.ai/code).

## Skills

| Skill | Description |
|-------|-------------|
| `query-bedrock-bill` | 查询 AWS Bedrock Claude 模型的调用量和费用 |
| `send2github` | 将当前聊天内容汇总并发布到新建的 GitHub 仓库 |
| `brand-guidelines` | Apply Anthropic's official brand colors and typography |
| `comeon` | 为视频创作提供选题思路 |
| `conv2md` | Convert conversation to Markdown |
| `domain-name-brainstormer` | Generate creative domain name ideas |
| `update-cc` | 更新 Claude Code 到最新版本并显示版本差异 |

## Installation

Copy any skill folder to `~/.claude/skills/`:

```bash
# Clone this repo
git clone https://github.com/Joseph19820124/claude-skills.git

# Copy a specific skill
cp -r claude-skills/query-bedrock-bill ~/.claude/skills/

# Or copy all skills
cp -r claude-skills/* ~/.claude/skills/
```

## Usage

After installing, invoke skills in Claude Code using:

```
/skill-name [arguments]
```

For example:
```
/query-bedrock-bill
/send2github public my-repo-name
```

## License

MIT
