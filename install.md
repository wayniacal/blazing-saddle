# Machine-level toolchain

One installer, mise. It pins versions in a file you can carry to the next box, it
reads the asdf plugin ecosystem, it owns per-directory env vars so you can retire
direnv, and its aqua backend pulls release binaries with the checksums verified.
Resist the urge to grab one tool from brew, one from cargo-binstall, one from npm.
Down that road every machine is a little different and you find out which one when
something breaks at 2am. One installer, one pin file.

## 1. Install mise

```bash
curl https://mise.run | sh
# add the shims to your shell; mise prints the exact line:
#   eval "$(~/.local/bin/mise activate bash)"   # or zsh/fish
```

On a box with no mise and no shell to paste into (CI, a container), `mise generate
bootstrap` writes a self-contained script that fetches a pinned mise for you. That
gets mise onto the machine. Installing your tools is still the next step.

## 2. Install the toolchain

Copy [mise.toml.example](mise.toml.example) to `~/.config/mise/config.toml`, then:

```bash
mise install
```

Adding one tool at a time writes the pin into the global config for you:

```bash
mise use -g <tool>@<version>
```

## The set

Every entry below is a global binary projects assume is already there. The
argument for the language-lane oracles (cargo, tsc, ruff, the rest) lives in
legendary-taste's `tools-meta.md`. This file is the packing list, not the debate.

### Always

| Tool | Role |
|---|---|
| mise | toolchain pins, per-directory env |
| jj (jujutsu) | version control, colocated with git |
| just | command runner (`check`/`fix`/`test`/`run`/`audit`/`ship`/`save`) |
| gh | GitHub from the CLI: PRs, issues, CI runs, `--json` everywhere; one of the highest-value tools an agent touches. Easy to forget because apt ships one, but apt's is years stale; it belongs in the pin file like everything else |
| ripgrep (`rg`) | search, the agent's eyes |
| fd | file find, the agent's other eye |
| shellcheck | shell oracle |
| osv-scanner | supply-chain oracle (`just audit`), one binary across every lockfile |

### Oracles worth having machine-wide

Each one beat the alternatives for a reason; the reason is in the note.

| Tool | Role | Note |
|---|---|---|
| ast-grep | structural search, plus lint rules you write yourself | Rust, tree-sitter. Comby matches text, not syntax; GritQL is younger. Semgrep is the security-rule specialist, but gitleaks and osv-scanner already cover that ground. |
| gitleaks | catches secrets before they ship (`just audit` / CI) | Go, fast, offline. trufflehog actually phones the provider to confirm a key is live, fewer false alarms, but it needs the network and the time. gitleaks is the right default. |
| actionlint | lints GitHub Actions workflows | Go. Nothing else is close. Only earns its keep where you ship workflows. |
| typos (optional) | spell-checks source | Rust, knows camelCase. codespell has the longer track record and a bigger dictionary, at the cost of being Python. Lowest priority of the set. |

### Per language (install when a lane needs it)

Rust (`rust` via rustup or mise, `cargo-nextest`), Node (`node`, `pnpm`), Python
(`uv`, `ruff`, `pyright`). These get pinned in the project's own `.mise.toml`, not
here. They drift per project, and the global config has no business carrying that
drift. Keep it to what every project shares.

### Tools for you, not the agent

difftastic, fish, helix, zoxide, bat, eza: the things that make a terminal
pleasant to sit in. They serve you, not the build loop, so they do not belong in
the copyable set. mise reads every file in `~/.config/mise/conf.d/`, so drop them
in `~/.config/mise/conf.d/personal.toml` and they install on the same `mise
install` without touching `config.toml`. Someone copying your harness gets the
oracles and not your shell habits.

## Pinning rule

No floating `latest` in the committed config. `mise use -g x@latest` resolves the
version once; then edit `config.toml` to the exact version it wrote, and bump when
you mean to. Leave it floating and it re-resolves on its own schedule. The day it
drifts is the day a check fails on code you never touched, and you spend an hour
proving your innocence. legendary-taste guards the same wound with lockfiles.
