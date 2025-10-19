# Project Debug Rules (Non-Obvious Only)

- Check IP address configuration first when debugging connection issues
- Verify `relinquish_ownership()` is called in finally block to prevent device lockouts
- For neural network examples, check if input/output values exceed [-1, 1] range (causes clipping)
- Use `uvx ruff check` to run linting checks without modifying files
- Moku device errors often require checking physical connections and power