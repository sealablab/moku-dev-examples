# Task Completion Checklist

When completing a task (adding/modifying examples), follow this checklist:

## Before Committing

### 1. Code Quality Checks
Run pre-commit hooks to ensure code meets quality standards:
```bash
pre-commit run --all-files
```

This will automatically run:
- **ruff-check**: Linting with auto-fix
- **ruff-format**: Code formatting  
- **mypy**: Type checking

### 2. Manual Verification

#### For Python Scripts (.py)
- [ ] Script has proper header comment with description
- [ ] Copyright notice included: `(c) Liquid Instruments Pty. Ltd.`
- [ ] IP address uses placeholder: `'192.168.###.###'`
- [ ] Constants defined at top (MOKU_IP, etc.)
- [ ] Proper exception handling with try/except/finally
- [ ] `relinquish_ownership()` called in finally block
- [ ] Code is commented adequately for educational purposes
- [ ] Script runs without errors (if you have hardware)

#### For Jupyter Notebooks (.ipynb)
- [ ] First cell is markdown with description
- [ ] Sections organized with markdown headers
- [ ] Code cells have explanatory markdown before/after
- [ ] IP addresses are placeholders
- [ ] Outputs shown (or cleared if preferred for clean diffs)
- [ ] Notebook runs from top to bottom without errors (if you have hardware)

#### For MATLAB Scripts (.m)
- [ ] Header comment with description
- [ ] IP address placeholder used
- [ ] Comments explain key steps
- [ ] Follows MATLAB conventions

### 3. File Naming
- [ ] Filename is descriptive and follows pattern: `{instrument}_{feature}.{ext}`
- [ ] Lowercase with underscores
- [ ] No spaces or special characters

### 4. Documentation
- [ ] Added comments explaining non-obvious code
- [ ] Configuration parameters clearly marked
- [ ] Updated README.md if adding new category/type of example

## Git Workflow

### 5. Commit
```bash
# Stage changes
git add .

# Commit (pre-commit hooks will run automatically)
git commit -m "Descriptive message about changes"
```

If pre-commit hooks fail:
1. Review error messages
2. Fix issues (some are auto-fixed)
3. Stage changes again: `git add .`
4. Retry commit

### 6. Push
```bash
# Push to your fork
git push origin your-branch-name
```

## Pull Request Creation

### 7. PR Checklist
- [ ] PR title clearly describes the change
- [ ] PR description includes:
  - What the PR does
  - Which instruments/features are demonstrated
  - Testing performed (with/without hardware)
  - Screenshots or output examples (if applicable)
- [ ] All pre-commit checks pass
- [ ] Code follows repository conventions
- [ ] No real IP addresses committed
- [ ] No sensitive data or large files committed

## Common Gotchas

### Things NOT to Commit
- Real IP addresses of devices
- Large data files (*.npy, *.mat, *.csv, *.hdf5)
- Neural network model files (*.linn, *.keras, *.h5, *.onnx)
- Virtual environment directories (.venv/, venv/, env/)
- IDE configuration (.vscode/, .idea/)
- System files (.DS_Store, Thumbs.db)
- Cache directories (__pycache__/, .mypy_cache/, .ruff_cache/)

All of these are already in .gitignore, but double-check with `git status`.

### What Should Be Committed
- Example code files (.py, .ipynb, .m)
- Documentation (.md files)
- Configuration files (pyproject.toml, .pre-commit-config.yaml)
- Small example data files (if absolutely necessary and <100KB)

## Optional: Test on Clean Environment

For contributors wanting to be thorough:
```bash
# Create fresh virtual environment
python -m venv test_env
source test_env/bin/activate
pip install moku

# Run your example
python python-api/your_new_example.py

# Deactivate and remove
deactivate
rm -rf test_env
```

## Summary Command Sequence

Quick reference for completing a task:
```bash
# 1. Run code quality checks
pre-commit run --all-files

# 2. Stage changes
git add .

# 3. Commit (hooks run automatically)
git commit -m "Add example for feature X"

# 4. Push
git push origin your-branch-name

# 5. Create PR on GitHub
```
