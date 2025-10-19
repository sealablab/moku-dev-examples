# Project Coding Rules (Non-Obvious Only)

- Always use placeholder IPs (`'192.168.###.###'`) in committed code, never real IPs
- Always wrap Moku device interactions in try/finally blocks with `relinquish_ownership()` in finally
- For neural network examples, input/output values must be in range [-1, 1] to avoid clipping
- Use `uv sync --all-extras` for dependency installation (faster than pip)
- Unused variables are allowed in examples (F841 is ignored)
- Lambda assignments are permitted for register calculations (E731 is ignored)