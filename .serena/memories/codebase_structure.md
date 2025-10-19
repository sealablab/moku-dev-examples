# Codebase Structure

## Top-Level Organization

```
moku-dev-examples/
├── python-api/              # Python API examples
├── matlab-api/              # MATLAB API examples  
├── neural-network/          # Neural network instrument examples
├── other-language-api/      # Examples in other languages
├── mcc/                     # Multi-instrument Cloud Compile examples
├── .serena/                 # Serena agent memory files
├── .git/                    # Git repository metadata
├── README.md                # Main project documentation
├── CONTRIBUTING.md          # Contributor guidelines
├── LICENSE                  # MIT License
├── pyproject.toml           # Python project configuration
├── .pre-commit-config.yaml  # Pre-commit hook configuration
└── .gitignore               # Git ignore patterns
```

## Python API Directory (`python-api/`)

Contains Python scripts and Jupyter notebooks organized by instrument type:

### Instrument Categories
- **Oscilloscope**: `oscilloscope_*.py`
  - Basic usage, plotting, streaming, deep memory mode, averaging
- **Waveform Generator**: `waveformgenerator_*.py`, `arbitrarywavegen_*.py`
  - Signal generation, modulation, triggering
- **Data Logger**: `datalogger_*.py`
  - Data logging, streaming
- **Spectrum Analyzer**: `spectrumanalyzer_*.py`
  - Basic spectrum analysis, plotting
- **Lock-in Amplifier**: `lock_in_amplifier_*.py`
  - Basic operation, plotting, streaming
- **Phasemeter**: `phasemeter_*.py`
  - Basic measurements, streaming, plotting, digital PLL
- **PID Controller**: `pidcontroller_*.py`
  - Basic control, plotting, transfer function analysis
- **Logic Analyzer**: `logic_analyzer_*.py`
  - Plotting, distance measurement
- **FIR Filter**: `fir_*.py`
  - Digital filtering
- **Laser Lock Box**: `laser_lock_box_*.py`
  - Laser stabilization
- **Frequency Response Analyzer**: `freq_response_analyzer_*.py`
  - Basic analysis, plotting
- **Time-Frequency Analyzer**: `timefrequencyanalyzer_*.py`
  - Basic analysis, plotting, Allan deviation
- **Digital Filter Box**: `digital_filter_box_*.py`
  - Filter plotting and configuration
- **Power Supply**: `power_supply_*.py`
  - Basic power supply control
- **Neural Network**: `neuralnetwork_*.py`
  - Simple neural network examples

### Multi-Instrument Mode (MIM)
Files prefixed with `mim_*`:
- `mim_wg_osc.py` - Waveform Generator + Oscilloscope
- `mim_wg_sa.py` - Waveform Generator + Spectrum Analyzer  
- `mim_dl_lia_streaming.py` - Data Logger + Lock-in Amplifier
- `mim_fir_fra.ipynb` - FIR Filter + Frequency Response Analyzer

### Cloud Compile Examples
- `cloud_compile_*.py` - Examples using Cloud Compile feature

### Special Subdirectories
- `python-api/microvolt_signal_measurement_example/` - Specific example project
- `python-api/dfrt_python_example/` - DFRT-specific example

### Getting Started
- `hello_moku.ipynb` - Beginner-friendly Jupyter notebook

## MATLAB API Directory (`matlab-api/`)

Mirrors Python examples but in MATLAB:
- Same instrument coverage as Python
- MATLAB script files (`.m`)
- Similar naming convention: `{instrument}_{feature}.m`
- Examples: `oscilloscope_basic.m`, `waveform_generator_modulation.m`

## Neural Network Directory (`neural-network/`)

Advanced examples using Moku Neural Network instrument:

### Example Types
- `Simple_sine.ipynb` - Getting started with neural networks
- `Classification.ipynb` - Signal classification
- `Autoencoder.ipynb` - Signal compression/reconstruction
- `Anomaly_detection.ipynb` - Detecting anomalies in signals
- `Signal_ID.ipynb` - Signal identification
- `Sum.ipynb` - Neural network arithmetic
- `Identity_NN.ipynb` - Identity function learning
- `Emitter_control.ipynb` - Emitter control applications
- `Quadrant_Photodiode.py` - Photodiode applications

### Support Files
- `utils_anomaly_detection.py` - Utilities for anomaly detection
- `emitter_simulator.py` - Emitter simulation tools  
- `nonlinear_actuator.py` - Actuator modeling

### Subdirectories
- `neural-network/models/` - Pre-trained models
- `neural-network/PulsedRADAR/` - Pulsed radar examples
- `neural-network/Screenshots/` - Documentation images

### Model Files (gitignored)
- `.linn` - Liquid Instruments Neural Network format
- `.keras`, `.h5` - Keras/TensorFlow models
- `.onnx` - ONNX format models

## MCC Directory (`mcc/`)

Multi-instrument Cloud Compile examples using HDL:

### Organization by Complexity
- `mcc/Basic/` - Simple examples
- `mcc/Moderate/` - Intermediate examples
- `mcc/Advanced/` - Complex examples
- `mcc/Template/` - Starting templates
- `mcc/IP Core Templates and Examples/` - IP core resources
- `mcc/HDLCoder/` - HDL Coder examples

## Other Language API (`other-language-api/`)

Examples in programming languages other than Python and MATLAB (scope varies).

## Configuration Files

### `pyproject.toml`
- Project metadata and dependencies
- Ruff configuration (linting and formatting)
- mypy configuration (type checking)
- Python version requirement: >=3.9

### `.pre-commit-config.yaml`
- Pre-commit hook definitions:
  1. ruff-check (linting)
  2. ruff-format (formatting)
  3. mypy (type checking)
- Runs using `uvx` for isolated tool execution

### `.gitignore`
Ignores:
- Python artifacts (__pycache__, *.pyc)
- Virtual environments (.venv, venv, env)
- IDE files (.vscode, .idea)
- Jupyter checkpoints (.ipynb_checkpoints)
- Neural network files (*.linn, *.keras, *.h5)
- Data files (*.npy, *.mat, *.csv)
- Build artifacts
- System files (.DS_Store, Thumbs.db)

## File Patterns and Naming

### Examples by Feature
- `*_basic.py` - Simple getting-started examples
- `*_plotting.py` - Examples with visualization
- `*_streaming.py` - Real-time data streaming
- `*_deep_memory_mode.py` - Deep memory features
- `*_averaging.py` - Signal averaging
- `*_modulation.py` - Signal modulation
- `*_triggered.py` - Triggered acquisition

### File Types by Language
- `.py` - Python scripts
- `.ipynb` - Jupyter notebooks (Python)
- `.m` - MATLAB scripts
- `.md` - Markdown documentation

## Key Directories to Know

### When Adding Examples
- **Python scripts**: Add to `python-api/`
- **Python notebooks**: Add to `python-api/` or `neural-network/`
- **MATLAB scripts**: Add to `matlab-api/`
- **HDL/Cloud Compile**: Add to appropriate `mcc/` subdirectory

### When Looking for Examples
- **By language**: Check `python-api/` or `matlab-api/`
- **By instrument**: Use glob patterns like `*oscilloscope*` or `*waveformgenerator*`
- **By feature**: Use patterns like `*plotting*`, `*streaming*`, `*basic*`
- **Multi-instrument**: Look for `mim_*` prefix
- **Neural networks**: Check `neural-network/` directory
