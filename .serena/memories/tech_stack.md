# Tech Stack

## Primary Languages
- **Python 3.9+** - Main language for API examples
- **MATLAB** (R2016b or later) - Alternative API examples
- **HDL/VHDL** - For MCC (Multi-instrument Cloud Compile) examples

## Core Dependencies

### Python
- `moku` (>=3.0.0) - Main Moku API library
- `matplotlib` - For plotting examples
- `numpy` - Data manipulation
- `jupyter` / `ipykernel` - For notebook examples
- Neural network specific:
  - `tensorflow` / `keras` - For neural network examples
  - `opencv-python` - Computer vision in some examples

### Development Tools
- **uv** - Fast Python package manager (recommended)
- **ruff** - Linting and formatting
- **mypy** - Type checking
- **pre-commit** - Git hooks for code quality
- **git** - Version control

## File Types
- `.py` - Python scripts
- `.ipynb` - Jupyter notebooks
- `.m` - MATLAB scripts
- `.md` - Documentation
- `.linn` - Liquid Instruments Neural Network files (generated, gitignored)
- `.keras`, `.h5`, `.onnx` - Neural network model files (gitignored)

## Package Management
- `pyproject.toml` - Python project configuration (PEP 518)
- `uv.lock` - Locked dependencies for reproducible builds (if using uv)
- No `requirements.txt` - Uses modern pyproject.toml approach

## Platform Support
- Cross-platform (macOS, Linux, Windows)
- Current development environment: macOS (Darwin)
