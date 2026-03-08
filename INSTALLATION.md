# Installation and Setup

## Prerequisites

1. **Install Goose**

   Follow the official Goose installation guide at https://block.github.io/goose/

   ```bash
   # macOS/Linux
   curl -fsSL https://raw.githubusercontent.com/block/goose/main/install.sh | sh

   # Or with Homebrew
   brew install block/tap/goose
   ```

2. **Verify Installation**

   ```bash
   goose --version
   ```

## Installing the Recipe

### Option 1: Clone this Repository

```bash
git clone <repository-url> rust-development-team
cd rust-development-team
```

### Option 2: Download Just the Recipe

```bash
mkdir -p ~/.goose/recipes
cd ~/.goose/recipes
curl -O https://raw.githubusercontent.com/<user>/<repo>/main/rust-development-team.yaml
```

### Option 3: Manual Installation

1. Create a directory for the recipe
2. Copy `rust-development-team.yaml` to that directory
3. Note the full path to the YAML file

## Using the Recipe

### Local File Path

```bash
goose run /path/to/rust-development-team.yaml \
  --param task="Your Rust development task here"
```

### If Installed in Goose Recipes Directory

```bash
goose run rust-development-team \
  --param task="Your Rust development task here"
```

## Configuration

### Environment Variables

The recipe uses Goose's default configuration. You can customize the model and provider:

```bash
export GOOSE_PROVIDER=anthropic
export GOOSE_MODEL=claude-opus-4

goose run rust-development-team.yaml \
  --param task="Build a web server"
```

### Goose Configuration File

Create `~/.config/goose/config.yaml`:

```yaml
provider: anthropic
model: claude-opus-4
temperature: 0.7
```

## Recommended Setup

### For Best Results

1. **Use a capable model**: Claude Opus or Sonnet 4+ recommended for complex tasks
2. **Set adequate context**: Larger context windows help maintain state across phases
3. **Clear task descriptions**: Be specific in your task parameter

### Project Integration

To use this recipe within an existing Rust project:

```bash
# Navigate to your Rust project
cd /path/to/your/rust/project

# Run the recipe with context
goose run rust-development-team.yaml \
  --param task="Add new feature X" \
  --param requirements="Integrate with existing module Y, follow project conventions in CONTRIBUTING.md"
```

## Verification

Test the installation:

```bash
goose run rust-development-team.yaml \
  --param task="Create a simple Hello World library" \
  --param max_review_cycles=3
```

You should see the workflow phases execute:
1. PHASE 1: ARCHITECTURE & PLANNING
2. PHASE 2: IMPLEMENTATION
3. PHASE 3: QUALITY ASSURANCE
4. PHASE 4: REFINEMENT (if needed)
5. PHASE 5: RE-REVIEW (if needed)

## Troubleshooting

### Recipe Not Found

**Error**: `recipe not found: rust-development-team`

**Solution**: Use the full path to the YAML file:
```bash
goose run ./rust-development-team.yaml --param task="..."
```

### Parameter Errors

**Error**: `required parameter 'task' not provided`

**Solution**: Always provide the required `task` parameter:
```bash
goose run rust-development-team.yaml --param task="Your task description"
```

### Model Access Issues

**Error**: API authentication failures

**Solution**: Ensure your API keys are set:
```bash
export ANTHROPIC_API_KEY=your_key_here
# or
export OPENAI_API_KEY=your_key_here
```

### Session Times Out

**Issue**: Recipe doesn't complete for large tasks

**Solution**: Break down the task into smaller components or increase `max_turns` in the recipe settings.

## Next Steps

- Read [README.md](README.md) for usage examples
- Check [EXAMPLE_SESSION.md](examples/EXAMPLE_SESSION.md) for a walkthrough
- See [CUSTOMIZATION.md](CUSTOMIZATION.md) to modify the recipe for your needs

## Support

For Goose-related issues:
- Documentation: https://block.github.io/goose/
- Issues: https://github.com/block/goose/issues

For recipe-specific issues:
- File an issue in this repository
