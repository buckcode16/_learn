Keyword arguments vs Positional arguments
In a Python function call, the interpreter expects arguments in a specific order:

1. **Positional Arguments**: These are matched based on their order (e.g., `client.post(url, json=payload)` where `url` is positional).
    
2. **Keyword Arguments**: These are matched by name (`json=payload`, `headers=headers`). Once you use a keyword argument, you cannot go back to using positional arguments.