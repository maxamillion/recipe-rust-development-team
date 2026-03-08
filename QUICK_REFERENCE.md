# Quick Reference

## Basic Commands

```bash
# Simple task
goose run rust-development-team.yaml \
  --param task="Build a JSON parser"

# With requirements
goose run rust-development-team.yaml \
  --param task="Build a web server" \
  --param requirements="Use axum, include middleware, async/await"

# Limit iterations
goose run rust-development-team.yaml \
  --param task="Simple utility function" \
  --param max_review_cycles=3
```

## Parameters

| Parameter | Required | Default | Example |
|-----------|----------|---------|---------|
| `task` | ✓ | - | "Build a CLI tool" |
| `requirements` | ✗ | "Follow Rust best practices..." | "Use clap, support JSON output" |
| `max_review_cycles` | ✗ | 10 | 5 |

## Workflow Phases

1. **Architect** - Research and design (Phase 1)
2. **Developer** - Implementation (Phase 2)
3. **QE** - Review and test (Phase 3)
4. **Developer** - Address feedback (Phase 4)
5. **QE** - Re-review (Phase 5)

Phases 4-5 iterate until approved or max cycles reached.

## Common Use Cases

### Data Structures
```bash
goose run rust-development-team.yaml \
  --param task="Implement a thread-safe queue with bounded capacity"
```

### CLI Tools
```bash
goose run rust-development-team.yaml \
  --param task="Build a file search tool" \
  --param requirements="Use clap for CLI, support regex, parallel search"
```

### Libraries
```bash
goose run rust-development-team.yaml \
  --param task="Create a configuration file parser library" \
  --param requirements="Support YAML and TOML, provide validation"
```

### Web Services
```bash
goose run rust-development-team.yaml \
  --param task="Build a REST API for user management" \
  --param requirements="Use axum, include auth middleware, PostgreSQL backend"
```

## Quality Standards

The QE ensures:
- ✅ Compiles without warnings
- ✅ All tests pass
- ✅ Idiomatic Rust code
- ✅ Proper error handling
- ✅ Memory and thread safety
- ✅ Good documentation
- ✅ Comprehensive tests

## Approval Criteria

The workflow completes when the QE says:
- **"APPROVED"** - Quality standards met
- Or **max_review_cycles** iterations reached

## Tips

1. **Be specific** - Detailed tasks get better results
2. **Set expectations** - Use requirements for dependencies/patterns
3. **Right-size tasks** - Break large features into smaller pieces
4. **Review early** - Check the architecture plan before implementation
5. **Iterate wisely** - Set max_review_cycles based on task complexity

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Recipe not found | Use full path: `./rust-development-team.yaml` |
| Missing parameter | Always include `--param task="..."` |
| Too many cycles | Reduce `max_review_cycles` or simplify task |
| Times out | Break into smaller tasks or increase `max_turns` |

## Environment Variables

```bash
# Set model provider
export GOOSE_PROVIDER=anthropic
export GOOSE_MODEL=claude-opus-4

# API keys
export ANTHROPIC_API_KEY=your_key
export OPENAI_API_KEY=your_key
```

## File Structure

```
rust-development-team/
├── rust-development-team.yaml    # Main recipe
├── README.md                      # Full documentation
├── INSTALLATION.md                # Setup guide
├── CUSTOMIZATION.md               # How to modify
├── QUICK_REFERENCE.md            # This file
└── examples/
    └── EXAMPLE_SESSION.md        # Example walkthrough
```

## Links

- Goose Docs: https://block.github.io/goose/
- Recipe Reference: https://block.github.io/goose/docs/guides/recipes/recipe-reference
- Rust Book: https://doc.rust-lang.org/book/
