# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Project Structure

- This repository contains examples for Moku devices across multiple languages:
  - `python-api/`: Python scripts and Jupyter notebooks
  - `matlab-api/`: MATLAB scripts
  - `neural-network/`: Deep learning examples using Moku Neural Network
  - `mcc/`: Multi-instrument Cloud Compile examples (VHDL/Verilog)
  - `other-language-api/`: Examples in other languages

## Critical Patterns

- All examples require IP address configuration before running
- Always use placeholder IPs (`'192.168.###.###'`) in committed code
- Always use `try/finally` blocks with `relinquish_ownership()` in finally block
- For neural network examples, input/output values must be in range [-1, 1]

## Development Commands

- Install dependencies: `uv sync --all-extras` (preferred) or `pip install -e ".[dev]"`
- Run linting: `pre-commit run --all-files`
- Run specific example: `python python-api/oscilloscope_basic.py`
- Run notebook: `jupyter notebook neural-network/Simple_sine.ipynb`

## Code Style

- Line length: 99 characters
- Linting: ruff (E, W, F, I rules with specific exceptions)
- Formatting: ruff format (preserves quote style)
- Type checking: mypy (lenient for examples)
- Unused variables allowed in examples