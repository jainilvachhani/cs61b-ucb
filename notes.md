# CS61B Data Structures - lecture notes
* * *

@ UCB, Spring 2018  
@ Instructor: [Josh Hug](https://www2.eecs.berkeley.edu/Faculty/Homepages/joshhug.html)  
@ Lecture: MWF 3-4 PM, Wheeler 150  
@ [Course Website](https://sp18.datastructur.es/index.html)  
@ [Java Visualizer](https://cscircles.cemc.uwaterloo.ca/java_visualize/)
* * *


## 1.1 - Intro, Hello World Java
### Running a Java Program

Given `HelloWorld.java` ->

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello world!");
    }
}
```


```bash
$ javac HelloWorld.java
$ java HelloWorld
Hello World! 
```

### Variables and loops

Given `HelloNumbers.java`

```java
public class HelloNumbers {
    public static void main(String[] args) {
        int x = 0;
        while (x < 10) {
            System.out.print(x + " ");
            x = x + 1;
        }
    }
}
```

```bash
$ javac HelloNumbers.java
$ java HelloNumbers
$ 0 1 2 3 4 5 6 7 8 9 
```

### Defining functions in Java

```java
public class LargerDemo {
    public static int larger(int x, int y) {
        if (x > y) {
            return x;
        }
        return y;
    }

    public static void main(String[] args) {
        System.out.println(larger(8, 10));
    }
}
```


## 1.2 - Defining and Using Classes
### public static void main(String[] args)
* `public`: So far, all of our methods start with this keyword.
* `static`: It is a static method, not associated with any particular instance.
* `void`: It has no return type.
* `main`: This is the name of the method.
* `String[] args`: This is a parameter that is passed to the main method.

```java
public class ArgsDemo {
    public static void main(String[] args) {
        System.out.println(args[0]);
    }
}
```

This program prints out the 0th command line argument, e.g.

```bash
$ java ArgsDemo these are command line arguments
these
```

However, those args need to be sepaerated by `space`, or it will print all of it. In the example above, args will be an array of Strings, where the entries are `{"these", "are", "command", "line", "arguments"}`.

Convert from `String` to `int`: 

```java
String myString = "1234";
int foo = Integer.parseInt(myString);
```


## 2.1 - References, Recursion, and Lists
### The Mystery of the Walrus
Try to predict what happens when we run the code below. Does the change to b affect a? Hint: If you're coming from Python, Java has the same behavior.

```java
Walrus a = new Walrus(1000, 8.3);
Walrus b;
b = a;
b.weight = 5;
System.out.println(a);
System.out.println(b);

// weight: 5, tusk size: 8.30
// weight: 5, tusk size: 8.30
```

```java
int x = 5;
int y;
y = x;
x = 2;
System.out.println("x is: " + x);
System.out.println("y is: " + y);

// x is 2
// y is 5
```

### Bits
8 primitive types in Java: byte, short, **int**, long, float, **double**, boolean, char. Everything else, including arrays, is not a primitive type but rather a `reference type`.  
All information in your computer is stored in `memory` as a sequence of ones and zeros

* 72 is often stored as 01001000
* 205.75 is often stored as 01000011 01001101 11000000 00000000
* The letter H is often stored as 01001000 (same as 72)
* The true value is often stored as 00000001

```java
char c = 'H';
int x = c;
System.out.println(c);
System.out.println(x);

// H
// 72
```

In this case, both the x and c variables contain the same bits (well, almost...), but the Java interpreter treats them differently when printed.

### Declaring a variable
When you declare a variable of a certain type in Java:

- Your computer sets aside exactly enough bits to hold a thing of that type.
	- Example: Declaring an int sets aside a “box” of **32** bits.
	- Example: Declaring a double sets aside a box of **64** bits.

- Java creates an internal table that maps each variable name to a location.
- Java does NOT write anything into the reserved boxes.
	- For safety, Java will not let access a variable that is uninitialized.
- Instead of writing memory box contents in binary, we’ll write them in human readable symbols.

The exact memory address is below the level of abstraction accessible to us in `Java`. This is unlike languages like `C` where you can ask the language for the exact address of a piece of data.


### Reference Types
#### Object Instantiation
When we instantiate an Object using `new` (e.g. Dog, Walrus, Planet), Java first allocates a box for each instance variable of the class, and fills them with a default value. The constructor then usually (but not always) fills every box with some other value.

#### Reference Variable Declaration
When we declare a variable of any reference type (Walrus, Dog, Planet, array, etc.), `Java` allocates a box of **64 bits**, no matter what type of object.  

The 64 bit box contains **not the data** about the walrus, **but instead the address** of the Walrus in memory.

```java
public static class Walrus {
    public int weight;
    public double tuskSize;

    public Walrus(int w, double ts) {
          weight = w;
          tuskSize = ts;
    }
}


Walrus someWalrus;
someWalrus = new Walrus(1000, 8.3);
```

The first line creates a box of **64 bits**. The second line creates a new Walrus, and the address is returned by the new operator. These bits are then copied into the someWalrus box according to the GRoE.


### Parameter Passing
When you pass parameters to a function, you are also simply copying the bits. In other words, the GRoE also applies to parameter passing. Copying the bits is usually called "pass by value". In `Java`, we always pass by value.

```java
public class PassByValueFigure {
    public static void main(String[] args) {
           Walrus walrus = new Walrus(3500, 10.5);
           int x = 9;

           doStuff(walrus, x);
           System.out.println(walrus);
           System.out.println(x);
    }

    public static void doStuff(Walrus W, int x) {
           W.weight = W.weight - 100;
           x = x - 5;
    }
}
```


#### Exercise

```java
public class PassByValueFigure {
    public static void main(String[] args) {
           Walrus walrus = new Walrus(3500, 10.5);
           int x = 9;

           doStuff(walrus, x);
           System.out.println(walrus);
           System.out.println(x);
    }

    public static void doStuff(Walrus W, int x) {
           W.weight = W.weight - 100;
           x = x - 5;
    }
}
```
Does the call to `doStuff` have an effect on walrus and/or x? Hint: We only need to know the GRoE to solve this problem.

### Instantiation of Arrays

```java
int[] a = new int[]{0, 1, 2, 95, 4};
```

1. Creates a **64 bit** box for storing an **int array address**. (declaration)
1. Creates a new Object, in this case an int array. (instantiation)
1. Puts the **address** of this new `Object` into the 64 bit box named a. (assignment)

Objects can be lost if you lose the bits corresponding to the address. For example if the only copy of the address of a particular Walrus is stored in `x`, then `x = null` will cause you to **permanently** lose this Walrus. This isn't necessarily a bad thing, since you'll often decide you're done with an object, and thus it's safe to simply throw away the reference. We'll see this when we build lists later in this chapter.


### IntLists

```java
public class IntList {
    public int first;
    public IntList rest;        

    public IntList(int f, IntList r) {
        first = f;
        rest = r;
    }
}

// IntList L = new IntList(5, null);
// L.rest = new IntList(10, null);
// L.rest.rest = new IntList(15, null);

IntList L = new IntList(15, null);
L = new IntList(10, L);
L = new IntList(5, L);
```

#### size and iterativeSize

```java
/** Return the size of the list using... recursion! */
public int size() {
    if (rest == null) {
        return 1;
    }
    return 1 + this.rest.size();
}

System.out.println(size())
```

The key thing to remember about recursive code is that you need a base case. In this situation, the most reasonable base case is that rest is null, which results in a size 1 list.

`iterativeSize` method is as shown below. I recommend that when you write iterative data structure code that you use the name `p` to remind yourself that the variable is holding a **pointer**. 

Pointer is needed because you cannot reassign `this` in Java. 

```java
/** Return the size of the list using no recursion! */
public int iterativeSize() {
    IntList p = this;
    int totalSize = 0;
    while (p != null) {
        totalSize += 1;
        p = p.rest;
    }
    return totalSize;
}

System.out.println(L.iterativeSize())
```

### get

```java
/** Returns the ith item of this IntList. */
public int get(int i) {
    if (i == 0) {
        return first;
    } else {
        return this.rest.get(i - 1);
    }
}
```

**Note:** the method here takes linear time! That is, if you have a list that is 1,000,000 items long, then getting the last item is going to take much longer than it would if we had a small list. We'll see an alternate way to implement a list that will avoid this problem in a future lecture.



















