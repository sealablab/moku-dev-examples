# Suggested Commands

## Development Environment Setup

### Using uv (Recommended)
```bash
# Install dependencies
uv sync --all-extras

# Install with specific extras
uv sync --extra dev
uv sync --extra neuralnetwork

# Activate virtual environment
source .venv/bin/activate  # macOS/Linux
.venv\Scripts\activate     # Windows

# Run commands without activating
uv run python python-api/oscilloscope_basic.py
uv run pre-commit run --all-files
```

### Using pip (Alternative)
```bash
# Install main dependencies
pip install moku

# Install with neural network support
pip install 'moku[neuralnetwork]'

# Install dev dependencies
pip install -e ".[dev]"
```

## Code Quality

### Pre-commit Hooks
```bash
# Install pre-commit
uv tool install pre-commit
# OR
pip install pre-commit

# Set up hooks
pre-commit install

# Run on all files
pre-commit run --all-files

# Run on specific files
pre-commit run --files python-api/oscilloscope_basic.py
```

### Manual Linting and Formatting
```bash
# Using uvx (recommended - doesn't require installation)
uvx ruff check --fix python-api/ neural-network/
uvx ruff format python-api/ neural-network/

# Or install ruff globally
uv tool install ruff
ruff check --fix python-api/ neural-network/
ruff format python-api/ neural-network/

# Type checking
mypy --no-install-types --explicit-package-bases python-api/ neural-network/
```

## Running Examples

### Python Scripts
```bash
# Basic run
python python-api/oscilloscope_basic.py
python python-api/oscilloscope_plotting.py

# With uv
uv run python python-api/oscilloscope_basic.py
```

### Jupyter Notebooks
```bash
# Start Jupyter
jupyter notebook python-api/hello_moku.ipynb
jupyter notebook neural-network/Simple_sine.ipynb

# Execute notebook from command line
jupyter nbconvert --to notebook --execute your_notebook.ipynb
```

### MATLAB Examples
```matlab
% In MATLAB console
run('matlab-api/oscilloscope_basic.m')
```

## Git Workflow

### Branch Management
```bash
# Update local main
git checkout main
git pull upstream main

# Create feature branch
git checkout -b feature/your-feature-name
git checkout -b fix/bug-description
git checkout -b docs/documentation-update

# Stage and commit (pre-commit hooks run automatically)
git add .
git commit -m "Add oscilloscope FFT example"

# Push to your fork
git push origin feature/your-feature-name
```

### Checking Status
```bash
# Current status
git status

# Recent commits
git log --oneline -10

# View changes
git diff
git diff --staged
```

## System Utils (macOS/Darwin)

### File Operations
```bash
# List files
ls -la
ls python-api/

# Find files
find . -name "*.py"
find . -name "*oscilloscope*"

# Search in files
grep -r "MOKU_IP" python-api/
grep -r "Oscilloscope" --include="*.py"
```

### Directory Navigation
```bash
# Change directory
cd python-api/
cd neural-network/
cd ..

# Current directory
pwd
```

### File Viewing
```bash
# View file contents
cat python-api/oscilloscope_basic.py
head -20 python-api/oscilloscope_plotting.py
tail -10 README.md

# Count lines
wc -l python-api/*.py
```

## Testing Examples (Manual)

Since this is an example repository, there are no automated unit tests. Testing is manual:

1. **Connect to Moku device** on your network
2. **Edit example** to update IP address
3. **Run the example** and verify output
4. **Check for errors** in console
5. **Verify data/plots** are as expected

## Troubleshooting

### Pre-commit Issues
```bash
# Skip hooks temporarily (not recommended)
git commit --no-verify -m "message"

# Update pre-commit hooks
pre-commit autoupdate

# Clear pre-commit cache
pre-commit clean
```

### Virtual Environment Issues
```bash
# Remove and recreate with uv
rm -rf .venv
uv sync --all-extras

# Or with venv/pip
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install moku
```

### mypy Issues
```bash
# Clear mypy cache
rm -rf .mypy_cache

# Run with verbose output
mypy --verbose python-api/
```
