# Design Patterns and Guidelines

## Core Principles

### 1. Educational Focus
- Examples are meant to **teach**, not be production-ready
- Prioritize **clarity** and **readability** over optimization
- Include **explanatory comments** for non-obvious operations
- Show **best practices** but allow flexibility for learning

### 2. Hardware Configuration Required
- **No example works out-of-the-box** - users must configure their device
- Always use **placeholder IP addresses**: `'192.168.###.###'`
- Clearly mark **configuration sections** at the top of files
- Include **comments** indicating what needs to be changed

### 3. Resource Management
- **Always clean up** hardware connections
- Use `try/except/finally` pattern
- Call `relinquish_ownership()` in finally block
- Handle exceptions gracefully

## Common Design Patterns

### Pattern 1: Basic Instrument Usage

```python
from moku.instruments import InstrumentName

# Configuration
MOKU_IP = '192.168.###.###'  # Update with your device IP

try:
    # Connect
    instrument = InstrumentName(MOKU_IP, force_connect=True)
    
    # Configure
    instrument.configure_something(parameters)
    
    # Use instrument
    data = instrument.get_data()
    print(data)
    
except Exception as e:
    instrument.relinquish_ownership()
    raise e
finally:
    # Always close connection
    instrument.relinquish_ownership()
```

**Key points:**
- Configuration constants at top
- `force_connect=True` to overtake existing connections
- Exception handling with cleanup
- Always call `relinquish_ownership()`

### Pattern 2: Plotting/Visualization

```python
import matplotlib.pyplot as plt
from moku.instruments import Oscilloscope

MOKU_IP = '192.168.###.###'
i = Oscilloscope(MOKU_IP, force_connect=True)

try:
    # Configure instrument
    i.set_timebase(-5e-6, 5e-6)
    i.set_trigger(type='Edge', source='Input1', level=0)
    
    # Get initial data for plot setup
    data = i.get_data()
    
    # Set up plotting
    plt.ion()
    plt.show()
    plt.grid(visible=True)
    plt.ylim([-1, 1])
    plt.xlim([data['time'][0], data['time'][-1]])
    
    line1, = plt.plot([])
    line2, = plt.plot([])
    
    # Real-time update loop
    while True:
        data = i.get_data()
        line1.set_data(data['time'], data['ch1'])
        line2.set_data(data['time'], data['ch2'])
        plt.pause(0.1)
        
except KeyboardInterrupt:
    pass
finally:
    i.relinquish_ownership()
```

**Key points:**
- Interactive mode: `plt.ion()`
- Initial data fetch to set up axes
- Update loop with `plt.pause()`
- Graceful exit on Ctrl+C with KeyboardInterrupt

### Pattern 3: Data Streaming

```python
from moku.instruments import DataLogger

MOKU_IP = '192.168.###.###'
DURATION = 10  # seconds

try:
    dl = DataLogger(MOKU_IP, force_connect=True)
    
    # Configure for streaming
    dl.set_acquisition_mode(mode='Precision')
    dl.set_samplerate(rate='1MSa/s')
    
    # Start streaming
    dl.start_streaming(duration=DURATION)
    
    # Process data as it arrives
    while not dl.is_streaming_complete():
        samples = dl.get_stream_data()
        if samples:
            # Process samples
            process_data(samples)
            
    # Get any remaining data
    remaining = dl.get_stream_data()
    
finally:
    dl.stop_streaming()
    dl.relinquish_ownership()
```

**Key points:**
- Configure before starting stream
- Poll for completion
- Get remaining data after completion
- Stop streaming in finally block

### Pattern 4: Multi-Instrument Mode (MIM)

```python
from moku.instruments import MultiInstrument

MOKU_IP = '192.168.###.###'

try:
    # Connect with MultiInstrument
    m = MultiInstrument(MOKU_IP, force_connect=True)
    
    # Set up multiple instruments
    connections = [
        dict(instrument='WaveformGenerator', slot=1),
        dict(instrument='Oscilloscope', slot=2)
    ]
    
    instruments = m.set_instruments(connections)
    wg = instruments[0]
    osc = instruments[1]
    
    # Configure each instrument
    wg.generate_waveform(channel=1, type='Sine', 
                         amplitude=1, frequency=1e6)
    osc.set_timebase(-1e-3, 1e-3)
    
    # Use instruments
    data = osc.get_data()
    
finally:
    m.relinquish_ownership()
```

**Key points:**
- Use `MultiInstrument` base class
- Define instruments with slots
- Configure each instrument independently
- Single `relinquish_ownership()` for all

### Pattern 5: Jupyter Notebook Structure

```
Cell 1 (Markdown):
# Title: Example Name
Brief description of what this notebook demonstrates.

Cell 2 (Markdown):
## Setup and Configuration
Explanation of what needs to be configured.

Cell 3 (Code):
from moku.instruments import SomeInstrument
MOKU_IP = '192.168.###.###'  # Update with your device IP

Cell 4 (Markdown):
## Step 1: Connect to Device
Explanation of connection process.

Cell 5 (Code):
instrument = SomeInstrument(MOKU_IP, force_connect=True)
# Configuration code

Cell 6 (Markdown):
## Step 2: Acquire Data
Explanation of data acquisition.

Cell 7 (Code):
data = instrument.get_data()
# Visualization code

... (continue pattern)

Final Cell (Code):
instrument.relinquish_ownership()
```

**Key points:**
- Markdown cells explain each step
- Code cells are focused and digestible
- Outputs/plots shown inline
- Clear flow from setup to cleanup

## Common Scenarios

### Signal Generation
```python
# Sine wave
wg.generate_waveform(channel=1, type='Sine', 
                     amplitude=1.0, frequency=1e6, offset=0)

# Square wave
wg.generate_waveform(channel=2, type='Square',
                     amplitude=2.0, frequency=100e3,
                     duty=50, offset=0)

# Modulation
wg.set_modulation(channel=1, type='AM', 
                  depth=50, frequency=1e3)
```

### Triggering
```python
# Edge trigger
osc.set_trigger(type='Edge', source='Input1', 
                level=0.5, mode='Normal')

# Pulse trigger
osc.set_trigger(type='Pulse', source='Input2',
                level=0, mode='Auto')
```

### Data Acquisition
```python
# Single frame
data = osc.get_data()
ch1_voltage = data['ch1']
ch2_voltage = data['ch2']
time = data['time']

# Specify timebase
osc.set_timebase(t1=-1e-3, t2=1e-3)  # -1ms to +1ms
```

## Best Practices

### Error Handling
✅ **Do:**
```python
try:
    instrument = Oscilloscope(MOKU_IP, force_connect=True)
    # ... use instrument ...
except ConnectionError as e:
    print(f"Failed to connect: {e}")
    raise
finally:
    instrument.relinquish_ownership()
```

❌ **Don't:**
```python
try:
    instrument = Oscilloscope(MOKU_IP)
    # ... use instrument ...
except:  # Bare except
    pass  # Silent failure
```

### Configuration
✅ **Do:**
```python
# Configuration section at top
MOKU_IP = '192.168.###.###'  # Update with your device IP
SAMPLE_RATE = '1MSa/s'
AMPLITUDE = 1.0  # Volts
FREQUENCY = 1e6  # Hz
```

❌ **Don't:**
```python
# Magic numbers scattered throughout
instrument.configure(1.0, 1e6, '192.168.50.247')
```

### Comments
✅ **Do:**
```python
# Set timebase to ±5μs (trigger centered)
osc.set_timebase(-5e-6, 5e-6)

# Generate 1MHz sine wave, 1Vpp
wg.generate_waveform(1, 'Sine', amplitude=1, frequency=1e6)
```

❌ **Don't:**
```python
osc.set_timebase(-5e-6, 5e-6)  # set timebase
wg.generate_waveform(1, 'Sine', amplitude=1, frequency=1e6)  # generate
```

### Import Organization
✅ **Do:**
```python
import matplotlib.pyplot as plt
import numpy as np

from moku.instruments import Oscilloscope
```

❌ **Don't:**
```python
from moku.instruments import *
import matplotlib.pyplot as plt, numpy as np
```

## Anti-Patterns to Avoid

### 1. Not Cleaning Up
❌ **Wrong:**
```python
instrument = Oscilloscope(MOKU_IP, force_connect=True)
data = instrument.get_data()
# Script ends without relinquish_ownership()
```

### 2. Real IP Addresses
❌ **Wrong:**
```python
MOKU_IP = '192.168.50.247'  # Your actual IP
```

✅ **Right:**
```python
MOKU_IP = '192.168.###.###'  # Update with your device IP
```

### 3. Hardcoded Configuration
❌ **Wrong:**
```python
# Scattered magic numbers
i.set_timebase(-0.001, 0.001)
i.set_frontend(1, coupling='DC', impedance='1MOhm', attenuation='0dB')
i.generate_waveform(1, 'Sine', amplitude=0.5, frequency=1000000)
```

✅ **Right:**
```python
# Configuration constants
TIMEBASE_START = -1e-3  # -1ms
TIMEBASE_END = 1e-3     # +1ms
WAVE_AMPLITUDE = 0.5    # Volts
WAVE_FREQUENCY = 1e6    # 1 MHz

i.set_timebase(TIMEBASE_START, TIMEBASE_END)
i.generate_waveform(1, 'Sine', 
                    amplitude=WAVE_AMPLITUDE, 
                    frequency=WAVE_FREQUENCY)
```

## Guidelines Summary

1. **Always clean up** - Use try/finally with relinquish_ownership()
2. **Configure at top** - Constants and configuration clearly marked
3. **Comment clearly** - Explain what and why, especially for learners
4. **Use placeholders** - Never commit real IP addresses
5. **Handle errors** - Specific exceptions, meaningful error messages
6. **Be educational** - Code should teach, not just demonstrate
7. **Follow patterns** - Use established patterns from existing examples
8. **Test manually** - Verify examples work (or explain limitations)
