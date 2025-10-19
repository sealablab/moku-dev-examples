# Code Style and Conventions

## General Philosophy
- **Clarity over cleverness** - Examples should be easy to understand
- **Educational focus** - Code is meant to teach, not be production-ready
- **Consistent but lenient** - Maintain quality without being overly strict for examples

## Python Style

### Line Length and Formatting
- **Line length**: 99 characters (configured in pyproject.toml)
- **Target version**: Python 3.9+
- **Quote style**: Preserve existing (allows both single and double quotes)
- **Formatter**: ruff format

### Import Organization
- Use `isort` rules via ruff
- Order: standard library, third-party, first-party (moku)
- Combine imports from same module
- No unused imports (except in `__init__.py`)

### Linting Rules
Enabled checks (ruff):
- **E** - pycodestyle errors
- **W** - pycodestyle warnings  
- **F** - pyflakes (undefined names, unused imports, etc.)
- **I** - isort (import sorting)

Disabled for examples:
- **E501** - Line too long (handled by formatter)
- **E731** - Lambda assignment (useful for register calculations)
- **F841** - Unused variables (common in examples)

### Naming Conventions
- Variables: `snake_case`
- Constants: `UPPER_SNAKE_CASE` (e.g., `MOKU_IP`, `SAMPLE_RATE`)
- Functions: `snake_case`
- Classes: `PascalCase` (rare in examples)
- Avoid ambiguous names like `O` (looks like 0) or `l` (looks like 1)

### Type Hints
- **Not required** for examples
- `mypy` runs but with lenient settings:
  - `disallow_untyped_defs = false`
  - `ignore_missing_imports = true`
  - Shows error codes for educational purposes

### File Structure (Python Scripts)
```python
#
# moku example: Brief Description
#
# Longer description of what the example demonstrates.
#
# (c) Liquid Instruments Pty. Ltd.
#

from moku.instruments import SomeInstrument

# Configuration constants at top
MOKU_IP = '192.168.###.###'  # Placeholder IP
SAMPLE_RATE = '1MSa/s'

try:
    # Main code here
    instrument = SomeInstrument(MOKU_IP, force_connect=True)
    
    # Configure and use instrument
    
except Exception as e:
    instrument.relinquish_ownership()
    raise e
finally:
    # Always close connection
    instrument.relinquish_ownership()
```

### Error Handling
- Use `try/except/finally` blocks
- **Always** call `relinquish_ownership()` in finally block
- Use specific exception types (avoid bare `except:`)
- Re-raise exceptions after cleanup

### IP Address Handling
- **Never commit real IP addresses**
- Use placeholders: `'192.168.###.###'` or `'192.168.1.100'`
- Always include comment: `# Update with your device IP`

### Comments
- Inline comments for non-obvious operations
- Docstrings not required (examples are self-documenting)
- Explain configuration parameters clearly

## Jupyter Notebooks

### Structure
1. Title markdown cell with description
2. Markdown headers for sections
3. Code cells with explanatory text before/after
4. Include visualization outputs
5. Clear explanations between code blocks

### Best Practices
- Can clear outputs before committing (keeps diffs clean)
- Use markdown formatting liberally
- Show example outputs/plots
- Explain what each section does

## File Naming
- Use descriptive, lowercase names with underscores
- Pattern: `{instrument}_{feature}.py` or `{instrument}_{feature}.ipynb`
- Examples:
  - `oscilloscope_plotting.py`
  - `waveformgenerator_modulation.py`
  - `neural_network_pid_control.ipynb`

## MATLAB Style
- Standard MATLAB conventions
- Similar structure to Python examples
- Clear comments and configuration sections
- Files named with underscores (MATLAB convention)

## Common Anti-Patterns to Avoid

### ❌ Bad
```python
# Bare except
try:
    data = device.get_data()
except:
    data = None

# Explicit boolean comparison
while status == False:
    do_something()

# List for axis limits
ax.set_xlim([0, 10])

# Ambiguous variable names
O = np.pi / np.array([o for o in range(8, 64)])
```

### ✅ Good
```python
# Specific exceptions
try:
    data = device.get_data()
except (KeyError, IndexError):
    data = None

# Pythonic boolean check
while not status:
    do_something()

# Tuple for axis limits
ax.set_xlim((0, 10))

# Clear variable names
omega = np.pi / np.array([o for o in range(8, 64)])
```
