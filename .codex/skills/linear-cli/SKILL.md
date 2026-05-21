---
name: linear-cli
description: |
  Use the `linear` command-line client for agent-friendly Linear issue,
  comment, project, milestone, document, and raw GraphQL operations.
  Prefer this skill for high-level Linear work when the CLI is available;
  use Symphony's `linear` skill for the injected `linear_graphql` fallback.
---

# Linear CLI

Use `linear-cli` for common Linear operations from the shell. It is more
agent-friendly than hand-written GraphQL for issue reads, comments, state
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

If neither is available, use the repo-local `linear` skill and its
`linear_graphql` tool instead.

## Authentication

Prefer existing CLI auth:

```bash
linear auth whoami
```

If auth is missing in an interactive operator shell, use:

```bash
linear auth login
```

In unattended Symphony app-server sessions, do not stop just because CLI auth is
missing. Fall back to `linear_graphql`, which reuses Symphony's configured
Linear auth for the session.

## Symphony Rules

- Prefer `linear-cli` for supported high-level operations.
- Use `linear_graphql` for exact custom mutations, uploads, schema
  introspection, or when CLI install/auth is unavailable.
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

For raw GraphQL through the CLI:

```bash
linear api --variable issueId=ISSUE_ID <<'GRAPHQL'
query($issueId: String!) {
  issue(id: $issueId) {
    id
    identifier
    title
    state { name type }
  }
}
GRAPHQL
```

Use heredocs for GraphQL documents with `!` type markers to avoid shell
escaping problems.

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
linear api --help
```
