# Pitfalls in Python
Mark the pitfalls I fell into while writing Python.


## #1
```python
>>> {1: 1, 1: 2}  # expect error
{1: 2}
```

## #2
You should add `# coding: utf-8` to the head of each Python source file, especially in production code.
