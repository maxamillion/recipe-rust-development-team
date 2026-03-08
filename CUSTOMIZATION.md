# Customizing the Rust Development Team Recipe

This guide explains how to customize and extend the recipe for your specific needs.

## Basic Customization

### Adjusting Review Cycles

Change the default maximum review cycles by editing the parameter default:

```yaml
parameters:
  - key: "max_review_cycles"
    input_type: "number"
    requirement: "optional"
    description: "Maximum number of developer/QE feedback cycles (1-10)"
    default: 5  # Changed from 10 to 5
```

### Changing Model Settings

Modify the `settings` section to use different models or parameters:

```yaml
settings:
  goose_provider: "anthropic"
  goose_model: "claude-opus-4"
  temperature: 0.7
  max_turns: 150  # Increased from 100 for more complex tasks
```

### Adding New Parameters

Add custom parameters for your workflow:

```yaml
parameters:
  # ... existing parameters ...

  - key: "target_performance"
    input_type: "string"
    requirement: "optional"
    description: "Performance requirements (e.g., 'optimize for speed', 'optimize for memory')"
    default: "balanced"

  - key: "dependency_policy"
    input_type: "select"
    requirement: "optional"
    description: "Policy for external dependencies"
    default: "minimal"
    options:
      - "minimal"
      - "pragmatic"
      - "unrestricted"
```

Then reference them in instructions:

```yaml
instructions: |
  **Performance Target:** {{ target_performance }}
  **Dependency Policy:** {{ dependency_policy }}
```

## Persona Customization

### Modifying Architect Persona

Enhance the architect's focus areas:

```yaml
## PHASE 1: ARCHITECTURE & PLANNING
**Persona: Lead Architect**

You are a Lead Architect with deep expertise in:
- Systems design and software architecture
- Rust ecosystem and best practices
- Domain-Driven Design (DDD)  # Added
- Microservices architecture  # Added

**Your responsibilities:**
1. Research the task requirements
2. Apply DDD principles to identify bounded contexts  # Added
3. Design the system architecture
# ... rest of responsibilities
```

### Customizing Developer Persona

Add specific coding standards:

```yaml
## PHASE 2: IMPLEMENTATION
**Persona: Rust Developer**

**Additional Coding Standards:**
- Use `thiserror` for error types
- Prefer `anyhow` for application errors
- Always implement `Debug` and `Display` for public types
- Use `tracing` instead of `log` for instrumentation
- Follow conventional commit messages
```

### Enhancing QE Persona

Add specific review criteria:

```yaml
## PHASE 3: QUALITY ASSURANCE
**Persona: Quality Engineer**

**Additional Review Areas:**
1. **Performance Review:**
   - Identify potential bottlenecks
   - Check for unnecessary allocations
   - Verify efficient algorithm choices

2. **Accessibility:** (for CLI tools)
   - Color output has monochrome fallback
   - Help text is comprehensive

3. **Dependency Audit:**
   - All dependencies are well-maintained
   - No unmaintained or deprecated crates
   - License compatibility verified
```

## Workflow Customization

### Adding a Fourth Persona

Add a Security Reviewer:

```yaml
## PHASE 3.5: SECURITY REVIEW
**Persona: Security Engineer**

You are a Security Engineer specializing in Rust security and vulnerability analysis.

**Your responsibilities:**
1. Perform security audit:
   - Input validation and sanitization
   - Potential injection vulnerabilities
   - Cryptographic usage (if applicable)
   - Dependency vulnerabilities (cargo-audit)
2. Check for common security anti-patterns
3. Verify secure defaults
4. Review error messages for information leakage

**Deliverable:** Security assessment report with risk ratings.
```

Update the workflow:

```yaml
# WORKFLOW EXECUTION

1. Start with PHASE 1 (Architect)
2. Move to PHASE 2 (Developer)
3. Move to PHASE 3 (QE)
4. Move to PHASE 3.5 (Security Engineer)  # Added
5. Alternate between PHASE 4 and PHASE 5...
```

### Parallel Review Process

Modify for concurrent QE and Security reviews:

```yaml
## PHASE 3: PARALLEL QUALITY REVIEW

Execute both reviews concurrently:

### PHASE 3A: Quality Engineering Review
[QE persona and responsibilities]

### PHASE 3B: Security Review
[Security persona and responsibilities]

Both reviews provide independent feedback that the Developer addresses in PHASE 4.
```

### Skip Architecture for Small Tasks

Add conditional workflow:

```yaml
# WORKFLOW EXECUTION

**Task Size Detection:**
If the task is small (< 100 lines of code expected):
1. Skip PHASE 1 - proceed directly to PHASE 2
2. Developer creates a minimal design as part of implementation

If the task is medium or large:
1. Execute full workflow starting with PHASE 1
```

## Adding MCP Extensions

### Integrate Rust-Specific Tools

Add extensions for Rust tooling:

```yaml
extensions:
  - type: "stdio"
    name: "rust-analyzer"
    cmd: "rust-analyzer"
    description: "Rust language server for code intelligence"

  - type: "stdio"
    name: "cargo-tools"
    cmd: "cargo"
    description: "Cargo build tool for Rust projects"
    available_tools:
      - "build"
      - "test"
      - "clippy"
      - "fmt"

  - type: "stdio"
    name: "miri"
    cmd: "cargo"
    args: ["miri"]
    description: "Miri interpreter for detecting undefined behavior"
```

Reference in instructions:

```yaml
**Your responsibilities:**
# ...
4. Run `cargo clippy` to catch common mistakes
5. Use `cargo miri test` to verify undefined behavior safety
6. Format code with `cargo fmt`
```

### Add Documentation Tools

```yaml
extensions:
  - type: "stdio"
    name: "cargo-doc"
    cmd: "cargo"
    args: ["doc", "--no-deps"]
    description: "Generate Rust documentation"
```

## Domain-Specific Customization

### Web Development Focus

```yaml
title: "Rust Web Development Team"
description: |
  Specialized for building web applications and APIs with Rust.
  Focuses on async/await, HTTP servers, and web frameworks.

parameters:
  - key: "web_framework"
    input_type: "select"
    requirement: "optional"
    description: "Preferred web framework"
    default: "axum"
    options:
      - "axum"
      - "actix-web"
      - "rocket"
      - "warp"

instructions: |
  # ... existing instructions ...

  **Web Development Requirements:**
  - Use {{ web_framework }} as the web framework
  - Implement proper async error handling
  - Include middleware for logging and error handling
  - Add OpenAPI/Swagger documentation
  - Implement request validation
  - Include CORS configuration
```

### CLI Tool Focus

```yaml
title: "Rust CLI Development Team"

parameters:
  - key: "cli_framework"
    input_type: "select"
    requirement: "optional"
    description: "CLI framework to use"
    default: "clap"
    options:
      - "clap"
      - "structopt"

instructions: |
  **CLI Development Requirements:**
  - Use {{ cli_framework }} for argument parsing
  - Include shell completion scripts
  - Support JSON and text output formats
  - Implement proper exit codes
  - Add progress indicators for long operations
  - Include comprehensive help text
```

### Embedded Systems Focus

```yaml
title: "Rust Embedded Development Team"

parameters:
  - key: "target_platform"
    input_type: "string"
    requirement: "required"
    description: "Target embedded platform (e.g., 'thumbv7em-none-eabihf')"

instructions: |
  **Embedded Development Requirements:**
  - Target platform: {{ target_platform }}
  - No standard library (no_std)
  - Minimize binary size
  - Optimize for resource constraints
  - Verify memory usage at compile time
  - Include hardware abstraction layer (HAL)
```

## Quality Standards Customization

### Stricter Standards

```yaml
# SUCCESS CRITERIA

The workflow is complete when:
- Code compiles without warnings on stable AND nightly Rust
- All tests pass with 100% code coverage
- Zero clippy warnings (including pedantic lints)
- All public APIs have documentation examples
- Benchmarks included for performance-critical code
- CHANGELOG.md updated with changes
- Security audit passed
- Quality Engineer AND Security Engineer both approve
```

### Relaxed Standards (Prototyping)

```yaml
# SUCCESS CRITERIA (Prototype Mode)

The workflow is complete when:
- Code compiles (warnings acceptable for prototypes)
- Core functionality tests pass
- Basic error handling in place
- Quality Engineer approves OR max cycles reached
- README with usage examples exists
```

## Template Variations

### Create Multiple Recipe Variants

**rust-dev-team-strict.yaml** - High quality bar for production code
**rust-dev-team-prototype.yaml** - Fast iteration for prototypes
**rust-dev-team-web.yaml** - Web development focused
**rust-dev-team-embedded.yaml** - Embedded systems focused

Users can choose the variant that matches their needs:

```bash
goose run rust-dev-team-strict.yaml --param task="Production API"
goose run rust-dev-team-prototype.yaml --param task="Quick POC"
```

## Advanced: Sub-Recipes

If Goose supports sub-recipes in your version, you can modularize:

```yaml
sub_recipes:
  - name: "architect"
    path: "./personas/architect.yaml"
  - name: "developer"
    path: "./personas/developer.yaml"
  - name: "qa_engineer"
    path: "./personas/qa.yaml"

instructions: |
  1. Run architect sub-recipe with task
  2. Run developer sub-recipe with plan from architect
  3. Run qa_engineer sub-recipe with implementation
  # ...
```

## Testing Your Customizations

1. **Validate YAML syntax:**
   ```bash
   yamllint rust-development-team.yaml
   ```

2. **Test with a simple task:**
   ```bash
   goose run rust-development-team.yaml \
     --param task="Create a simple calculator function" \
     --param max_review_cycles=2
   ```

3. **Verify all parameters work:**
   ```bash
   goose run rust-development-team.yaml \
     --param task="Test task" \
     --param requirements="Test requirements" \
     --param max_review_cycles=1
   ```

## Best Practices

1. **Version your customizations** - Use git tags or version field
2. **Document changes** - Keep CHANGELOG.md updated
3. **Test incrementally** - Test each modification before adding more
4. **Keep focused** - Don't try to solve every use case in one recipe
5. **Share variants** - Create separate recipes for different domains
6. **Stay updated** - Check Goose documentation for new features

## Contributing Customizations

If you create useful customizations, consider:
1. Opening a PR to add variant recipes
2. Documenting your use case
3. Sharing examples of outputs
4. Adding to the examples directory

## Questions?

If you have questions about customization:
1. Check the Goose recipe reference: https://block.github.io/goose/docs/guides/recipes/recipe-reference
2. Review example recipes in the Goose repository
3. Open an issue in this repository with your use case
