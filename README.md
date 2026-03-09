# Rust Development Team Recipe

A Goose Recipe that implements a multi-agent workflow for professional Rust software development.

## Overview

This recipe coordinates three specialized AI personas working together to deliver high-quality Rust code:

1. **Lead Architect** - Systems design expert who researches and creates implementation plans
2. **Rust Developer** - Expert in writing safe, idiomatic Rust code
3. **Quality Engineer** - Testing and code review specialist

## Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ PHASE 1: Architecture & Planning (Lead Architect)           │
│ - Research requirements                                      │
│ - Design system architecture                                │
│ - Create implementation plan                                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ PHASE 2: Implementation (Rust Developer)                    │
│ - Review architectural plan                                 │
│ - Implement solution in idiomatic Rust                      │
│ - Write initial tests                                       │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ PHASE 3: Quality Assurance (Quality Engineer)               │
│ - Comprehensive code review                                 │
│ - Test coverage analysis                                    │
│ - Security and safety review                                │
│ - Provide prioritized feedback                              │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
         ┌───────────────┴───────────────┐
         │                               │
         ▼                               ▼
┌──────────────────┐            ┌──────────────────┐
│ PHASE 4:         │◄──────────►│ PHASE 5:         │
│ Refinement       │            │ Re-Review        │
│ (Developer)      │            │ (QE)             │
└──────────────────┘            └──────────────────┘
         │                               │
         │      Iterate up to 10 times   │
         └───────────────┬───────────────┘
                         │
                         ▼
                   ┌──────────┐
                   │ APPROVED │
                   └──────────┘
```

## Quick Start

```bash
goose run rust-development-team.yaml \
  --param task="Create a simple calculator struct"
```

## Usage

### Basic Usage

```bash
goose run rust-development-team.yaml \
  --param task="Create a thread-safe cache with TTL support"
```

### With Custom Requirements

```bash
goose run rust-development-team.yaml \
  --param task="Build a CLI tool for parsing log files" \
  --param requirements="Must support JSON and text formats, use clap for CLI, include error handling with thiserror"
```

### Limit Review Cycles

```bash
goose run rust-development-team.yaml \
  --param task="Implement a binary search tree" \
  --param max_review_cycles=5
```

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `task` | string | Yes | - | The Rust development task to complete |
| `requirements` | string | No | "Follow Rust best practices..." | Specific requirements or constraints |
| `max_review_cycles` | number | No | 10 | Maximum developer/QE feedback iterations (1-10) |

## Using Individual Personas

Each persona is also available as a standalone recipe for focused tasks:

```bash
# Just get architectural design
goose run personas/architect.yaml \
  --param task="Design a web scraping system"

# Just code review
goose run personas/quality-engineer.yaml \
  --param task="Review implementation" \
  --param implementation="$(cat src/lib.rs)"
```

See [STANDALONE_PERSONAS.md](STANDALONE_PERSONAS.md) for complete details on using personas independently.

## What Gets Delivered

At the end of the workflow, you'll receive:

- **Architectural documentation** - System design and component structure
- **Production-ready Rust code** - Safe, idiomatic implementation
- **Comprehensive tests** - Unit tests with good coverage
- **Code review feedback** - Detailed quality assessment
- **Documentation** - Inline docs for public APIs

## Quality Standards

The Quality Engineer ensures:

- Code compiles without warnings
- All tests pass
- Follows Rust idioms and best practices
- Proper error handling with Result types
- Memory safety (minimal/no unsafe code)
- Thread safety where applicable
- Good test coverage including edge cases
- Clear documentation

## Example Tasks

This recipe works well for:

- **Data structures**: "Implement a lock-free queue"
- **CLI tools**: "Build a grep clone with regex support"
- **Libraries**: "Create a JSON parser library"
- **System utilities**: "Write a file watcher with notification support"
- **API clients**: "Build a REST API client with async support"
- **Algorithms**: "Implement a B-tree with insertion and deletion"

## File Structure

```
recipe-rust-development-team/
├── rust-development-team.yaml       # Main recipe (full workflow)
├── personas/
│   ├── architect.yaml               # Standalone architect persona
│   ├── rust-developer.yaml          # Standalone developer persona
│   └── quality-engineer.yaml        # Standalone QE persona
├── README.md
├── INSTALLATION.md
├── CUSTOMIZATION.md
├── STANDALONE_PERSONAS.md
├── QUICK_REFERENCE.md
└── examples/
    └── EXAMPLE_SESSION.md
```

## Tips

1. **Be specific in your task description** - The more detail you provide, the better the architecture phase
2. **Use requirements parameter** - Specify dependencies, patterns, or constraints upfront
3. **Start small** - Complex systems can be broken into multiple recipe runs
4. **Review the architecture** - The plan from Phase 1 guides everything else
5. **Trust the process** - The feedback loop ensures quality improves iteratively

## Advanced Usage

### Integration with Your Project

If you're adding to an existing Rust project:

```bash
goose run rust-development-team.yaml \
  --param task="Add authentication module to existing web server" \
  --param requirements="Use existing database connection pool, integrate with actix-web, follow project's error handling pattern"
```

### Combining with Other Tools

The recipe works well alongside:

- `cargo fmt` - Format the generated code
- `cargo clippy` - Additional linting
- `cargo test` - Run the generated tests
- `cargo doc` - Generate documentation

## Troubleshooting

**Recipe doesn't finish**: Check that your task is well-scoped. Very large tasks might hit the turn limit.

**Too many review cycles**: The QE might be too strict. Try reducing `max_review_cycles` or simplifying requirements.

**Architecture phase too brief**: Make your task description more detailed and specific.

## License

This recipe is provided as-is for use with Goose.

## Contributing

Feedback and improvements welcome! This recipe can be extended with:

- Additional personas (e.g., Security Reviewer, Performance Engineer)
- Customizable quality standards
- Project template support
