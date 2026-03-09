# Standalone Personas

## Overview

The primary recipe for the full Rust development workflow is **`rust-development-team.yaml`**. It orchestrates all three personas (Architect, Developer, Quality Engineer) in a single conversation with shared state, enabling the sequential workflow where each phase builds on the previous one.

The `personas/` directory contains each persona as a **standalone recipe** that can be run independently for focused, single-purpose tasks.

## File Structure

```
recipe-rust-development-team/
├── rust-development-team.yaml       # Full workflow (all three personas)
└── personas/
    ├── architect.yaml               # Standalone: architecture & planning
    ├── rust-developer.yaml          # Standalone: implementation
    └── quality-engineer.yaml        # Standalone: code review & testing
```

## Using Individual Personas

### Architecture Only

Use the architect persona when you need a design plan without implementation:

```bash
goose run personas/architect.yaml \
  --param task="Design a web scraping system" \
  --param requirements="Async, rate limiting, robots.txt compliance"
```

### Implementation Only

Use the developer persona when you already have a plan or want to implement directly:

```bash
goose run personas/rust-developer.yaml \
  --param task="Implement the web scraper" \
  --param architectural_plan="$(cat architecture.md)"
```

### Code Review Only

Use the quality engineer persona to review existing code:

```bash
goose run personas/quality-engineer.yaml \
  --param task="Review web scraper implementation" \
  --param implementation="$(cat src/lib.rs)"
```

## Why Not Sub-Recipes?

Goose supports a `sub_recipes:` field for composing recipes, but it does not work for this workflow. Goose sub-recipes run in **isolated sessions with no shared state**, and by default run **in parallel**. The Rust Development Team workflow requires sequential execution where each persona's output feeds into the next (Architect plan -> Developer implementation -> QE review -> Developer refinement). This is impossible with isolated sub-recipe sessions.

The main recipe (`rust-development-team.yaml`) solves this by switching personas within a single conversation, which preserves full context between phases.

## Persona Details

### Architect (`personas/architect.yaml`)

**Purpose:** Research and design system architecture.

**Parameters:**
- `task` (required): What to architect
- `requirements` (optional): Constraints and requirements

**Use when:**
- Planning a new Rust project
- Evaluating design alternatives
- Creating technical specifications

---

### Rust Developer (`personas/rust-developer.yaml`)

**Purpose:** Implement Rust code following plans and feedback.

**Parameters:**
- `task` (required): What to implement
- `architectural_plan` (optional): Design to follow
- `feedback` (optional): QE feedback to address

**Use when:**
- Implementing from an existing design
- Refactoring based on review feedback
- Adding features to existing code

---

### Quality Engineer (`personas/quality-engineer.yaml`)

**Purpose:** Review code for quality, safety, and correctness.

**Parameters:**
- `task` (required): What was being implemented
- `implementation` (required): Code to review
- `architectural_plan` (optional): Original design
- `previous_feedback` (optional): Prior review comments

**Use when:**
- Reviewing existing Rust code
- Auditing third-party code
- Pre-merge quality checks

## Customization

You can copy and modify any persona for your specific needs:

```bash
# Create a security-focused reviewer
cp personas/quality-engineer.yaml personas/security-reviewer.yaml
# Edit to add security-specific checks
```

See [CUSTOMIZATION.md](CUSTOMIZATION.md) for more modification examples.

## Questions?

- See the main [README.md](README.md) for general usage
- Check [CUSTOMIZATION.md](CUSTOMIZATION.md) for modification guides
- Review persona YAML files for implementation details
