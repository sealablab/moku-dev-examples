# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **examples repository** for Liquid Instruments Moku devices - hardware instruments for signal measurement, generation, data acquisition, and analysis. The code is educational and demonstrative, designed to be copied and modified by users for their own hardware setups.

**Critical**: No example works out-of-the-box. Users must configure device IP addresses (e.g., `'192.168.###.###'`) and hardware-specific parameters before running any example.

## Common Commands

### Development Setup
```bash
# Install dependencies (recommended: uses uv for fast package management)
uv sync --all-extras

# Alternative: using pip
pip install moku
pip install 'moku[neuralnetwork]'  # For neural network examples
pip install -e ".[dev]"  # Dev dependencies

# Install and set up pre-commit hooks (REQUIRED for contributions)
uv tool install pre-commit
pre-commit install
```

### Code Quality
```bash
# Run all pre-commit checks (linting, formatting, type checking)
pre-commit run --all-files

# Manual linting and formatting with ruff
uvx ruff check --fix python-api/ neural-network/
uvx ruff format python-api/ neural-network/

# Type checking with mypy
mypy --no-install-types --explicit-package-bases python-api/ neural-network/
```

### Running Examples
```bash
# Python scripts (remember to configure IP first!)
python python-api/oscilloscope_basic.py
python python-api/oscilloscope_plotting.py

# With uv
uv run python python-api/oscilloscope_basic.py

# Jupyter notebooks
jupyter notebook python-api/hello_moku.ipynb
jupyter notebook neural-network/Simple_sine.ipynb
```

## Code Architecture

### Directory Organization

- **`python-api/`** - Python scripts and notebooks organized by instrument type
  - Examples: `oscilloscope_*.py`, `datalogger_*.py`, `waveformgenerator_*.py`
  - Multi-instrument mode: `mim_*.py` (multiple instruments simultaneously)
  - Cloud Compile: `cloud_compile_*.py`

- **`matlab-api/`** - MATLAB implementations mirroring Python examples

- **`neural-network/`** - Neural Network instrument examples
  - Notebooks for classification, autoencoders, signal processing
  - Support utilities: `utils_anomaly_detection.py`, `emitter_simulator.py`
  - Pre-trained models in `models/` (gitignored: `.linn`, `.keras`, `.h5`, `.onnx`)

- **`mcc/`** - Multi-instrument Cloud Compile (HDL-based)
  - Organized by complexity: `Basic/`, `Moderate/`, `Advanced/`, `Template/`

- **`other-language-api/`** - Examples in other programming languages

### Instrument Categories

The Moku API supports these instrument types:
- Oscilloscope, Waveform Generator, Data Logger, Spectrum Analyzer
- Lock-in Amplifier, Phasemeter, PID Controller, Logic Analyzer
- FIR Filter, Laser Lock Box, Frequency Response Analyzer
- Time-Frequency Analyzer, Digital Filter Box, Power Supply
- Neural Network (deep learning-based signal processing)

### Core Design Patterns

#### Pattern 1: Basic Instrument Usage
```python
from moku.instruments import Oscilloscope

MOKU_IP = '192.168.###.###'  # Update with your device IP

try:
    # Connect with force_connect=True to overtake existing connections
    osc = Oscilloscope(MOKU_IP, force_connect=True)

    # Configure instrument
    osc.set_timebase(-5e-6, 5e-6)
    osc.set_trigger(type='Edge', source='Input1', level=0)

    # Acquire data
    data = osc.get_data()

finally:
    # ALWAYS clean up - critical for hardware resource management
    osc.relinquish_ownership()
```

**Key principles:**
- Configuration constants at top (MOKU_IP, SAMPLE_RATE, etc.)
- `force_connect=True` to take over existing connections
- Try/finally pattern for guaranteed cleanup
- Always call `relinquish_ownership()` in finally block

#### Pattern 2: Multi-Instrument Mode (MIM)
```python
from moku.instruments import MultiInstrument

try:
    m = MultiInstrument(MOKU_IP, force_connect=True)

    # Set up multiple instruments in slots
    connections = [
        dict(instrument='WaveformGenerator', slot=1),
        dict(instrument='Oscilloscope', slot=2)
    ]

    instruments = m.set_instruments(connections)
    wg, osc = instruments[0], instruments[1]

    # Configure each independently
    wg.generate_waveform(channel=1, type='Sine', amplitude=1, frequency=1e6)
    osc.set_timebase(-1e-3, 1e-3)

    # Use instruments
    data = osc.get_data()

finally:
    # Single cleanup for all instruments
    m.relinquish_ownership()
```

#### Pattern 3: Real-time Plotting
```python
import matplotlib.pyplot as plt

# Configure interactive plotting
plt.ion()
plt.show()
plt.grid(visible=True)

# Initial setup with first data frame
data = osc.get_data()
plt.xlim([data['time'][0], data['time'][-1]])
line1, = plt.plot([])

# Update loop
try:
    while True:
        data = osc.get_data()
        line1.set_data(data['time'], data['ch1'])
        plt.pause(0.1)
except KeyboardInterrupt:
    pass  # Graceful exit on Ctrl+C
finally:
    osc.relinquish_ownership()
```

#### Pattern 4: Data Streaming
```python
from moku.instruments import DataLogger

try:
    dl = DataLogger(MOKU_IP, force_connect=True)

    # Configure before streaming
    dl.set_acquisition_mode(mode='Precision')
    dl.set_samplerate(rate='1MSa/s')

    # Start streaming
    dl.start_streaming(duration=10)

    # Process data as it arrives
    while not dl.is_streaming_complete():
        samples = dl.get_stream_data()
        if samples:
            process_data(samples)

    # Get remaining data
    remaining = dl.get_stream_data()

finally:
    dl.stop_streaming()
    dl.relinquish_ownership()
```

### Jupyter Notebook Structure

Notebooks follow this pattern:
1. **Markdown header** - Title and description
2. **Setup cell** - Imports and configuration
3. **Markdown sections** - Explaining each step
4. **Code cells** - Focused, digestible code blocks
5. **Inline outputs** - Visualizations and data
6. **Cleanup cell** - `relinquish_ownership()`

### Configuration Management

Examples use configuration constants at the top:
```python
# Configuration - Update these for your setup
MOKU_IP = '192.168.###.###'  # Your device IP
SAMPLE_RATE = '1MSa/s'
TIMEBASE_START = -1e-3  # -1ms
TIMEBASE_END = 1e-3     # +1ms
WAVE_AMPLITUDE = 1.0    # Volts
WAVE_FREQUENCY = 1e6    # 1 MHz
```

**Never commit real IP addresses** - use placeholders like `'192.168.###.###'` or generic examples like `'192.168.1.100'`.

## Code Quality Standards

### Ruff Configuration (pyproject.toml)
- Line length: 99 characters
- Target: Python 3.9+
- Rules: pycodestyle (E, W), pyflakes (F), isort (I)
- Lenient: Allows unused variables (F841) in examples, lambda assignments (E731)
- Quote style: preserved (both single and double quotes allowed)

### Type Checking (mypy)
- Python 3.9+ required
- Lenient for examples: `ignore_missing_imports = true`
- No strict untyped definitions required

### Pre-commit Hooks
Three checks run automatically on commit:
1. **ruff-check** - Linting with auto-fixes
2. **ruff-format** - Code formatting
3. **mypy** - Type checking

All use `uvx` for isolated tool execution (no global installation needed).

## Important Constraints

### Hardware-Specific Code
- All examples require physical Moku hardware
- No mocking or simulation layer exists
- Testing requires actual device connection

### Educational Focus
- Prioritize clarity over optimization
- Include explanatory comments for learners
- Code demonstrates best practices but allows flexibility
- Not production-ready - meant to be modified

### Resource Management Critical
- Hardware connections must be cleaned up
- Missing `relinquish_ownership()` can lock the device
- Always use try/finally pattern
- One cleanup call per connection (or MultiInstrument)

## File Naming Conventions

- **By instrument**: `{instrument}_{feature}.py` (e.g., `oscilloscope_plotting.py`)
- **By feature**: `*_basic.py`, `*_plotting.py`, `*_streaming.py`
- **Multi-instrument**: `mim_*.py` prefix
- **Notebooks**: `{Feature}_{Description}.ipynb` (e.g., `Simple_sine.ipynb`)
- **Lowercase with underscores** for all filenames

## Finding Code

### By Instrument Type
Use glob patterns: `*oscilloscope*`, `*waveformgenerator*`, `*datalogger*`

### By Feature
- Plotting examples: `*plotting*`
- Streaming examples: `*streaming*`
- Basic getting-started: `*basic*`
- Multi-instrument: `mim_*`

### By Language
- Python: `python-api/` (`.py` and `.ipynb`)
- MATLAB: `matlab-api/` (`.m` files)
- Neural networks: `neural-network/` (mostly notebooks)

## Key Implementation Details

### Exception Handling
```python
# ✅ Correct - specific exceptions with cleanup
try:
    data = osc.get_data()
except (ConnectionError, TimeoutError) as e:
    print(f"Failed to acquire data: {e}")
    raise
finally:
    osc.relinquish_ownership()

# ❌ Wrong - bare except, no cleanup
try:
    data = osc.get_data()
except:
    pass
```

### Signal Generation
```python
# Common waveform types
wg.generate_waveform(channel=1, type='Sine', amplitude=1.0, frequency=1e6, offset=0)
wg.generate_waveform(channel=2, type='Square', amplitude=2.0, frequency=100e3, duty=50)

# Modulation
wg.set_modulation(channel=1, type='AM', depth=50, frequency=1e3)
```

### Triggering
```python
# Edge trigger
osc.set_trigger(type='Edge', source='Input1', level=0.5, mode='Normal')

# Pulse trigger
osc.set_trigger(type='Pulse', source='Input2', level=0, mode='Auto')
```

### Data Acquisition
```python
# Get data frame
data = osc.get_data()
ch1_voltage = data['ch1']  # NumPy array
ch2_voltage = data['ch2']
time = data['time']

# Set timebase (trigger-centered window)
osc.set_timebase(t1=-1e-3, t2=1e-3)  # -1ms to +1ms
```

## Anti-Patterns to Avoid

1. **No cleanup** - Missing `relinquish_ownership()` locks the device
2. **Real IPs committed** - Always use placeholders
3. **Scattered magic numbers** - Use named constants at top
4. **Bare except clauses** - Use specific exception types
5. **Silent failures** - Always log or raise exceptions

## Development Workflow

1. **Make changes** - Follow existing code patterns
2. **Test manually** - Requires actual Moku device
3. **Run pre-commit** - `pre-commit run --all-files`
4. **Verify cleanup** - Ensure `relinquish_ownership()` called
5. **Check IP addresses** - Use placeholders only
6. **Create PR** - Include description and testing notes
