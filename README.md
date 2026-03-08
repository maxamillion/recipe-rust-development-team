# Rust Development Team Recipe

A Goose Recipe that implements a multi-agent workflow for professional Rust software development.

## Two Versions Available

This project provides **two implementations** of the same workflow:

1. **Monolithic** (`rust-development-team.yaml`) - All-in-one recipe, single file
2. **Modular** (`rust-development-team-modular.yaml`) - Coordinator + reusable persona sub-recipes

See [MODULAR_ARCHITECTURE.md](MODULAR_ARCHITECTURE.md) for detailed comparison and usage.

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

## Which Version to Use?

### Monolithic (`rust-development-team.yaml`)

**Best for:**
- Quick, straightforward tasks
- Single-file deployment
- Environments where sub-recipes aren't supported
- Getting started quickly

**Pros:** Simple, self-contained, no dependencies

**Cons:** Not reusable, harder to customize

### Modular (`rust-development-team-modular.yaml`)

**Best for:**
- Reusing personas across projects
- Customizing individual personas
- Building persona libraries
- Advanced workflows

**Pros:** Reusable components, easier to maintain, extensible

**Cons:** More files, requires sub-recipe support

### Quick Start

**Try the monolithic version first:**
```bash
goose run rust-development-team.yaml \
  --param task="Create a simple calculator struct"
```

**Then explore the modular version** for advanced use cases.

## Usage

### Basic Usage (Monolithic)

```bash
goose run rust-development-team.yaml \
  --param task="Create a thread-safe cache with TTL support"
```

### Basic Usage (Modular)

```bash
goose run rust-development-team-modular.yaml \
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

### Using Individual Personas (Modular Only)

```bash
# Just get architectural design
goose run personas/architect.yaml \
  --param task="Design a web scraping system"

# Just code review
goose run personas/quality-engineer.yaml \
  --param task="Review implementation" \
  --param implementation="$(cat src/lib.rs)"
```

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `task` | string | Yes | - | The Rust development task to complete |
| `requirements` | string | No | "Follow Rust best practices..." | Specific requirements or constraints |
| `max_review_cycles` | number | No | 10 | Maximum developer/QE feedback iterations (1-10) |

## What Gets Delivered

At the end of the workflow, you'll receive:

- **Architectural documentation** - System design and component structure
- **Production-ready Rust code** - Safe, idiomatic implementation
- **Comprehensive tests** - Unit tests with good coverage
- **Code review feedback** - Detailed quality assessment
- **Documentation** - Inline docs for public APIs

## Quality Standards

The Quality Engineer ensures:

- ✅ Code compiles without warnings
- ✅ All tests pass
- ✅ Follows Rust idioms and best practices
- ✅ Proper error handling with Result types
- ✅ Memory safety (minimal/no unsafe code)
- ✅ Thread safety where applicable
- ✅ Good test coverage including edge cases
- ✅ Clear documentation

## Example Tasks

This recipe works well for:

- **Data structures**: "Implement a lock-free queue"
- **CLI tools**: "Build a grep clone with regex support"
- **Libraries**: "Create a JSON parser library"
- **System utilities**: "Write a file watcher with notification support"
- **API clients**: "Build a REST API client with async support"
- **Algorithms**: "Implement a B-tree with insertion and deletion"

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

## Modular Architecture

The modular version provides reusable persona recipes:

```
personas/
├── architect.yaml         # Standalone architect persona
├── rust-developer.yaml    # Standalone developer persona
└── quality-engineer.yaml  # Standalone QE persona
```

**Benefits:**
- Use personas independently or together
- Customize individual personas without affecting others
- Share personas across different projects
- Mix and match with your own personas

**Example - Custom QE:**
```bash
# Copy and customize the QE persona
cp personas/quality-engineer.yaml personas/security-focused-qe.yaml
# Edit to add security-specific checks
# Update coordinator to use your custom version
```

See [MODULAR_ARCHITECTURE.md](MODULAR_ARCHITECTURE.md) for complete details.

## License

This recipe is provided as-is for use with Goose.

## Contributing

Feedback and improvements welcome! This recipe can be extended with:

- Additional personas (e.g., Security Reviewer, Performance Engineer)
- Integration with Rust-specific MCP extensions
- Customizable quality standards
- Project template support
