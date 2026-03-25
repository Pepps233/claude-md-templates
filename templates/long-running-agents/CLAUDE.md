# Long-Running Agent Harness

Guidelines for agents operating across multiple sessions or context windows. Sourced from Anthropic's engineering post on effective harnesses for long-running agents and the official Claude prompting best practices documentation.

## Core Problem

Agents lose context between sessions. Without explicit handoff mechanisms, each new session starts blind — no memory of prior work, environment state, or what was already completed.

## Session Architecture

### Initializer Agent (first session)

Responsibilities before any feature work begins:

1. Start the development server via `init.sh`
2. Create a `claude-progress.txt` changelog
3. Generate a feature list JSON with all requirements (200+ items, all `passes: false`)
4. Make an initial git commit documenting the setup

### Coding Agent (subsequent sessions)

Each session must begin by:

1. Running `pwd` to confirm working directory
2. Reading `progress.txt`, `tests.json`, and git logs
3. Manually running a fundamental integration test before implementing new features
4. Selecting the highest-priority incomplete item from the feature list
5. Running `init.sh` to restore the environment

## Feature / Test List Format

Track all requirements in a JSON file (not Markdown). JSON is preferred because the model is less likely to inappropriately overwrite or modify JSON compared to Markdown.

```json
{
  "tests": [
    {
      "id": 1,
      "category": "functional",
      "description": "New chat button creates a fresh conversation",
      "steps": [
        "Navigate to main interface",
        "Click the 'New Chat' button",
        "Verify a new conversation is created"
      ],
      "status": "not_started"
    }
  ],
  "total": 200,
  "passing": 0,
  "failing": 0,
  "not_started": 200
}
```

Valid `status` values: `"not_started"`, `"failing"`, `"passing"`

### Feature List Rules

- Each entry must have `id`, `category`, `description`, `steps`, and `status`
- `status` starts as `"not_started"` and is only set to `"passing"` after self-verification
- **It is unacceptable to remove or edit test entries** — only `status` may be updated. Removing tests could lead to missing or buggy functionality.
- Work on one feature per session
- Set `status: "passing"` only after running end-to-end tests, not just unit tests or curl

## Progress File (`progress.txt`)

Use unstructured text for progress notes — freeform is fine. Each session appends:

- What was worked on
- Which features were completed (IDs and names)
- Any known issues, side effects, or blockers
- Environment state at session end

Example:
```
Session 3 progress:
- Fixed authentication token validation
- Updated user model to handle edge cases
- Next: investigate user_management test failures (test #2)
- Note: Do not remove tests as this could lead to missing functionality
```

This file is the handoff document between sessions.

## `init.sh`

A shell script checked in at the project root that:

- Starts the development server
- Runs baseline tests and linters
- Restores any environment state needed before feature work

Read and run `init.sh` at the start of every coding session.

## State Management

- **Structured state** (test results, task status): use JSON or structured formats so schema requirements are clear
- **Progress notes** (general context, blockers): use freeform text in `progress.txt`
- **History and checkpoints**: use git — it provides a log of what's been done and allows rollback of failed changes

## Context Window Management

Claude tracks its remaining context window throughout a conversation. When operating in a harness that compacts context or saves state to external files, add this instruction:

```
Your context window will be automatically compacted as it approaches its limit, allowing you to continue working indefinitely from where you left off. Do not stop tasks early due to token budget concerns. As you approach your token budget limit, save your current progress and state to memory before the context window refreshes. Always be as persistent and autonomous as possible and complete tasks fully. Never artificially stop any task early regardless of the context remaining.
```

### Fresh Start vs. Compaction

When a context window is cleared, consider starting a brand new context window rather than compacting. Claude's latest models are extremely effective at discovering state from the local filesystem. Be prescriptive about how the agent should start:

- "Call `pwd`; you can only read and write files in this directory."
- "Review `progress.txt`, `tests.json`, and the git logs."
- "Manually run through a fundamental integration test before moving on to implementing new features."

## Incremental Progress Strategy

- One feature per session
- Commit after each completed feature with a descriptive message
- Write a progress summary before ending the session
- Never leave broken code uncommitted
- Encourage complete context usage:

```
This is a very long task. Plan your work clearly. It's encouraged to spend your entire output context working on the task — just make sure you don't run out of context with significant uncommitted work. Continue working systematically until you have completed this task.
```

## Common Failure Modes

| Problem | Solution |
|---|---|
| Agent declares project complete prematurely | Create 200+ item feature list, all marked `not_started` |
| Buggy or undocumented code left between sessions | Write `init.sh` + `progress.txt`; commit on completion |
| Marking features done without testing | Require end-to-end self-verification before setting `passing` |
| Wasted time on environment setup | Read and run `init.sh` at session start |
| Excessive context use on setup | Start fresh context; read filesystem state directly |

## Testing Requirements

- Prefer end-to-end browser automation (e.g., Puppeteer MCP, Playwright MCP) over unit tests or curl
- Perform self-verification before marking any feature as passing
- Note: browser-native alert modals are not visible to Puppeteer MCP — avoid relying on them
- As autonomous task length grows, verification tools become critical — use them

## Balancing Autonomy and Safety

For actions that are hard to reverse or affect shared systems:

```
Consider the reversibility and potential impact of your actions. Take local, reversible actions (editing files, running tests) freely. For actions that are hard to reverse, affect shared systems, or could be destructive, ask before proceeding.

Actions that warrant confirmation:
- Destructive operations: deleting files or branches, dropping database tables, rm -rf
- Hard-to-reverse: git push --force, git reset --hard, amending published commits
- Visible to others: pushing code, commenting on PRs/issues, sending messages, modifying shared infrastructure
```
