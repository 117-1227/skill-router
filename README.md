# Skill Router

`skill-router` is a lightweight coordination skill for selecting the smallest appropriate installed skill and identifying when a task needs Plan Mode.

It does not replace or modify peer skills. The initial registry covers:

- `analytics`
- `frontend-design`
- `ui-ux-pro-max`
- `web-design-guidelines`

## Automatic and Explicit Use

The skill is configured for implicit invocation. A matching task can be routed automatically after the skill is installed.

An explicit request takes precedence when needed:

```text
$analytics
$frontend-design
$ui-ux-pro-max
$web-design-guidelines
```

## GitHub Installation

Publish this directory in a GitHub repository, then install the skill using the GitHub skill installer with the repository path:

```powershell
python <CODEX_HOME>\skills\.system\skill-installer\scripts\install-skill-from-github.py `
  --url https://github.com/<owner>/<repository>/tree/<ref>/<path-to-skill-router>
```

The installer places the skill under `$CODEX_HOME/skills/skill-router`. The four peer skills should remain installed separately.

## Adding Skills

Add a new entry to `references/skill-registry.yaml` with:

- `name`
- `responsibility`
- `use_when`
- `exclude_when`
- optional `follow_up_skills`

Keep detailed procedures in the new skill's own `SKILL.md`; do not copy them into this router.
