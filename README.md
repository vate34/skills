# Things Skills

Agent Skills for interacting with Things app on macOS using URL schemes.

These skills follow the [Agent Skills specification](https://agentskills.io/specification) so they can be used by any skills-compatible agent, including Claude Code and Codex CLI.

## Installation

### OpenCode

Clone the repo into the OpenCode skills directory:

```sh
git clone https://github.com/vate34/skills.git ~/.opencode/skills/vate34-skills
```

OpenCode auto-discovers all `SKILL.md` files under `~/.opencode/skills/`.

## Skills

| Skill | Description |
|-------|-------------|
| [things-cli](skills/things-cli) | Manage todos and projects in Things app using URL schemes. Create, update, complete, and search for todos directly from conversation |
