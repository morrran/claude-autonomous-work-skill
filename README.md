# Claude Code Autonomous Work Skill

A skill for [Claude Code](https://claude.ai/code) that enables autonomous execution mode — batch operations without per-step confirmation.

## Features

- **Trigger-based activation** — Say "自主工作", "自己做", "keep going", or "autonomous mode"
- **Chain operations** — Read, analyze, implement, test, fix, commit without pausing
- **Direct web access** — Use WebFetch/WebSearch freely to look up documentation and references
- **Self-healing** — Auto-debug and retry on failures (up to 3 attempts)
- **Status display** — Shows autonomous mode indicator via claude-hud
- **Safety guardrails** — Still pauses for destructive operations (rm, force push, etc.)

## Installation

Copy `SKILL.md` to your Claude Code skills directory:

```
~/.claude/skills/autonomous-work/SKILL.md
```

## Usage

Enter autonomous mode:
- "自主工作" / "自己做" / "keep going" / "autonomous mode"

Exit autonomous mode:
- "暂停" / "停一下" / "pause" / "stop"

## Recommended Permissions

Add to `.claude/settings.json` for smooth autonomous execution:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test*)",
      "Bash(npm run build*)",
      "Bash(git *)",
      "Bash(python *)",
      "Read",
      "Edit",
      "Write",
      "WebFetch",
      "WebSearch"
    ]
  }
}
```

## License

MIT
