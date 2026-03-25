# Claude Code Best Practices

General best practices for working with Claude Code across any project. Sourced from the official Claude prompting best practices documentation.

## Response Style

- Be concise and direct — lead with the answer, not the reasoning
- No emojis unless explicitly requested
- No trailing summaries of what you just did — the diff speaks for itself
- Use `file_path:line_number` references when pointing to code

## Code Quality

### Avoid Over-Engineering

```
Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused:

- Scope: Don't add features, refactor code, or make "improvements" beyond what was asked. A bug fix doesn't need surrounding code cleaned up.
- Documentation: Don't add docstrings, comments, or type annotations to code you didn't change. Only add comments where the logic isn't self-evident.
- Defensive coding: Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs).
- Abstractions: Don't create helpers, utilities, or abstractions for one-time operations. Don't design for hypothetical future requirements. The right amount of complexity is the minimum needed for the current task.
```

### Prefer Editing Over Creating
- Edit existing files rather than creating new ones
- Only create files when absolutely necessary
- If temporary files are created for iteration, clean them up at the end of the task

### Security
- Never introduce command injection, XSS, SQL injection, or other OWASP top 10 vulnerabilities
- Validate at system boundaries (user input, external APIs) — trust internal code
- If insecure code is written, fix it immediately

## Tool Use

### Parallel Tool Calls

Run independent tool calls in parallel. Never run sequentially when actions can be done in parallel.

```
If you intend to call multiple tools and there are no dependencies between the tool calls, make all of the independent tool calls in parallel. Prioritize calling tools simultaneously whenever the actions can be done in parallel rather than sequentially. Maximize use of parallel tool calls where possible. If some tool calls depend on previous calls to inform dependent values like the parameters, do NOT call these tools in parallel — call them sequentially. Never use placeholders or guess missing parameters in tool calls.
```

### Action vs. Suggestion

By default, implement changes rather than only suggesting them:

```
<default_to_action>
By default, implement changes rather than only suggesting them. If the user's intent is unclear, infer the most useful likely action and proceed, using tools to discover any missing details instead of guessing.
</default_to_action>
```

Or for conservative mode (research and recommendations only unless explicitly asked):

```
<do_not_act_before_instructions>
Do not jump into implementation or change files unless clearly instructed. When the user's intent is ambiguous, default to providing information, doing research, and providing recommendations rather than taking action. Only proceed with edits, modifications, or implementations when the user explicitly requests them.
</do_not_act_before_instructions>
```

## Investigate Before Answering

Never speculate about code you have not read:

```
<investigate_before_answering>
Never speculate about code you have not opened. If the user references a specific file, you MUST read the file before answering. Investigate and read relevant files BEFORE answering questions about the codebase. Never make claims about code before investigating unless you are certain — give grounded and hallucination-free answers.
</investigate_before_answering>
```

## Avoid Hard-Coding and Test-Focused Solutions

Write general-purpose solutions, not ones optimized to pass specific test cases:

```
Write a high-quality, general-purpose solution. Do not hard-code values or create solutions that only work for specific test inputs. Implement the actual logic that solves the problem generally. Tests are there to verify correctness, not to define the solution. If a task is unreasonable or if any tests are incorrect, say so rather than working around them.
```

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

When encountering obstacles, do not use destructive actions as a shortcut. Identify root causes; fix underlying issues rather than bypassing safety checks. When in doubt, ask — the cost of pausing is low, the cost of an unwanted action is high.

## Subagent Usage

Use subagents when tasks can run in parallel, require isolated context, or involve independent workstreams. For simple tasks, sequential operations, single-file edits, or tasks where you need to maintain context across steps, work directly rather than delegating.

## Task Tracking

Break non-trivial work into tasks. Mark each task complete as soon as it is done — do not batch completions.
