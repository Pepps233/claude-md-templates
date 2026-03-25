# claude-md-templates

A curated collection of CLAUDE.md files for use across projects. These templates encode best practices, workflow conventions, and agent behavior guidelines for Claude Code.

## Structure

```
claude-md-templates/
├── README.md
└── templates/
    ├── long-running-agents/
    │   └── CLAUDE.md        # Multi-session agent harness: JSON test tracking, init.sh, progress files
    ├── commit-conventions/
    │   └── CLAUDE.md        # Conventional commit format with scopes and types
    ├── best-practices/
    │   └── CLAUDE.md        # General Claude Code best practices and safety guidelines
    └── prompt-engineering/
        └── CLAUDE.md        # Prompt structure, XML tags, few-shot examples, output formatting
```

## Templates

### `templates/long-running-agents/`
Harness patterns for agents operating across multiple context windows. Covers JSON-based feature/test tracking (`tests.json`), session continuity via `progress.txt`, `init.sh` for environment setup, context window management, incremental git commits, and balancing autonomy with safety. Sourced from Anthropic's engineering post and official prompting best practices.

### `templates/commit-conventions/`
Conventional commit format with typed scopes (`feat`, `fix`, `refactor`, etc.), imperative tense rules, and body guidelines. Sourced from a production project.

### `templates/best-practices/`
General best practices for Claude Code: response style, avoiding over-engineering, parallel tool calls, action vs. suggestion defaults, hallucination prevention, and acting with care on irreversible operations. Sourced from the official Claude prompting best practices documentation.

### `templates/prompt-engineering/`
Writing effective system prompts and user prompts: clarity principles, context and motivation, few-shot examples, XML structuring, long context handling, output format control, thinking/reasoning guidance, and agentic coding directives. Sourced from the official Claude prompting best practices documentation.

## Usage

Copy the relevant `CLAUDE.md` into the root of your project, or combine sections from multiple templates as needed.
