---
name: autonomous-work
description: Use when the user says they're stepping away, asks Claude to work independently, or uses trigger words like "自主工作", "自己做", "keep going", "autonomous mode". Enables batch execution without per-step confirmation.
---

# Autonomous Work Mode

## Overview

Enter autonomous mode when the user signals they're stepping away. Execute tasks end-to-end with minimal interruptions. Only pause for destructive operations.

## Trigger Words

**Enter autonomous mode:**
- "自主工作" / "自己做" / "你自己来"
- "keep going" / "autonomous mode" / "just do it"
- "我去开会/吃饭/忙了" + task description
- Any variant meaning "do it yourself, don't wait for me"

**Exit autonomous mode:**
- "暂停" / "停一下" / "等等"
- "pause" / "stop" / "hold on"
- User returns and asks a question (implicit exit)
- On any exit: **clear the statusline indicator** (run the exit command in Status Display section)

## Status Display

When entering autonomous mode, update the claude-hud statusline to show autonomous status. The plugin re-reads config.json every ~300ms, so this is visible immediately in the status bar below the input field.

**Enter autonomous mode — set status:**
```bash
node -e "const fs=require('fs');const p=process.env.HOME+'/.claude/plugins/claude-hud/config.json';const c=JSON.parse(fs.readFileSync(p,'utf8'));c.display=c.display||{};c.display.customLine='🤖 自主模式运行中...';fs.writeFileSync(p,JSON.stringify(c,null,2))"
```

**Exit autonomous mode — clear status:**
```bash
node -e "const fs=require('fs');const p=process.env.HOME+'/.claude/plugins/claude-hud/config.json';const c=JSON.parse(fs.readFileSync(p,'utf8'));if(c.display)delete c.display.customLine;fs.writeFileSync(p,JSON.stringify(c,null,2))"
```

The statusline shows the custom line in orange (color 208) by default, making it clearly visible below the input field.

## Execution Behavior

When in autonomous mode:

1. **Acknowledge entry** — One sentence: "进入自主模式，任务：[summary]。完成后汇报。"
2. **Update statusline** — Run the status display command above to show autonomous mode indicator.
3. **Chain operations** — Don't ask between steps. Read → analyze → implement → test → fix → commit.
4. **Direct web access** — Use WebFetch and WebSearch freely to look up documentation, API references, error messages, or any information needed. No confirmation required.
4. **Make reasonable decisions** — Pick sensible defaults. Don't agonize over choices with clear best answers.
5. **Self-heal** — Tests fail? Debug, fix, re-run. Build breaks? Investigate, fix, rebuild. Up to 3 attempts.
6. **Stop and record** — If stuck after 3 attempts or hit an ambiguous fork with no clear best choice, stop and add to the "open questions" section of your report.
7. **Clear status on exit** — When exiting autonomous mode (completion or user returns), clear the statusline indicator.

## Destructive Operations — MUST PAUSE

Even in autonomous mode, **stop and confirm** before:

- **Deleting files or directories** (`rm`, `rmdir`, `del`)
- **Git force push** (`push --force`, `push -f`)
- **Dropping database tables** or truncating data
- **Overwriting uncommitted user work** (e.g., `git checkout -- file` on modified files)

For these, say: "自主模式暂停：即将执行 [operation]，这不可逆。确认继续？"

## Permission Configuration

Claude Code's permission system may block autonomous execution. Before entering autonomous mode, suggest the user pre-approve common operations:

```
# In .claude/settings.json or via /permissions:
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

Adapt the allowlist to the user's project (e.g., `cargo` for Rust, `go` for Go).

If a permission prompt blocks execution during autonomous mode, include it in your report: "需要你授权 [command] 才能继续。"

## Progress Reporting

When all tasks complete (or you're stuck), report back in this structure:

```
## 自主模式完成报告

### 完成的工作
- [what you did, bullet list]

### 测试结果
- [pass/fail, what was tested]

### 修复的问题（如有）
- [what broke and how you fixed it]

### 需要你确认的事项（如有）
- [open questions, decisions that need human input]

### 下一步建议（可选）
- [what could be done next]
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Every small step asks for confirmation | Chain operations. Only pause for destructive ops. |
| Stuck in infinite fix loop | Max 3 attempts, then stop and report. |
| Picks unsafe defaults to "save time" | When uncertain, prefer the safer option. Stop if truly ambiguous. |
| Forgets to report back | Always produce the completion report. User may not be watching. |
| Doesn't suggest permission config | Ask before starting if common ops aren't pre-approved. |
| Forgets to clear statusline on exit | Always clear `customLine` from config.json when leaving autonomous mode. |
