# Long-Running Agent Harness

Guidelines for agents operating across multiple sessions or context windows, based on Anthropic's engineering post on effective harnesses for long-running agents.

## Core Problem

Agents lose context between sessions. Without explicit handoff mechanisms, each new session starts blind — no memory of prior work, environment state, or what was already completed.

## Session Architecture

### Initializer Agent (first session)

Responsibilities before any feature work begins:

1. Start the development server via `init.sh`
2. Create a `claude-progress.txt` changelog
3. Generate a feature list JSON with all requirements
4. Make an initial git commit documenting the setup

### Coding Agent (subsequent sessions)

Each session must begin by:

1. Running `pwd` to confirm working directory
2. Reading git log and `claude-progress.txt`
3. Selecting the highest-priority incomplete feature from the feature list
4. Running `init.sh` to restore the environment

## Feature List Format

Track all requirements in a JSON file (not Markdown). JSON is preferred because the model is less likely to inappropriately overwrite or modify JSON compared to Markdown.

```json
[
  {
    "category": "functional",
    "description": "New chat button creates a fresh conversation",
    "steps": [
      "Navigate to main interface",
      "Click the 'New Chat' button",
      "Verify a new conversation is created"
    ],
    "passes": false
  }
]
```

### Feature List Rules

- Each feature must have a `category`, `description`, `steps` array, and `passes` boolean
- `passes` starts as `false` and is only set to `true` after self-verification
- **It is unacceptable to remove or edit feature entries** — only the `passes` field may be updated
- Work on one feature per session
- Mark `passes: true` only after running end-to-end tests, not just unit tests or curl

## Progress File (`claude-progress.txt`)

A session log maintained across context windows. Each session appends:

- What was worked on
- What was completed (which feature, what `passes` was set to `true`)
- Any known issues, side effects, or blockers
- Environment state at session end

This file is the handoff document between sessions.

## `init.sh`

A shell script checked in at the project root that:

- Starts the development server
- Runs baseline tests
- Restores any environment state needed before feature work

Read `init.sh` at the start of every coding session.

## Incremental Progress Strategy

- One feature per session
- Commit after each completed feature with a descriptive message
- Write a progress summary before ending the session
- Use git to enable rollback of failed changes — never leave broken code uncommitted

## Common Failure Modes

| Problem | Solution |
|---|---|
| Agent declares project complete prematurely | Create 200+ item feature list, all marked `passes: false` |
| Buggy or undocumented code left between sessions | Write `init.sh` + `claude-progress.txt`; commit on completion |
| Marking features done without testing | Require end-to-end self-verification before setting `passes: true` |
| Wasted time on environment setup | Read `init.sh` at session start; never guess at setup |

## Testing Requirements

- Prefer end-to-end browser automation (e.g., Puppeteer MCP) over unit tests or curl
- Perform self-verification before marking any feature as passing
- Note: browser-native alert modals are not visible to Puppeteer MCP — avoid relying on them
