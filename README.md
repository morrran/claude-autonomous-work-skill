# Claude Code Autonomous Work Skill

A skill for [Claude Code](https://claude.ai/code) that enables autonomous execution mode — task queue scheduling with parallel sub-agent execution, timeout handling, and session recovery.

## Features

- **Trigger-based activation** — Say "自主工作", "自己做", "keep going", or "autonomous mode"
- **Task queue scheduling** — Parse multiple tasks into a queue, auto-detect dependencies
- **Parallel execution** — Dispatch independent tasks as parallel subagents
- **Dynamic task insertion** — Add new tasks mid-execution
- **Timeout & error handling** — 3-strike protocol, timeout detection, strategy mutation
- **Session recovery** — Resume interrupted tasks via planning-with-files integration
- **Direct web access** — Use WebFetch/WebSearch freely to look up documentation
- **Status display** — Shows autonomous mode indicator via claude-hud
- **Safety guardrails** — Still pauses for destructive operations (rm, force push, etc.)

## Dependencies

- **planning-with-files** — Task persistence, session recovery, error handling
- **claude-hud** — Statusline display (optional)

## Installation

Copy to your Claude Code skills directory:

```
~/.claude/skills/autonomous-work/SKILL.md
```

## Usage

Enter autonomous mode:
- "自主工作" / "自己做" / "keep going" / "autonomous mode"
- Then list your tasks: "做这5件事：1. ... 2. ... 3. ..."

Add tasks mid-execution:
- "再加一个任务：..."

Exit autonomous mode:
- "暂停" / "停一下" / "pause" / "stop"

Resume interrupted tasks:
- "继续之前的任务" / "resume"

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
      "Bash(pip install*)",
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
