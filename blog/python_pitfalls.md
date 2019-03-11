# Pitfalls in Python
Mark the pitfalls I fell into while writing Python.

## #1
```python
>>> {1: 1, 1: 2}  # expect error
{1: 2}
```

## #2
You should always add `# coding: utf-8` to the head of each Python source file, especially when writing production code.

## #3
List multiplying is error-prone.
```python
>>> matrix = [[0] * 4] * 4
>>> matrix
[[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
>>> matrix[0][0] = 1
>>> matrix
[[1, 0, 0, 0], [1, 0, 0, 0], [1, 0, 0, 0], [1, 0, 0, 0]]  # surprise!!!
```
