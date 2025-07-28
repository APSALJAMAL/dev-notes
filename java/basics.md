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
```javapublic class StringDemo {
    public static void main(String[] args) {
        String name = "Jamal";

        // Length
        System.out.println("Length: " + name.length()); // Output: Length: 5

        // Character at index
        System.out.println("Character at index 1: " + name.charAt(1)); // Output: Character at index 1: a

        // Substring
        System.out.println("Substring (1 to 3): " + name.substring(1, 3)); // Output: Substring (1 to 3): am

        // Uppercase & Lowercase
        System.out.println("Uppercase: " + name.toUpperCase()); // Output: Uppercase: JAMAL
        System.out.println("Lowercase: " + name.toLowerCase()); // Output: Lowercase: jamal

        // String comparison
        String anotherName = "jamal";
        System.out.println("Equals: " + name.equals(anotherName)); // Output: Equals: false
        System.out.println("Equals Ignore Case: " + name.equalsIgnoreCase(anotherName)); // Output: Equals Ignore Case: true

        // Contains
        System.out.println("Contains 'mal': " + name.contains("mal")); // Output: Contains 'mal': true

        // Replace
        System.out.println("Replace 'J' with 'K': " + name.replace("J", "K")); // Output: Replace 'J' with 'K': Kamal

        // Concatenation
        String greeting = "Hello";
        String fullGreeting = greeting + " " + name;
        System.out.println("Concatenation: " + fullGreeting); // Output: Concatenation: Hello Jamal

        // Loop through characters
        System.out.println("Characters in name:"); // Output: Characters in name:
        for (int i = 0; i < name.length(); i++) {
            System.out.println(name.charAt(i)); 
            // Output: J (then a, m, a, l on next lines)
        }
    }
}

```
```java
public class MathDemo {
    public static void main(String[] args) {
        int a = 25;
        int b = 4;

        // Maximum and Minimum
        System.out.println("Max: " + Math.max(a, b)); // Output: Max: 25
        System.out.println("Min: " + Math.min(a, b)); // Output: Min: 4

        // Power and Square Root
        System.out.println("Power (a^b): " + Math.pow(a, b)); // Output: Power (a^b): 390625.0
        System.out.println("Square Root of a: " + Math.sqrt(a)); // Output: Square Root of a: 5.0

        // Absolute Value
        System.out.println("Absolute of -a: " + Math.abs(-a)); // Output: Absolute of -a: 25

        // Random (0.0 to 1.0)
        System.out.println("Random: " + Math.random()); // Output: Random: (varies every time)

        // Round, Ceil, and Floor
        double val = 5.7;
        System.out.println("Round: " + Math.round(val)); // Output: Round: 6
        System.out.println("Ceil: " + Math.ceil(val));   // Output: Ceil: 6.0
        System.out.println("Floor: " + Math.floor(val)); // Output: Floor: 5.0

        // Trigonometry (in radians)
        double angle = Math.toRadians(30); // 30 degrees to radians
        System.out.println("Sin(30°): " + Math.sin(angle)); // Output: Sin(30°): 0.5
        System.out.println("Cos(30°): " + Math.cos(angle)); // Output: Cos(30°): ~0.866
        System.out.println("Tan(30°): " + Math.tan(angle)); // Output: Tan(30°): ~0.577
    }
}
```
```java
public class ControlFlowDemo {
    public static void main(String[] args) {
        int number = 10;

        // ===== CONDITIONAL STATEMENTS =====

        // if
        if (number > 0) {
            System.out.println("Number is positive"); // Output: Number is positive
        }

        // if-else
        if (number % 2 == 0) {
            System.out.println("Even number"); // Output: Even number
        } else {
            System.out.println("Odd number");
        }

        // else if
        if (number < 0) {
            System.out.println("Negative");
        } else if (number == 0) {
            System.out.println("Zero");
        } else {
            System.out.println("Positive"); // Output: Positive
        }

        // ===== LOOPS =====

        // for loop
        System.out.println("For loop:");
        for (int i = 1; i <= 3; i++) {
            System.out.println("i = " + i); // Output: i = 1, i = 2, i = 3
        }

        // while loop
        System.out.println("While loop:");
        int j = 1;
        while (j <= 3) {
            System.out.println("j = " + j); // Output: j = 1, j = 2, j = 3
            j++;
        }

        // do-while loop
        System.out.println("Do-While loop:");
        int k = 1;
        do {
            System.out.println("k = " + k); // Output: k = 1, k = 2, k = 3
            k++;
        } while (k <= 3);

        // ===== SWITCH STATEMENT =====

        int day = 3;
        System.out.print("Day " + day + " is: ");
        switch (day) {
            case 1:
                System.out.println("Monday");
                break;
            case 2:
                System.out.println("Tuesday");
                break;
            case 3:
                System.out.println("Wednesday"); // Output: Day 3 is: Wednesday
                break;
            default:
                System.out.println("Invalid day");
        }
    }
}
```
