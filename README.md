# Skill Router

`skill-router` is a lightweight coordination skill for selecting the smallest appropriate installed skill and identifying when a task needs Plan Mode.

中文文档：[README.zh-CN.md](README.zh-CN.md)

It does not replace or modify peer skills. The initial registry covers:

- `code-review`
- `diagnosing-bugs`
- `security-best-practices`
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
$code-review
$diagnosing-bugs
$security-best-practices
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

The registered peer skills should remain installed separately for each host. The router does not copy or replace them.

## Code Review, Diagnosis, and Security

- `code-review` reviews a branch, pull request, commit range, or work-in-progress diff from a fixed Git comparison point. It evaluates repository standards and specification compliance separately.
- `diagnosing-bugs` investigates concrete failures, exceptions, flaky behavior, incorrect output, and performance regressions by first building a reproducible feedback loop.
- `security-best-practices` is explicit-only and applies when the user requests security guidance, a security review or report, or secure-by-default implementation for supported Python, JavaScript/TypeScript, or Go projects.

These skills do not replace one another: use `code-review` for reviewing changes, `diagnosing-bugs` for a known symptom, and `security-best-practices` for an explicit security request.

## Strict Routing Policy

The router is the only automatic entry point. For every registered peer skill:

- Codex should set `policy.allow_implicit_invocation: false` in `agents/openai.yaml`.
- Claude Code should hide peer skills from model invocation using its `skillOverrides` settings or the `/skills` configuration UI; do not add Claude-only frontmatter to the shared `SKILL.md`.
- `references/skill-registry.yaml` should include `invocation: explicit-only`.

When adding a skill, register it first, apply the host's explicit-only policy where supported, and test that the router can read its `SKILL.md`.

## Missing Peer Skills

When a registered peer skill is missing, the router pauses the affected workflow. It reports the missing name and checked paths, explains the impact, proposes an updated fallback plan, and waits for confirmation before continuing.

The fallback must state what is unavailable. For example:

```text
Original:
skill-router -> design-system -> ui-styling

Missing:
design-system is unavailable.

Updated fallback:
skill-router -> user confirmation
-> general three-layer token guidance
-> ui-styling
-> reduced validation scope and an explicit fallback note
```

Independent work may continue only after it is included in the confirmed fallback plan. A fallback must never be reported as if the missing skill had been used.

## Adding Skills

Add a new entry to `references/skill-registry.yaml` with:

- `name`
- `responsibility`
- `use_when`
- `exclude_when`
- `invocation: explicit-only`
- optional `follow_up_skills`

Keep detailed procedures in the new skill's own `SKILL.md`; do not copy them into this router.
