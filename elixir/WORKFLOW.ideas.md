---
tracker:
  kind: linear
  project_slug: "0c74b1d6ea00"
  active_states:
    - Todo
    - In Progress
  terminal_states:
    - Done
    - Canceled
    - Cancelled
    - Duplicate
polling:
  interval_ms: 10000
workspace:
  root: ~/dev/symphonyws
hooks:
  after_create: |
    mkdir -p artifacts notes
    cp /Users/advithgopinath/dev/Vault/IDEAS.md ./IDEAS.md 2>/dev/null || true
agent:
  max_concurrent_agents: 3
  max_turns: 8
codex:
  command: codex --config shell_environment_policy.inherit=all --config 'model="gpt-5.5"' --config model_reasoning_effort=high app-server
  approval_policy: never
  thread_sandbox: workspace-write
  turn_timeout_ms: 1800000
  stall_timeout_ms: 600000
---

You are working on a Linear idea-lab ticket `{{ issue.identifier }}`.

## Table of Contents

- [Objective](#objective)
- [Operating Rules](#operating-rules)
- [Required Artifact](#required-artifact)
- [Linear Updates](#linear-updates)
- [Completion Criteria](#completion-criteria)

## Objective

Turn the ticket's incubating idea into a concrete, reviewable scoping artifact.

Issue context:

- Identifier: {{ issue.identifier }}
- Title: {{ issue.title }}
- Current status: {{ issue.state }}
- URL: {{ issue.url }}

Description:

{% if issue.description %}
{{ issue.description }}
{% else %}
No description provided.
{% endif %}

## Operating Rules

- Work only inside the current Symphony workspace.
- Do not edit files outside the workspace.
- Use online/current documentation when evaluating APIs, SDKs, libraries, browser behavior, platform rules, or vendor capabilities.
- Keep the result focused on what can be built or tested next.
- For risky security or account-automation ideas, stay in safe local analysis and do not perform live abuse, signup automation, bypass work, or unsolicited scanning.
- Prefer markdown tables for structured comparisons, inventories, and rankings.
- If the output has multiple sections, include a table of contents.

## Required Artifact

Create `artifacts/{{ issue.identifier }}-scope.md`.

The artifact must include:

- Table of contents
- Problem framing
- User/workflow assumptions
- Source/API/auth requirements
- Implementation options
- Risks and constraints
- First prototype recommendation
- Validation plan
- Open questions

## Linear Updates

Use the repo-local `linear-cli` skill to keep Linear updated when the `linear` command is
available. Use `linear_graphql` as the fallback for Symphony session auth or exact custom GraphQL:

- Read the issue before starting substantive work.
- Add exactly one concise progress/handoff comment when the artifact is complete.
- Move the issue to a completed terminal state only after the artifact exists and the handoff comment is posted.

## Completion Criteria

Stop only after:

1. `artifacts/{{ issue.identifier }}-scope.md` exists in the workspace.
2. The artifact is specific to this ticket, not a generic template.
3. A Linear handoff comment states the artifact path and summarizes the recommended first prototype.
4. The Linear issue is moved to a completed terminal state.
