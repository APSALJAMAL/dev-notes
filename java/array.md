# Array
```java
String[] cars = {"Volvo", "BMW", "Ford", "Mazda"};
int[] myNum = {10, 20, 30, 40};
```
```java
System.out.println(cars[0]);
System.out.println(cars.length);
```
```java
for (int i = 0; i < cars.length; i++) {
  System.out.println(cars[i]);
}
```
```java

for (type variable : arrayname) {
  ...
}

for (String i : cars) {
  System.out.println(i);
}
```
# Multi-Dimensional Arrays

```java
int[][] myNumbers = { {1, 2, 3, 4}, {5, 6, 7} };
```
```java
System.out.println(myNumbers[1][2]);
```
```java
for (int i = 0; i < myNumbers.length; ++i) {
  for (int j = 0; j < myNumbers[i].length; ++j) {
    System.out.println(myNumbers[i][j]);
  }
}
```
```java
for (int[] row : myNumbers) {
  for (int i : row) {
    System.out.println(i);
  }
}
```