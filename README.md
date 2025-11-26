# Megatron-LM Cursor Commands

A collection of [Cursor IDE](https://cursor.com/) custom commands designed for Megatron-LM developers. These prompts help streamline code reviews and PR comprehension for the [NVIDIA Megatron-LM](https://github.com/NVIDIA/Megatron-LM) codebase.

## Available Commands

### `mcore-review-pr`

A comprehensive code review prompt for Megatron-LM pull requests.

**Features:**
- **Severity Classification** — Issues categorized as P0 (Critical), P1 (Important), P2 (Suggestion), P3 (Nitpick)
- **Four-Phase Review Workflow** — Understand → Verify Tests → Analyze Code → Generate Output
- **Detailed Checklists** — Covers correctness, distributed training safety, numerical stability, performance, readability, and maintainability
- **Actionable Output** — Produces copy-paste ready GitHub review comments

### `mcore-explain-pr`

A deep-dive prompt for understanding Megatron-LM PR implementations.

**Features:**
- **Top-Down + Execution Path Tracing** — Systematic methodology for understanding complex changes
- **Mermaid Diagrams** — Architecture comparisons, sequence diagrams, data flow visualizations, and class diagrams
- **Structured Output** — TL;DR, Big Picture, Design Rationale, Execution Path Deep Dive, Module Relationships, Risks & Edge Cases
- **Quick Reference** — File change summaries and key function indexes

## Usage

### Installation

Place the `.md` files in your Cursor commands directory:

```
~/.cursor/commands/mcore-cursor-commands/
├── mcore-review-pr.md
└── mcore-explain-pr.md
```

### Invocation

In Cursor, use the command palette or type:

```
/mcore-review-pr <PR-number>
/mcore-explain-pr <PR-number>
```

The commands will fetch the PR context from GitHub and provide structured analysis.

## License

Apache License 2.0 — See [LICENSE](LICENSE) for details.
