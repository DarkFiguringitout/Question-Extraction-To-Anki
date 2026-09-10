# Claude Code permissions for this repo

`settings.json` here turns off Claude Code's permission prompts for work done
**inside this repository**. It is checked in, so it applies on any machine where
this repo is cloned and Claude Code is run from the project root.

## What it sets

| Key | Effect |
| --- | --- |
| `permissions.defaultMode: "bypassPermissions"` | Sessions start in bypass mode — no approval prompts. |
| `permissions.allow` | Tool-level allow rules (a bare tool name allows every use of that tool). Belt-and-braces: these apply even in modes that still prompt. |
| `permissions.ask: []` | Nothing is forced back to a prompt. |
| `permissions.deny: []` | Nothing is blocked. |
| `enableAllProjectMcpServers: true` | MCP servers defined in `.mcp.json` are approved without asking. |

## Scope — what this file does *not* cover

Settings load user → project → local, so this file only reaches sessions started
in this project. Two things it cannot set for you:

1. **A remote session's mode** (claude.ai/code, the desktop or mobile app). That
   is chosen in the UI when the session is created, not from a settings file.
2. **Other projects.** For machine-wide behaviour, put the same block in
   `~/.claude/settings.json` and add `"skipDangerousModePermissionPrompt": true`
   so the one-time bypass-mode confirmation dialog is pre-accepted.

## Trade-off

Permission prompts are the main thing standing between an agent and an action
you did not intend — a bad `rm`, a force-push, a destructive migration, or an
instruction picked up from repo content, a web page, or a PR comment rather than
from you. With this file in place none of those stop for confirmation.

That is a reasonable trade in a repo you own, working in a disposable container.
It is a much larger one on a machine holding credentials and unrelated work,
which is why the machine-wide version above is a separate, deliberate step.

## Dialling it back

- **Off entirely:** delete `.claude/settings.json`.
- **Prompt only for edits:** change `defaultMode` to `"acceptEdits"`.
- **Keep the quiet, restore the guardrails** — allow everything, block the
  handful of things you never want done unattended:

  ```json
  "deny": ["Bash(rm -rf *)", "Bash(git push --force *)", "Bash(git reset --hard *)"]
  ```

`deny` beats `allow`, and `/permissions` shows the rules in effect.
