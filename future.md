# What might change

No harness keeps the crown forever. Claude Code is the best one going right now,
and all of this is built on it. The hedge is that the project layer,
legendary-taste, knows nothing about the harness, so the day a better one shows up
the projects come along untouched. This file is the watch list: what would move
us, and why it has not yet.

## One provider, for now

Every project talks to the same provider today. There are honest reasons to spread
the work around. Cost. A model that happens to be better at a given language. A
project whose data is not allowed to leave a jurisdiction. Plain unwillingness to
bet the whole operation on one vendor's pricing and uptime.

Part of the machinery is already running. Meridian is a local Claude API proxy,
and every autonomous agent here points at it through `ANTHROPIC_BASE_URL`. A proxy
is the right place to put routing: swap a backend, or fan out to several, and the
agents never know. Generalizing it means one proxy that picks a provider per
project, or per task, from one config you keep in one place.

## Other harnesses

dirge (https://github.com/dirge-code/dirge) is on the watch list as a harness
that is provider-agnostic from the start. The day per-project provider choice
turns into a real requirement instead of a someday, a harness built for many
backends beats welding routing onto one that was built for a single vendor. Judge
it on two questions. Does it keep what makes Claude Code good (hooks, permissions,
skills, file memory, the edit-check loop). Does the project layer survive the move
without edits. If both answers hold, the switch is cheap.

## When to actually move

Not on enthusiasm. When one of these is true:

- A project needs a non-Claude model and routing it through the proxy is more
  hassle than changing harness.
- The harness blocks something the loop needs, a hook point or a tool contract,
  that another one offers.
- Single-vendor risk stops being theoretical: pricing, availability, or terms turn
  against work that matters.

Until one of those lands, the old rule holds. Battle-tested beats new. The proxy
seam and the harness-blind project layer are what keep a future move from hurting.

## On the bench

- A per-project provider router, which is Meridian grown up.
- A real one-shot bootstrap: install mise, link in the `~/.claude` config, install
  rtk, done. This repo is step one toward that. Right now it is still a guide you
  follow by hand.
