### declare

```java
package sample;

public class Arrayexample {
	public static void main(String[] args) {
	int[] arr=new int[5];
	arr[0]=192;
//	int[] arr = {12, 4, 5, 2, 5};
	System.out.println(arr[0]);
	
	}
}
```
### print
```java

	for(int i=0;i<arr.length;i++) {
		System.out.println(arr[i]);
	}

```
### for each
```java
for (int i : arr) {
		  System.out.print(i+" ");//12 4 5 2 5 
		}
```
### print in array format
```java
System.out.println(Arrays.toString(arr)); //[12, 4, 5, 2, 5]
```
### reverse
```java
for(int i=arr.length-1;i>=0;i--) {
		System.out.print(arr[i]+" ");
	}
```
### sort , max and min
```java
Arrays.sort(arr);
        System.out.println(arr[0]);
        System.out.println(arr[arr.length-1]);
```
### sum
```java
int sum=0;
	for (int num : arr) {
        sum=sum+num;
    }
	System.out.print(sum);
```
### linear search
```java
int key=42;
for (int i = 0; i < arr.length; i++) {
    if (arr[i] == key) 
    	System.out.print(i);
}
```
###
```java
```
###
```java
```
###
```java
```
###
```java
```
###
```java
```

