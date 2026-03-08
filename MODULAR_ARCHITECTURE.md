# Modular Architecture

This directory contains two implementations of the Rust Development Team workflow:

1. **Monolithic** (`rust-development-team.yaml`) - All personas in one recipe
2. **Modular** (`rust-development-team-modular.yaml`) - Coordinator + persona sub-recipes

## Architecture Comparison

### Monolithic (Original)

```
rust-development-team.yaml
└── Contains all three personas inline
    ├── Lead Architect instructions
    ├── Rust Developer instructions
    └── Quality Engineer instructions
```

**Pros:**
- Single file, easy to deploy
- Simple to understand
- No dependencies between files

**Cons:**
- Large, complex single file
- Personas not reusable
- Harder to maintain and customize
- All personas share the same context

### Modular (New)

```
rust-development-team-modular.yaml (Coordinator)
├── Orchestrates workflow
├── Manages state and flow control
└── Invokes sub-recipes:
    ├── personas/architect.yaml
    ├── personas/rust-developer.yaml
    └── personas/quality-engineer.yaml
```

**Pros:**
- Each persona is a standalone, reusable recipe
- Easier to maintain and customize individual personas
- Can use personas independently
- Better separation of concerns
- Can run personas in parallel (future enhancement)
- Cleaner, more focused code in each file

**Cons:**
- More files to manage
- Slightly more complex to deploy
- Requires sub-recipe support in Goose

## File Structure

```
recipe-rust-development-team/
├── rust-development-team.yaml          # Monolithic version
├── rust-development-team-modular.yaml  # Modular coordinator
└── personas/
    ├── architect.yaml                  # Lead Architect persona
    ├── rust-developer.yaml             # Rust Developer persona
    └── quality-engineer.yaml           # Quality Engineer persona
```

## Using the Modular Version

### Full Workflow

```bash
goose run rust-development-team-modular.yaml \
  --param task="Build a thread-safe cache" \
  --param requirements="Support TTL, use std library only"
```

### Using Individual Personas

You can also invoke personas directly for specific tasks:

#### Architecture Only

```bash
goose run personas/architect.yaml \
  --param task="Design a web scraping system" \
  --param requirements="Async, rate limiting, robots.txt compliance"
```

#### Implementation Only (with existing plan)

```bash
goose run personas/rust-developer.yaml \
  --param task="Implement the web scraper" \
  --param architectural_plan="$(cat architecture.md)"
```

#### Code Review Only

```bash
goose run personas/quality-engineer.yaml \
  --param task="Review web scraper implementation" \
  --param implementation="$(cat src/lib.rs)"
```

## Customization Strategies

### Modular Architecture Benefits

1. **Replace Individual Personas**

   Don't like the default QE? Create your own:
   ```bash
   # Use custom QE with standard architect and developer
   cp personas/quality-engineer.yaml personas/security-focused-qe.yaml
   # Edit security-focused-qe.yaml
   # Update rust-development-team-modular.yaml to reference it
   ```

2. **Add New Personas**

   Create `personas/performance-engineer.yaml`:
   ```yaml
   title: "Performance Engineer"
   description: "Specializes in performance analysis and optimization"
   # ... persona implementation
   ```

   Update the coordinator to invoke it after QE review.

3. **Mix and Match**

   Use the architect from this recipe with your own developer:
   ```bash
   # Get architecture
   goose run personas/architect.yaml --param task="..." > architecture.md

   # Use your own implementation workflow
   your-custom-dev-workflow --input architecture.md
   ```

4. **Parallel Reviews**

   Modify the coordinator to run QE and Security reviews in parallel:
   ```yaml
   # In coordinator, Phase 3:
   # Invoke both quality-engineer.yaml and security-engineer.yaml
   # Combine their feedback for the developer
   ```

## Persona Specifications

### Architect (personas/architect.yaml)

**Purpose:** Research and design system architecture

**Inputs:**
- `task` (required): What to architect
- `requirements` (optional): Constraints and requirements

**Outputs:**
- Comprehensive architectural plan
- Module organization
- Type designs
- Implementation roadmap

**Use Independently When:**
- Planning a new Rust project
- Evaluating design alternatives
- Creating technical specifications

---

### Rust Developer (personas/rust-developer.yaml)

**Purpose:** Implement Rust code following plans and feedback

**Inputs:**
- `task` (required): What to implement
- `architectural_plan` (optional): Design to follow
- `feedback` (optional): QE feedback to address

**Outputs:**
- Complete Rust implementation
- Unit and integration tests
- Documentation
- Build/test results

**Use Independently When:**
- Implementing from an existing design
- Refactoring based on review feedback
- Adding features to existing code

---

### Quality Engineer (personas/quality-engineer.yaml)

**Purpose:** Review code for quality, safety, and correctness

**Inputs:**
- `task` (required): What was being implemented
- `implementation` (required): Code to review
- `architectural_plan` (optional): Original design
- `previous_feedback` (optional): Prior review comments

**Outputs:**
- Prioritized feedback (Critical/Important/Suggested)
- Test results
- APPROVED or REQUEST CHANGES decision

**Use Independently When:**
- Reviewing existing Rust code
- Auditing third-party code
- Pre-merge quality checks

## Migration Guide

### From Monolithic to Modular

If you're currently using the monolithic version:

1. **Test the modular version** with a simple task
   ```bash
   goose run rust-development-team-modular.yaml \
     --param task="Create a simple struct" \
     --param max_review_cycles=2
   ```

2. **Customize personas** as needed in the `personas/` directory

3. **Update your workflows** to use the modular version

4. **Keep the monolithic version** as a fallback if sub-recipes aren't supported

### Hybrid Approach

You can use both:
- **Modular** for complex projects where you need customization
- **Monolithic** for quick, simple tasks

## Advanced: Extending the Workflow

### Adding a Security Reviewer

1. Create `personas/security-engineer.yaml`
2. Update the coordinator to invoke it after QE approval
3. Add another feedback loop if security issues found

### Creating Domain-Specific Teams

**Web Development Team:**
```
web-dev-team-modular.yaml
├── personas/web-architect.yaml       # Specializes in web architecture
├── personas/rust-web-developer.yaml  # Knows axum, actix-web, etc.
└── personas/api-reviewer.yaml        # Reviews API design
```

**Embedded Team:**
```
embedded-team-modular.yaml
├── personas/embedded-architect.yaml
├── personas/embedded-developer.yaml  # no_std expert
└── personas/embedded-tester.yaml     # Hardware testing focus
```

## Best Practices

1. **Version Control**: Track persona recipes separately
2. **Documentation**: Document any custom personas you create
3. **Testing**: Test persona recipes independently
4. **Sharing**: Share useful custom personas with the community
5. **Naming**: Use descriptive names for custom personas

## Future Enhancements

Potential improvements to the modular architecture:

- **Parallel Execution**: Run independent reviews concurrently
- **Persona Swapping**: Easy runtime persona replacement
- **Metrics Collection**: Track performance of different personas
- **Persona Marketplace**: Share and discover community personas
- **Dynamic Routing**: Coordinator chooses personas based on task type

## Troubleshooting

### Sub-recipe not found

**Error:** `Cannot find sub-recipe: personas/architect.yaml`

**Solution:** Ensure you're running from the correct directory:
```bash
cd recipe-rust-development-team
goose run rust-development-team-modular.yaml --param task="..."
```

### Personas not being invoked

If the coordinator isn't properly invoking personas, it may be due to Goose version differences. Fallback options:

1. Use the monolithic version
2. Manually copy persona instructions into the coordinator
3. Check Goose documentation for sub-recipe support

## Contributing

If you create useful custom personas or coordinator variations:

1. Document them well
2. Add examples of their use
3. Submit a PR to share with the community
4. Update this architecture guide

## Questions?

- See the main [README.md](README.md) for general usage
- Check [CUSTOMIZATION.md](CUSTOMIZATION.md) for modification guides
- Review persona YAML files for implementation details
