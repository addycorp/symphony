---
tracker:
  kind: linear
  project_slug: "10255b4875af"
  active_states:
    - Todo
    - In Progress
    - Merging
    - Rework
  terminal_states:
    - Closed
    - Cancelled
    - Canceled
    - Duplicate
    - Done
polling:
  interval_ms: 5000
workspace:
  root: ~/dev/symphonyws
hooks:
  after_create: |
    git clone https://github.com/addycorp/ak.git .
    mkdir -p .codex/skills
    cp -R /Users/advithgopinath/dev/PERSONAL/symphony/.codex/skills/linear-cli .codex/skills/
  before_remove: |
    true
agent:
  max_concurrent_agents: 1
  max_turns: 12
codex:
  command: codex --config shell_environment_policy.inherit=all --config 'model="gpt-5.5"' --config model_reasoning_effort=xhigh app-server
  approval_policy: never
  thread_sandbox: workspace-write
  turn_sandbox_policy:
    type: workspaceWrite
    networkAccess: true
---

You are working on a Linear ticket `{{ issue.identifier }}` for the `addycorp/ak` repository.

{% if attempt %}
Continuation context:

- This is retry attempt #{{ attempt }} because the ticket is still in an active state.
- Resume from the current workspace state instead of restarting from scratch.
- Do not repeat already-completed investigation or validation unless needed for new code changes.
- Do not end the turn while the issue remains in an active state unless you are blocked by missing required permissions/secrets.
{% endif %}

Issue context:
Identifier: {{ issue.identifier }}
Title: {{ issue.title }}
Current status: {{ issue.state }}
Labels: {{ issue.labels }}
URL: {{ issue.url }}

Description:
{% if issue.description %}
{{ issue.description }}
{% else %}
No description provided.
{% endif %}

## Objective

Fix the issue end-to-end in the current `addycorp/ak` workspace, validate it, push a branch, open a pull request, link the PR to Linear, and move the issue to `Human Review`.

## Required Tools

- Use the repo-local `linear-cli` skill and the external `linear` command for Linear reads, comments, and state transitions.
- Do not use raw Linear GraphQL tooling.
- Use normal git and `gh` CLI commands for branch, commit, push, and PR work.

## Operating Rules

- Work only in the current workspace.
- Never print or expose secret values.
- Start by reading the Linear issue with `linear issue view {{ issue.identifier }} --json`.
- If the issue is `Todo`, move it to `In Progress` before implementation.
- Create exactly one `## Codex Workpad` Linear comment if none exists, and update that same comment throughout the run.
- Keep the workpad concise and current: plan, acceptance criteria, validation, notes, and final handoff.
- Reproduce the bug before changing code, using either the real CLI behavior described in the issue or a deterministic fake-store test.
- Prefer focused tests around `internal/cli` and `internal/envmap`; do not refactor unrelated code.
- Do not modify user secrets or delete real Keychain entries.
- Do not include `.DS_Store`, build artifacts, coverage output, or local cache files in commits.

## Implementation Flow

1. Read the issue and existing comments.
2. Move `Todo` issues to `In Progress`.
3. Create or update the `## Codex Workpad` comment with:
   - environment stamp: `<host>:<abs-workdir>@<short-sha>`
   - implementation plan
   - acceptance criteria copied from the issue
   - validation checklist
4. Create a branch from latest `origin/main` using the Linear branch name when available.
5. Reproduce the `ak list` bug.
6. Implement the smallest fix that hides internal bookkeeping accounts from `ak list` while preserving imported dotenv variable behavior.
7. Run `gofmt` on changed Go files.
8. Run `go test ./...`.
9. Run a local CLI proof that demonstrates `ak list` no longer shows internal metadata rows.
10. Commit the fix with a clear message.
11. Push the branch to `origin`.
12. Open a GitHub PR against `main`; include the reproduction, fix summary, and validation evidence.
13. Attach or link the PR in Linear using `linear issue link`.
14. Update the workpad with final checklist status and validation evidence.
15. Move the issue to `Human Review`.

## Completion Bar

- Bug reproduced or covered by a failing-before/fixed-after regression test.
- Code fix is committed.
- `go test ./...` passes.
- Local CLI proof is recorded in the workpad.
- PR is open and linked to Linear.
- Issue is moved to `Human Review`.

## Blockers

Only stop early for missing required auth, missing required command-line tools, or an unavailable remote service. If blocked, update the workpad with the exact missing item and leave the issue in a review/blocker state rather than pretending the work is complete.
