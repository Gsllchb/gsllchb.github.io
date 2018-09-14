# Pitfalls in Java
Mark the pitfalls I fell into while writing Java.


## #1
```java
ArrayList<Character> list = new ArrayList<>();
list.add('a');
list.remove('a');  // raise IndexOutOfBoundsException
list.remove((Character) 'a');  // return true
```