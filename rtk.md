# rtk, the token proxy

rtk (Rust Token Killer) trims and compacts command output before it lands in the
model's context. Routine dev commands cost 60 to 90 percent fewer tokens, and the
prompt overhead is zero because a hook does the rewriting where you never see it.
rtk lives at the harness layer and nowhere else. No project depends on it, and the
published project scaffold must never assume it is there.

## Install

Install the `rtk` binary per its own instructions, then check it:

```bash
rtk --version     # rtk X.Y.Z
rtk gain          # works, not "command not found"
which rtk
```

There is another `rtk` in the world (Rust Type Kit). If `rtk gain` errors, that is
the one on your PATH. Sort that out before going further.

## The rewrite hook

A Claude Code `PreToolUse:Bash` hook rewrites commands to their rtk forms as they
go by. Drop [hooks/rtk-rewrite.sh](hooks/rtk-rewrite.sh) at
`~/.claude/hooks/rtk-rewrite.sh`, `chmod +x` it, and wire it in
`~/.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      { "matcher": "Bash",
        "hooks": [ { "type": "command", "command": "~/.claude/hooks/rtk-rewrite.sh" } ] }
    ]
  }
}
```

`git status` becomes `rtk git status`, `cargo build` becomes `rtk cargo build`, and
so on down the line. If rtk or jq is missing the hook does nothing, so a half-set-up
machine still runs.

## The oracle-verbatim rule (the load-bearing part)

A thing that compacts output must never compact away the truth. The worst failure
in the build loop is output that drops a diagnostic, or quietly swallows a nonzero
exit code. The agent reads a green check, declares victory, and the loop is now
lying to itself. Everything else here is convenience. This part is not.

So oracles run verbatim. The hook sends every check and test tool through `rtk
proxy`, which runs the command untouched but still counts it. That covers `cargo
check/clippy/test/nextest`, `tsc`, `ruff check`, `pytest`, `mypy`, `pyright`,
`biome`, `eslint`, `playwright`, `vitest`, `go test/vet`, `golangci-lint`, and the
`pnpm exec`, `npx`, and `python -m` spellings of each. Full output, still on the
books. Everything that is not an oracle keeps the lossy compaction, which is fine
for it.

Two reasons this holds up:

- The real oracle path is `just check` and `just test`. The hook only looks at the
  first word, `just` is not a target, so those run raw no matter what.
- For the times the agent types `cargo check` by hand mid-debug, `rtk proxy`
  guarantees verbatim output even though the per-tool compactors turned out
  lossless on signal when tested. Belt and suspenders, and it survives whatever a
  future rtk release does to its compactors.

Tested on cargo: exit code preserved on pass and on fail, every diagnostic kept,
only the progress chatter stripped, and the full output teed to a log besides.

## Config

rtk reads `~/.config/rtk/config.toml`. The defaults are fine; `rtk config` prints
them. The usage it tracks feeds `rtk gain` and `rtk cc-economics`, which put
savings next to spend so you can see whether any of this is worth it.
