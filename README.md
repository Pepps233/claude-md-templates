# claude-md-templates

A curated collection of CLAUDE.md files for use across projects. These templates encode best practices, workflow conventions, and agent behavior guidelines for Claude Code.

## Structure

```
claude-md-templates/
├── README.md
└── templates/
    ├── long-running-agents/
    │   └── CLAUDE.md        # Harness patterns for multi-session agents (feature tracking via JSON)
    ├── commit-conventions/
    │   └── CLAUDE.md        # Conventional commit format with scopes and types
    └── best-practices/
        └── CLAUDE.md        # General Claude Code best practices
```

## Templates

### `templates/long-running-agents/`
Based on Anthropic's engineering post on effective harnesses for long-running agents. Covers JSON-based feature tracking, session continuity patterns, and incremental progress strategies.

### `templates/commit-conventions/`
Conventional commit format with typed scopes (`feat`, `fix`, `refactor`, etc.), imperative tense rules, and body guidelines. Sourced from a production project.

### `templates/best-practices/`
General best practices for working with Claude Code: response style, tool use, code quality, and safety guidelines.

## Usage

Copy the relevant `CLAUDE.md` into the root of your project, or combine sections from multiple templates as needed.
