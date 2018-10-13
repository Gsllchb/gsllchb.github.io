# Pitfalls in Java
Mark the pitfalls I fell into while writing Java.


## #1
```java
ArrayList<Character> list = new ArrayList<>();
list.add('a');
list.remove('a');  // raise IndexOutOfBoundsException
list.remove((Character) 'a');  // return true
```

## #2
```java
assert new String("123") != new String("123");
assert new String("123").equals(new String("123"));
```

## #3
```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};

assert !a.equals(b);
assert java.util.Arrays.equals(a, b);

assert a.hashCode() != b.hashCode();
assert java.util.Arrays.hashCode(a) == java.util.Arrays.hashCode(b);
```
