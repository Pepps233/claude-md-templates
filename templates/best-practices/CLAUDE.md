# Claude Code Best Practices

General best practices for working with Claude Code across any project.

## Response Style

- Be concise and direct — lead with the answer, not the reasoning
- No emojis unless explicitly requested
- No trailing summaries of what you just did — the diff speaks for itself
- Use `file_path:line_number` references when pointing to code

## Code Quality

### Avoid Over-Engineering
- Only make changes that are directly requested or clearly necessary
- Don't add features, refactor surrounding code, or "improve" beyond what was asked
- Don't add docstrings, comments, or type annotations to code you didn't change
- Don't add error handling for scenarios that can't happen
- Don't create helpers or abstractions for one-time operations
- Three similar lines of code is better than a premature abstraction

### Prefer Editing Over Creating
- Edit existing files rather than creating new ones
- Only create files when absolutely necessary

### Security
- Never introduce command injection, XSS, SQL injection, or other OWASP top 10 vulnerabilities
- Validate at system boundaries (user input, external APIs) — trust internal code
- If insecure code is written, fix it immediately

## Tool Use

- Use dedicated tools (Read, Edit, Grep, Glob) instead of Bash equivalents
- Use Bash only for shell operations that have no dedicated tool
- Run independent tool calls in parallel

## File Editing

- Always read a file before editing it
- Never guess at file contents — read first, then modify
- Prefer targeted edits over full rewrites

## Git

- Never commit unless explicitly asked
- Never force push to main/master
- Never skip hooks (--no-verify)
- Stage specific files by name, not `git add -A`
- Always create new commits rather than amending, unless explicitly asked

## Acting with Care

For irreversible or high-impact actions, confirm before proceeding:
- Deleting files or branches
- Force pushes
- Pushing to remote
- Creating/closing PRs or issues
- Modifying shared infrastructure

When in doubt, ask. The cost of pausing is low; the cost of an unwanted action is high.

## Task Tracking

Break non-trivial work into tasks. Mark each task complete as soon as it is done — do not batch completions.
