# Project Architecture Rules (Non-Obvious Only)

- Repository is organized by language/platform, not by instrument type
- All examples follow consistent error handling pattern with try/finally
- Neural network examples require TensorFlow/Keras for model creation
- MCC examples demonstrate FPGA programming for Moku devices
- Multi-Instrument Mode (MIM) examples show how to use multiple instruments simultaneously
- Examples are standalone and don't share code between files (for educational clarity)