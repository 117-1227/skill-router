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

The router is configured for implicit invocation of tasks involving registered professional peer skills. Those peers are explicit-only; the router reads the selected peer's `SKILL.md` directly rather than relying on host auto-invocation.

Host-provided system, plugin, and tool skills follow host requirements and their native workflows, including mandatory loading order. Do not change their metadata to enforce this registry's policy.

An explicit skill choice takes precedence without expanding authorization or bypassing required planning and verification:

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

The installer places the skill under `$CODEX_HOME/skills/skill-router`. If `CODEX_HOME` is unset or empty, use `~/.codex` as the Codex home in the example above.

### Claude Code

Place or install the directory so that the skill entry point is available at one of these locations:

```text
<project>/.claude/skills/skill-router/SKILL.md
~/.claude/skills/skill-router/SKILL.md
```

Claude Code uses the shared `SKILL.md` and `references/` files. The optional `agents/openai.yaml` file is Codex-specific metadata and can be ignored by Claude Code.

The registered peer skills should remain installed separately for each host. The router does not copy or replace them.

## Skill Paths

Resolve a selected skill for the active host and project:

1. Use the applicable absolute skill path explicitly supplied by the host.
2. Otherwise use the confirmed installation directory for the active host.
3. For Codex, use `$CODEX_HOME/skills/<name>/SKILL.md` when `CODEX_HOME` is non-empty; otherwise use `~/.codex/skills/<name>/SKILL.md`.
4. For Claude Code, check `<project>/.claude/skills/<name>/SKILL.md`, then `~/.claude/skills/<name>/SKILL.md`.

Prefer the applicable active-host/project copy; do not silently fall back to another host. For system and plugin skills, use the path supplied by the host. Do not scan historical plugin caches, migrate, or copy skills.

If the required skill or entrypoint is missing, follow Missing Peer Skills. Do not download, install, replace, or claim to have used a missing skill.

## Code Review, Diagnosis, and Security

- `code-review` reviews a branch, pull request, commit range, or work-in-progress diff from a fixed Git comparison point. It evaluates repository standards and specification compliance separately.
- `diagnosing-bugs` investigates concrete failures, exceptions, flaky behavior, incorrect output, and performance regressions by first building a reproducible feedback loop.
- `security-best-practices` is explicit-only and applies when the user requests security guidance, a security review or report, or secure-by-default implementation for supported Python, JavaScript/TypeScript, or Go projects.

These skills do not replace one another: use `code-review` for reviewing changes, `diagnosing-bugs` for a known symptom, and `security-best-practices` for an explicit security request.

## Strict Routing Policy

The router is the only automatic entry point for registered professional peer skills. This restriction does not apply to host-provided system, plugin, or tool skills. For every registered peer skill:

- Codex should set `policy.allow_implicit_invocation: false` in `agents/openai.yaml`.
- Claude Code should hide peer skills from model invocation using its `skillOverrides` settings or the `/skills` configuration UI; do not add Claude-only frontmatter to the shared `SKILL.md`.
- `references/skill-registry.yaml` should include `invocation: explicit-only`.

When adding a skill, register it first, apply the host's explicit-only policy where supported, and test that the router can read its `SKILL.md`.

## Planning Gate

Read-only inspection, audits, and plan discussion may proceed without entering Plan Mode. They do not authorize file changes or external writes. Apply the remaining gate only to implementation work.

When a plan is already approved and the current session mode permits execution, continue within its approved scope without restarting approval. If the scope, risk, or key approach changes materially, explain the change and obtain confirmation again.

For implementation that does not yet have an approved plan, determine whether the task is simple or complex before selecting a peer workflow.

A task is complex when it involves multiple files, architecture, refactoring,
migration, deployment, production systems, databases, permissions, external
services, broad user-facing workflows, multiple skills, ambiguous requirements,
or creating or modifying skills, plugins, or automation workflows.

A task is simple only when it is localized, low-risk, reversible, clearly
defined, and does not involve any of the conditions above.

If implementation requires a plan and the host is not already in planning mode, stop and ask the user to enable the host's planning mode. In Codex, this is Plan Mode.

In planning mode, inspect the environment without mutation, resolve discoverable facts, ask only material questions, produce a decision-complete plan, and wait for confirmation. For tasks that require a plan, do not modify files or perform external writes before confirmation.

Small, clearly defined tasks may skip this gate when explicitly authorized. Host system and developer instructions, permissions, project instructions, and the current session mode remain binding.

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
