# blazing-saddle

Getting a fresh box, or a fresh agent, set up to build without tripping over its
own feet. This is the layer under a project. You install it once per machine.
Projects sit on top of it.

## Two layers

A machine has one harness and many projects. Sort every tool by which of the two
it serves and you stop wondering where things go.

| Layer | Repo | Holds | Touched |
|---|---|---|---|
| Harness | blazing-saddle (here) | the agent harness, the token proxy, the tools every project takes for granted | once per machine |
| Project | [legendary-taste](https://github.com/wayniacal/legendary-taste) | per-repo scaffold: oracles wired to verbs, lanes, CI | once per project |

When a fact could live in either, the cut is clean. This repo says what to
install. legendary-taste says why, and how it gets wired. State it once, in one
place, and the two repos never drift against each other.

## Bootstrap order

1. Toolchain. Install mise, then the tool list. See [install.md](install.md). The
   pinned set is [mise.toml.example](mise.toml.example); it copies to
   `~/.config/mise/config.toml`.
2. Token proxy. Install rtk and its rewrite hook. See [rtk.md](rtk.md). rtk is the
   only tool that lives nowhere but here.
3. Harness config. Settings, permissions, hooks, identity, memory. See
   [harness.md](harness.md).
4. Then projects. From here on a new project starts from legendary-taste, not from
   this repo.

## The one idea

Same one that runs legendary-taste: agent leverage is oracle quality times
iteration speed. The tools give you the oracles. The proxy keeps the loop cheap
enough to spin fast. The config keeps the agent from surprising you. A tool that
does none of those three does not earn a line in this config, however fond of it
you are on your own machine. install.md says where the fond-of-it tools go.

## Settled, and not

Settled: mise, rtk with the oracle-verbatim rule, the toolchain, Claude Code for
the harness.

Not settled: the harness. It is the best one going right now. It will not hold
that title forever, so the project layer stays harness-agnostic on purpose and a
swap stays cheap. [future.md](future.md) has the multi-provider story (dirge, the
Meridian proxy) and the things that would actually make us move.
