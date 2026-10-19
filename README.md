# Skill Router

`skill-router` is a lightweight coordination skill for selecting the smallest appropriate installed skill and identifying when a task needs Plan Mode.

It does not replace or modify peer skills. The initial registry covers:

- `analytics`
- `frontend-design`
- `ui-ux-pro-max`
- `web-design-guidelines`
- `design-system`
- `ui-styling`

## Automatic and Explicit Use

The skill is configured for implicit invocation. A matching task can be routed automatically after the skill is installed.

Peer skills are explicit-only. The router reads the selected peer skill's `SKILL.md` directly, so a peer skill does not need to be automatically invoked by the host.

An explicit request takes precedence when needed:

```text
$analytics
$frontend-design
$ui-ux-pro-max
$web-design-guidelines
```

## Installation

Publish this directory in a GitHub repository, then install the skill using the platform's skill mechanism.

### Codex

Use the GitHub skill installer with the repository path:

```powershell
python <CODEX_HOME>\skills\.system\skill-installer\scripts\install-skill-from-github.py `
  --url https://github.com/<owner>/<repository>/tree/<ref>/<path-to-skill-router>
```

The installer places the skill under `$CODEX_HOME/skills/skill-router`.

### Claude Code

Place or install the directory so that the skill entry point is available at one of these locations:

```text
<project>/.claude/skills/skill-router/SKILL.md
~/.claude/skills/skill-router/SKILL.md
```

Claude Code uses the shared `SKILL.md` and `references/` files. The optional `agents/openai.yaml` file is Codex-specific metadata and can be ignored by Claude Code.

The six peer skills should remain installed separately for each host. The router does not copy or replace them.

## Strict Routing Policy

The router is the only automatic entry point. For every registered peer skill:

- Codex should set `policy.allow_implicit_invocation: false` in `agents/openai.yaml`.
- Claude Code should hide peer skills from model invocation using its `skillOverrides` settings or the `/skills` configuration UI; do not add Claude-only frontmatter to the shared `SKILL.md`.
- `references/skill-registry.yaml` should include `invocation: explicit-only`.

When adding a skill, register it first, apply the host's explicit-only policy where supported, and test that the router can read its `SKILL.md`.

## Adding Skills

Add a new entry to `references/skill-registry.yaml` with:

- `name`
- `responsibility`
- `use_when`
- `exclude_when`
- optional `follow_up_skills`

Keep detailed procedures in the new skill's own `SKILL.md`; do not copy them into this router.
