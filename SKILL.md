---
name: skill-router
description: Route tasks to the smallest appropriate installed skill, assess whether Plan Mode is required, and coordinate analytics, UI/UX, visual design, and Web review workflows. Use when a request may match an installed skill or may require a complex multi-step change.
metadata:
  version: "1.0.0"
---

# Skill Router

Use this skill as the entry point for tasks that may match one or more installed skills. Select the smallest useful set of peer skills, keep their responsibilities separate, and avoid loading unrelated instructions.

## Explicit User Choice

If the user names a skill or invokes `$skill-name`, treat that choice as the preferred route. Still apply the Planning Gate and do not load unrelated skills.

## Planning Gate

For complex tasks, first inspect the environment and determine whether Plan Mode is required.

Complex tasks include multiple-file changes, architecture, refactoring, migration, deployment, production systems, databases, permissions, or broad user-facing workflows.

In Plan Mode:

- Explore the environment without mutating files;
- Resolve discoverable facts before asking questions;
- Ask only for decisions that materially affect the implementation;
- Produce a decision-complete plan;
- Wait for plan confirmation before modifying files.

Small, clearly defined tasks may skip Plan Mode.

This workflow supplements the minimal global rule in `AGENTS.md` and does not override system instructions, user requests, project instructions, or session mode.

## Routing Rules

Read [skill-registry.yaml](references/skill-registry.yaml) when the request may match an installed peer skill.

- Use `analytics` for tracking, measurement, attribution, conversion events, GA4, GTM, UTM, Mixpanel, Segment, or questions about whether marketing or product activity is working.
- Use `ui-ux-pro-max` for product structure, interaction, responsive behavior, accessibility, component systems, dashboards, or stack-specific UI implementation.
- Use `frontend-design` for new pages, landing pages, brand sites, visual direction, typography, color, composition, or distinctive presentation.
- Use `web-design-guidelines` for existing Web UI reviews, accessibility audits, UX reviews, best-practice checks, or pre-release Web validation.

## Combination Rules

Select only the minimum useful set.

- Analytics plus interface implementation: use `analytics` first for measurement requirements, then the applicable UI skill.
- Product UI plus visual design: use `ui-ux-pro-max` first for structure and usability, then `frontend-design` for visual direction.
- Existing Web review: use `web-design-guidelines`; add `ui-ux-pro-max` only when the findings require broader interaction or accessibility analysis.
- Full design-to-review workflow: use `ui-ux-pro-max`, then `frontend-design`, then `web-design-guidelines`.
- Backend, database-only, infrastructure, or non-visual scripting tasks: do not force any of the four peer skills.

If recommendations conflict, preserve accessibility, responsive behavior, interaction usability, and explicit user requirements before visual novelty.

## Missing Peer Skills

The router does not copy, modify, install, or replace peer skills. If a selected peer skill is unavailable, report the missing dependency and continue with the remaining applicable workflow.

## Extending the Registry

Add future skills to `references/skill-registry.yaml` with a name, use cases, exclusions, responsibility, and optional follow-up skills. Keep detailed procedures in the peer skill's own `SKILL.md`.
