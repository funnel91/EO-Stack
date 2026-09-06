# The EO Stack

A fork of [gstack](https://github.com/garrytan/gstack) (Garry Tan, MIT) that we use to build
side projects and the kids' businesses.

Everything upstream does, this does. The fork changes three things — deliberately kept small,
so we can keep pulling upstream fixes without a merge war.

## What's different from upstream

| # | Change | Where | Why |
|---|---|---|---|
| 1 | Skills install as `/eo-*` instead of `/review`, `/qa`, `/ship` | `setup` — `SKILL_NS`, the link-name block | Short names collide. `investigate` already exists as a repo-local skill in the aiosx project; a global `/investigate` would mean two different commands with the same name depending on which folder you're in. `eo-` makes that impossible and says which stack you're in. |
| 2 | Namespaced names are the **default** (no install prompt) | `setup` — `SKILL_PREFIX_FLAG=1` | Upstream defaults to flat names and asks at install time. We always want namespaced, and nobody should have to answer a prompt correctly for the safety property in #1 to hold. |
| 3 | Update + upgrade point at **this fork**, not upstream | `bin/gstack-update-check`, `gstack-upgrade/SKILL.md` | Upstream's auto-update pulls a third party's `main` branch into our tooling, unreviewed, hourly. Pointing at our own fork means upstream changes land only when we choose to sync. |

Legacy `gstack-`-prefixed skill dirs get their prefix swapped rather than stacked, so
`gstack-upgrade` installs as `/eo-upgrade` (not `/eo-gstack-upgrade`).

Internals — `bin/gstack-*`, the `~/.gstack` state dir, the test suite — keep their upstream
names on purpose. Renaming those touches 200+ files and would make every future upstream
merge a conflict. The rename is user-facing only.

## Install

Requires [Bun](https://bun.sh) (`brew install oven-sh/bun/bun`), Git, and Claude Code.

```bash
git clone https://github.com/funnel91/EO-Stack.git ~/.claude/skills/gstack
cd ~/.claude/skills/gstack && ./setup
```

The install directory must stay `gstack` — internal path resolution depends on that name.
What you type is `/eo-office-hours`, `/eo-review`, `/eo-qa`.

### Say no to the optional hooks

`./setup` offers to register hooks in the **global** `~/.claude/settings.json`
(AskUserQuestion capture, a preference PreToolUse hook, a timeline Stop hook). Those fire in
*every* Claude Code session — including the aiosx and knowledge-base projects, which run their
own hooks. Decline them. None of the skills need them.

Already installed and want them gone:

```bash
~/.claude/skills/gstack/bin/gstack-settings-hook remove-source --source gstack-timeline-stop
```

### Don't put the browser rule in a global CLAUDE.md

Upstream's install blurb tells you to write "never use `mcp__claude-in-chrome__*`" into
CLAUDE.md so its own browse daemon wins. In `~/.claude/CLAUDE.md` that would break Chrome MCP
for the health, CFO, tutor, and chief-of-staff agents. Put it in the individual project repos
or nowhere.

## The eight that matter

There are 55 skills. For a side project or a kid's business, this is the whole loop:

| Command | What it's for |
|---|---|
| `/eo-office-hours` | Talk through the idea. Pushes back on the framing before any code exists. |
| `/eo-spec` | Turn a vague idea into something precise enough to build. |
| `/eo-autoplan` | Runs the CEO / design / eng / DX reviews over the plan back to back. |
| `/eo-review` | Reviews a branch for real bugs before it lands. |
| `/eo-qa` | Opens a real browser against a URL and finds what's broken. |
| `/eo-design-review` | Catches AI-slop visual design — spacing, hierarchy, inconsistency. |
| `/eo-ship` | Tests, changelog, commit, push, PR. |
| `/eo-careful` | Guardrails on destructive commands. Worth it with kids driving. |

The iOS skills (`/eo-ios-*`), `/eo-canary`, and `/eo-land-and-deploy` are dead weight until
there's an iOS app or a real deploy pipeline.

## Pulling upstream fixes

This fork has commits of its own, so `gh repo sync` will report a diverged branch. Merge it:

```bash
git remote add upstream https://github.com/garrytan/gstack.git   # once
git fetch upstream && git merge upstream/main
```

Expect conflicts only in the files listed in the table above. Resolve by keeping our side,
then re-run `./setup`.

---

Upstream is MIT-licensed; see [LICENSE](LICENSE). Credit to Garry Tan — this is his work with
three small changes.
