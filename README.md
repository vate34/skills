# Skills

Agent Skills for interacting with Things app on macOS and querying MiniMax API usage.

These skills follow the [Agent Skills specification](https://agentskills.io/specification) so they can be used by any skills-compatible agent, including Claude Code and Codex CLI.

## Installation

```sh
npx skills add https://github.com/vate34/skills
```

## Skills

| Skill | Description |
|-------|-------------|
| [things-cli](skills/things-cli) | Manage todos and projects in Things app using URL schemes. Create, update, complete, and search for todos directly from conversation |
| [minimax-usage](skills/minimax-usage) | 查询 MiniMax API 使用状况。当用户询问 MiniMax 使用情况、额度、消耗统计时触发。 |
