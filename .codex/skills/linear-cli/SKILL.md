---
name: linear-cli
description: |
  Use the `linear` command-line client for agent-friendly Linear issue,
  comment, project, milestone, document, and Linear workflow operations.
  Use this skill for all Linear work in Symphony workflows.
---

# Linear CLI

Use `linear-cli` for common Linear operations from the shell. It is more
agent-friendly than hand-written API calls for issue reads, comments, state
updates, projects, milestones, labels, and documents.

## Availability

Check the command first:

```bash
linear --version
```

If `linear` is not installed but package execution is available, use:

```bash
pnpm dlx @schpet/linear-cli --version
pnpm dlx @schpet/linear-cli <command...>
```

If neither is available, install the CLI before attempting Linear work.

## Authentication

Prefer existing CLI auth:

```bash
linear auth whoami
```

If auth is missing in an interactive operator shell, use:

```bash
linear auth login
```

In unattended Symphony app-server sessions, missing CLI auth is a real blocker:
record it in the workpad and move the issue according to the workflow's blocker
policy.

## Symphony Rules

- Prefer `linear-cli` for supported high-level operations.
- Do not use Symphony's injected `linear_graphql` tool in these workflows.
- Do not run `linear issue start` inside Symphony unless the workflow explicitly
  asks for branch creation or branch switching. Symphony owns workspace
  lifecycle, and the `pull`/`push` skills own git handoff.
- Prefer `--json` for reads when the output will be parsed.
- Prefer file-based body flags for Markdown.
- Keep issue mutations narrow and verify important writes with a readback.

## Common Commands

```bash
linear issue view ISSUE-123 --json
linear issue query --search "term" --all-teams --json
linear issue update ISSUE-123 --state "In Progress"
linear issue comment list ISSUE-123 --json
linear issue comment add ISSUE-123 --body-file /tmp/comment.md
linear issue comment update COMMENT_ID --body-file /tmp/comment.md
linear issue attach ISSUE-123 ./artifact.png --title "Validation artifact"
linear project list
linear project view PROJECT_ID
linear milestone list --project PROJECT_ID
linear document list
```

## Markdown Bodies

Use files for multi-line Markdown:

```bash
tmpfile="$(mktemp)"
$EDITOR "$tmpfile"
linear issue comment add ISSUE-123 --body-file "$tmpfile"
```

Only use inline `--body` or `--description` for simple one-line text.

## Discovery

Use help output before unfamiliar operations:

```bash
linear --help
linear issue --help
linear issue update --help
linear issue comment --help
```
