# Harness config

The harness today is Claude Code, and its config sits in `~/.claude/`. What
follows is what keeps an agent quick and predictable across every project on the
box. It belongs here, not in any one project, because it applies to all of them.

## settings.json

`~/.claude/settings.json` carries permissions, hooks, and defaults. Keep it thin.

- Permissions. Allowlist the read-only and idempotent commands you run a hundred
  times a day (`git`, `gh`, `journalctl`, `systemctl status`, `ls`, search) so the
  agent stops asking. Never hand it a broad allowlist on anything that deletes,
  overwrites, or ships.
- Hooks. The rtk rewrite hook from [rtk.md](rtk.md) is the one global hook.
  Anything project-shaped, like legendary-taste running `just check` after every
  edit, belongs in that project's `.claude/settings.json`, not up here.
- Defaults. Model and effort level. Set once, override per session when you need to.

## Identity and voice

A standing `CLAUDE.md` keeps the agent sounding like one thing instead of a
different thing every session: who it is, how it writes, how it works with you. It
is standing instruction, not project context. Keep the voice rules short and
concrete. A long style file is one nobody reads twice, and an agent is no
different.

## Memory

File-based memory carries facts between sessions. One fact per file, an index that
loads at the start of each session, links from one fact to the next. What keeps it
from rotting:

- Save only what the repo and git history cannot tell you later. Code structure
  and old fixes are already on disk; a fact about them is dead weight.
- Write dates absolute, never "last Tuesday".
- A remembered file or flag is true as of when it was written. Check it still
  exists before you act on it.
- Fix or delete a fact that went stale. Do not let two versions of it pile up.

## Secrets

Secrets stay out of code, out of chat, out of any committed file. On the machine,
one env file at mode 600 that the agent scripts source, and a plan for rotating it
that exists before you need it. In a project, an untracked `.env.local` loaded
through mise `[env]`. gitleaks, already in the toolchain, is the net that catches
the one you forget.
