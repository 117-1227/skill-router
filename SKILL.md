---
name: skill-router
description: Route tasks to the smallest appropriate installed skill, assess whether Plan Mode is required, and coordinate code review, debugging, security, analytics, UI/UX, visual design, and Web review workflows. Use when a request may match an installed skill or may require a complex multi-step change.
metadata:
  version: "1.0.0"
---

# Skill Router

Use this skill as the entry point for tasks that may match one or more installed skills. Select the smallest useful set of peer skills, keep their responsibilities separate, and avoid loading unrelated instructions.

## Explicit User Choice

If the user names a skill or invokes `$skill-name`, treat that choice as the preferred route. Still apply the Planning Gate and do not load unrelated skills.

## Planning Gate

Determine whether the task is simple or complex before selecting a peer workflow.

A task is complex when it involves multiple files, architecture, refactoring,
migration, deployment, production systems, databases, permissions, external
services, broad user-facing workflows, multiple skills, ambiguous requirements,
or creating or modifying skills, plugins, or automation workflows.

A task is simple only when it is localized, low-risk, reversible, clearly
defined, and does not involve any of the conditions above.

If the task is complex and the host is not already in planning mode, stop and ask
the user to enable the host's planning mode. In Codex, this is Plan Mode.

After planning mode is enabled:

- Explore the environment without mutating files;
- Resolve discoverable facts before asking questions;
- Ask only for decisions that materially affect the implementation;
- Produce a decision-complete plan;
- Wait for the user's confirmation.

Do not modify files or perform external writes before the plan is confirmed.

Small, clearly defined tasks may skip this planning gate.

This workflow supplements the host's project instructions and does not override system instructions, user requests, project instructions, or session mode.

## Routing Rules

Read [skill-registry.yaml](references/skill-registry.yaml) when the request may match an installed peer skill.

The router is the automatic entry point. Peer skills are explicit-only by default. After selecting a peer skill, locate and read that peer's `SKILL.md` from the supported paths below and use its workflow. Do not rely on platform-level implicit invocation, and do not load every peer skill.

- Use `code-review` to review changes since a fixed commit, branch, tag, or merge-base, including branches, pull requests, and work-in-progress diffs. It evaluates repository standards and specification compliance separately.
- Use `diagnosing-bugs` for reproducible failures, exceptions, incorrect behavior, flaky bugs, and performance regressions that require investigation.
- Use `security-best-practices` only when the user explicitly requests security guidance, a security review or report, or secure-by-default implementation for supported Python, JavaScript/TypeScript, or Go projects.
- Use `analytics` for tracking, measurement, attribution, conversion events, GA4, GTM, UTM, Mixpanel, Segment, or questions about whether marketing or product activity is working.
- Use `ui-ux-pro-max` for product structure, interaction, responsive behavior, accessibility, component systems, dashboards, or stack-specific UI implementation.
- Use `frontend-design` for new pages, landing pages, brand sites, visual direction, typography, color, composition, or distinctive presentation.
- Use `web-design-guidelines` for existing Web UI reviews, accessibility audits, UX reviews, best-practice checks, or pre-release Web validation.
- Use `design-system` for design tokens, CSS variables, typography scales, spacing systems, component specifications, and design system migration.
- Use `ui-styling` for Tailwind, shadcn/ui, Radix UI, accessible components, themes, dark mode, and frontend styling implementation.

## Peer Skill Paths

Resolve a selected peer skill by checking these locations in order:

1. `$CODEX_HOME/skills/<name>/SKILL.md`
2. `<project>/.claude/skills/<name>/SKILL.md`
3. `~/.claude/skills/<name>/SKILL.md`

If more than one copy exists, use the copy belonging to the active host and project. If no copy exists, report the missing peer skill and stop that workflow.

## Combination Rules

Select only the minimum useful set.

- General branch, pull request, or diff review uses `code-review`; do not add `diagnosing-bugs` unless the user reports a concrete failure or regression.
- Known broken, failing, incorrect, flaky, or slow behavior uses `diagnosing-bugs`; do not substitute a general code review for diagnosis.
- General code review and debugging do not automatically invoke `security-best-practices`. Security routing requires an explicit security request.
- If the user explicitly requests both a general code review and a security review, run `code-review` and `security-best-practices` as separate workflows and report their findings separately.
- Analytics plus interface implementation: use `analytics` first for measurement requirements, then the applicable UI skill.
- Product UI plus visual design: use `ui-ux-pro-max` first for structure and usability, then `frontend-design` for visual direction.
- Design system workflow: use `design-system` first, then `ui-styling` for implementation.
- Product UI implementation with a design system: use `ui-ux-pro-max`, then `design-system`, then `ui-styling`.
- Existing Web review: use `web-design-guidelines`; add `ui-ux-pro-max` only when the findings require broader interaction or accessibility analysis.
- Full design-to-review workflow: use `ui-ux-pro-max`, then `frontend-design`, then `web-design-guidelines`.
- Backend, database-only, infrastructure, or non-visual scripting tasks: do not force UI or Web-design skills, but still route explicit review, debugging, or security requests to their matching skill.

If recommendations conflict, preserve accessibility, responsive behavior, interaction usability, and explicit user requirements before visual novelty.

## Missing Peer Skills

The router does not copy, modify, install, or replace peer skills. If a selected peer skill is unavailable:

1. Report the peer skill name and every path checked.
2. Explain what part of the task depends on the missing skill.
3. Separate affected work from independent work.
4. Propose a fallback plan using available skills or general guidance.
5. State which references, scripts, platform capabilities, or validation guarantees are unavailable.
6. Stop the affected workflow and wait for the user's confirmation before executing the fallback plan.

After confirmation, follow the updated plan and label the result as a fallback. Never claim that the missing peer skill was used, and never silently substitute a similar skill.

## Extending the Registry

Add future skills to `references/skill-registry.yaml` with a name, use cases, exclusions, responsibility, `invocation: explicit-only`, and optional follow-up skills. Also add the platform metadata that disables implicit invocation before testing the new route. Keep detailed procedures in the peer skill's own `SKILL.md`.
