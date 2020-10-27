# My solution of the Google puzzle: First 10-digit prime found in consecutive digits of *e*
![](../img/first_10digit_prime_in_e.jpg)
```python
from sympy import *

TEN_POW_TEN = Number(10 ** 10)


def is_prime(num) -> bool:
    assert num >= 0
    if num <= 1:
        return False
    elif num < 4:
        return True
    for i in range(2, int(sqrt(num)) + 1):
        if num % i == 0:
            return False
    return True


def find_first_10digit_prime_in_e():
    s = Number(0)
    n = Number(0)
    a = Number(10 ** 9)
    while True:
        while a > (1 - s % 1) / 2:
            s += a
            n += 1
            a /= n
        num = int(s) % TEN_POW_TEN
        s = s - TEN_POW_TEN * (s // TEN_POW_TEN)
        if is_prime(num):
            return num
        a *= 10
        s *= 10


if __name__ == '__main__':
    print(find_first_10digit_prime_in_e())

```
Result: `7427466391`
