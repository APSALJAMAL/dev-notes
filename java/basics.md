```java
public class Main {
  public static void main(String[] args) {
    System.out.println("Hello World");
  }
}
```
```java
System.out.println("Hello World!");
```
```java
// single line

/* multi line  */
```
type variableName = value;
```java
int myNum = 5;
float myFloatNum = 5.99f;
char myLetter = 'D';
boolean myBool = true;
String myText = "Hello";
```
```java
Names can contain letters, digits, underscores, and dollar signs
Names must begin with a letter
Names should start with a lowercase letter, and cannot contain whitespace
Names can also begin with $ and _
Names are case-sensitive ("myVar" and "myvar" are different variables)
Reserved words (like Java keywords, such as int or boolean) cannot be used as names

int m = 60;
```
```java
int myInt = 9;
double myDouble = myInt;  // Automatic casting: int to double

double myDouble = 9.78d;
int myInt = (int) myDouble; // Manual casting: double to int
```
```java
public class StringDemo {
    public static void main(String[] args) {
        String name = "Jamal";

        // Length
        System.out.println("Length: " + name.length());

        // Character at index
        System.out.println("Character at index 1: " + name.charAt(1));

        // Substring
        System.out.println("Substring (1 to 3): " + name.substring(1, 3));

        // Uppercase & Lowercase
        System.out.println("Uppercase: " + name.toUpperCase());
        System.out.println("Lowercase: " + name.toLowerCase());

        // String comparison
        String anotherName = "jamal";
        System.out.println("Equals: " + name.equals(anotherName));
        System.out.println("Equals Ignore Case: " + name.equalsIgnoreCase(anotherName));

        // Contains
        System.out.println("Contains 'mal': " + name.contains("mal"));

        // Replace
        System.out.println("Replace 'J' with 'K': " + name.replace("J", "K"));

        // Concatenation
        String greeting = "Hello";
        String fullGreeting = greeting + " " + name;
        System.out.println("Concatenation: " + fullGreeting);

        // Loop through characters
        System.out.println("Characters in name:");
        for (int i = 0; i < name.length(); i++) {
            System.out.println(name.charAt(i));
        }
    }
}
```
