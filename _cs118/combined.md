---
layout: CS118
part: true
math: true
title: CS118 Combined Notes
---



# Introduction to programming

 - Ways to classify programming languages
   	- High and low level languages
      - Low level languages only support simple operations, which they can run very quickly, but are difficult to program complex tasks in due to this restriction
      - High level languages rely on abstractions on top of low level languages to perform more complex operations, but these operations run slower as they are composed from many smaller ones, and are often less fit for purpose, as they are generic for many use cases
    - Programming paradigms
      - Imperative, instructing a machine how to change its state
        - Procedural - Grouping sequences of instructions into procedures
        - Object oriented - Grouping data and operations on the data together to model systems
      - Declarative, declaring the properties of the desired result, but not how to compute it
        - Functional - Declaring the result as the value of a sequence of function applications
        - Logical - Declaring the result as the answer to a question composed of a set of axioms and inference rules
- Java is an object oriented programming language
  - Provides automatic memory management, called garbage collection
  - Error checks at both compile and runtime
  - Doesn't compile straight to machine code like most compiled languages, but instead to "bytecode", which is not human-readable, but is portable between machines, unlike machine code, which is specific to an individual machines architecture/instruction set
    - The java code is first compiled to bytecode (Using the `javac` command, producing a `.class` file)
    - The bytecode in this generated file is then interpreted into machine code in the JVM (Java virtual machine), using a JIT (just in time) compiler, so the computer can execute it (Using the `java` command)
- The process of converting the specification of what a program should do to program code is known as "refinement". This often includes five main things
  - Precondition - when should the program work
  - Input - what data does the program need
  - Calculates - what should the program calculate
  - Output - what results does the program return
  - Postcondition - what is the state after execution

# Variables, operators and types

- Variables store values, associating them with a name so they can be used symbolically
  - Variables require a type and a name, given by `[type] name;`, defined in a process called instantiation
  - They can then be assigned a value, either on the same line or after instantiation `name = value`
- Number systems
  - Numbers are stored in binary
  - In order to store negative integer numbers, we don't just add a "sign bit" as we would normally, as then we define both positive and negative values for zero, which wastes space, and can be confusing. Instead, we use "Two's complement"
    - Binary representation, but the left-most bit is negative
      ![twosComplement](..\media\twosComplement.png)
    - With eight bits, the smallest value is $$10000000_2 = -128_{10}$$, and the largest value is $$01111111_2 = 127_{10}$$ (this is $$-2^n $$ to $$2^n - 1$$)
  - In order to store decimal numbers, we use "floating point notation", which automatically handles the trade-off between precision and range for storing numbers in a fixed number of bits. This is defined in the IEEE-754 specification (for single precision `floats`, `double`s are a bit different):
    ![IEEE754main](..\media\IEEE754main.png)
    - The "sign bit" denotes whether the number is positive or negative
    - The "exponent" needs to represent both positive and negative numbers, but it **does not** use two's complement, it instead uses a bias of $$127$$, meaning you subtract $$-127$$ from the decimal value to get the exponent. Additionally, $$-127_{10} = 00000000_2$$ and $$128+{10} = 11111111_2$$ are reserved special cases. Double precision uses an 11-bit field instead of an 8-bit one, and hence has an offset of $$1023$$
    - The "fraction/mantissa" represents the precision bits of the number, with the most significant bit being $$\frac{1}{2^1}$$, then $$\frac{1}{2^2}$$, and so on. This is taken as the value after the binary point, and in almost all cases, the value before the binary point is one, however, if the exponent is all $$0$$s, it is $$0$$ and said to be denormalised. Double precision uses a 52-bit field instead of a 23-bit one.
      As a rule of thumb, calculate the value of the fraction from binary with the reciprocal values, the add one to it
    - The fraction represents the value of the number, and is of fixed precision, for example, 23 bits of information. The exponent then specifies the range of the number.
    - Special values
      - Denormalised, when the exponent is all $$0$$s, so the number is assumed to have a leading $$0$$ not a leading $$1$$ before the binary point, i.e. $$0.[fraction]$$ not $$ 1.[fraction]$$
      - Zero, when both the exponent and fraction are all $$0$$s, so it resolves to $$0.0$$ . Due to the sign bit, there are both positive and negative zeroes, but they compare as equal
      - Infinity, positive and negative infinities are when the exponent is all $$1$$s, but the fraction is all $$0$$s, and the sign bit indicates sign. Since they are actual values in the scheme, we can do operations on them
      - NaN (not a number), when the exponent is all $$1$$s, and there is a non-zero fraction. These can be used for signalling errors etc.
  - A tool for **checking** this is available: https://www.h-schmidt.net/FloatConverter/IEEE754.html, and additional resources are available: https://steve.hollasch.net/cgindex/coding/ieeefloat.html
  - An example of this in practice is:
    ![IEEE754example](..\media\IEEE754example.png)
    Note that the "1.(fraction)" denotes a decimal point, not a multiplication operation

## Data types

### Primitive data types

| Name      | Range                                                        | Size    | Default  |
| --------- | ------------------------------------------------------------ | ------- | -------- |
| `boolean` | `true` or `false`                                            | 1 bit   | `false`  |
| `byte`    | $$-128$$ to $$127$$                                              | 1 byte  | `0`      |
| `short`   | $$-32768$$ to $$+32767$$                                         | 2 bytes | `0`      |
| `char`    | $$0$$ to $$65,535$$                                              | 2 bytes | `\u0000` |
| `int`     | $$-2,147,483,648$$ to $$+2,147,483,647$$                         | 4 bytes | `0`      |
| `long`    | $$-2^{63}$$ to $$2^{63}-1$$                                      | 8 bytes | `0L`     |
| `float`   | $$-3.4 \times 10^{38}$$ and $$3.4 \times 10^{38}$$ with 6 to 7 significant digits of accuracy | 4 bytes | `0.0f`   |
| `double`  | $$-1.7 \times 10^{308}$$ and $$1.7 \times 10^{308}$$ with 14 to 15 significant digits of accuracy | 8 bytes | `0.0d`   |

The `char` primitive is essentially an unsigned `short` primitive, which is used to store UTF-16 encoded single characters instead of a number

### Passing by reference and by value

- Primitive data types are stored as a value in a place in memory, for example, and `int` would be four consecutive bytes, whose value can be looked up by a variable name
- However, since objects are not necessarily of a fixed size, we cannot just allocate an amount of memory for them. Instead, we store a "reference" to them on the heap, and the variable name refers to this reference, not the actual data
  - Because of this, we cannot use the `==` operator to compare object contents, as it merely compares their memory references, only telling you if it is the exact same instance, not if it has the same stuff in
- All parameters passed to and returned by methods are "passed-by-value", i.e. the data in the physical memory space is duplicated in the scope of the method, and won't affect the contents of the variable which it was passed from. However, we know that objects are only stored as references to the data on the heap, so the value passed is only the reference to the data, so it any operations will act on the same object, so it becomes "pass-by-reference", as it is passing the value of the reference to the object
  - In practice, in Java primitives are "pass-by-value" (so changing them in the method won't change them outside it), and objects are "pass-by-reference" (so any changes to them in a new method will affect their state outside of it)
- Autoboxing is an action performed automatically by the compiler, which "boxes" a primitive into its corresponding object (e.g. `int` to `Integer`) if a method requires an object, not just a primitive value.

### Casting

- Casting is the process of changing the data type used to express a piece of information, for example to change the range or the precision used to store a numeric value

- There are three different sets of keywords used to describe casting, which are:

  - Explicit and implicit

    - Explicit casting is when the casting is indicated directly in the source code. In Java, this is denoted by wrapping the new type in brackets, for example `castValue = (newType) value`
    - Implicit casting is when the casting can be inferred by the compiler, and so does not need to be indicated directly in the source code (however, it can be indicated with explicit casting as well for easier readability)

  - Safe/widening and unsafe/narrowing casting

    - Safe (also called widening) casting is when the value is moved to a data type which supports more precision or a greater range of values. **No data is lost in this operation**. This can be done simply by setting a more precisely/larger typed variable equal to the value of a less precisely/smaller typed one, using implicit casting

      ```java
      float narrowValue = 3.141f;
      double widenedValue = narrrowValue;
      ```

      Explicit casting can also be used, although it is not necessary, for additional readability

    - Unsafe (also called narrowing) casting is when the value is moved to a data type which supports less precision or a smaller range of values. **Data can (possibly but not necessarily) be lost in this operation**. This must be indicated with an explicit cast, with brackets indicating the new type, as the compiler cannot infer that the type should change

      ```java
      float wideValue = 3.141f;
      int narrowedValue = (int) wideValue;
      ```

      Explicit casting is required for unsafe casting, but unsafe casting is not required for explicit casting

  - Checked and unchecked casting

    - Checked casting is when the cast operation is "well-defined", i.e. the output type is able to represent the information stored in the input type. For example, numeric types can be safely cast between each other, since they all encode numbers

    - Unchecked casting is when the cast operation is may not be "well-defined", i.e. the output type may not be able to represent all the information stored in the input type, or the input type may not provide enough information to "fill" the output type. For example, casting from an `ArithmeticException` to an `Integer` would be an unsafe cast, as one cannot fully represent the other.

      Unsafe/unchecked casting will result in an error being thrown by the compiler, unless the `@SuppressWarnings("unchecked")` method annotation is used, which allows the code to be compiled, but this may still fail at runtime

## Operator evaluation and precedence

- Types of operators
  
  - Comparison operators: `==, !=, >=, <=, <, >`
  - Boolean operators: `&, &&, |, ||, !`
  
- Lazy and strict operators
  - Lazy operators aren't executed if an earlier operations fully defines the statement, e.g. the value is an or, and the first statement is true, the second statement needn't be tested
  - Strict operators execute everything, even if it isn't "needed" to evaluate the statement, as state might be mutated in later parts, e.g. `y && (x++ == 4)` would change the value of `x`
  
- Initialising variables allocates space in memory. Variables need to be initialised along all code paths, as otherwise the memory might not be allocated

- Pre-increment and post-increment
  
  - `++i` increments the variable before it is used in the equation,`i++` increments it after it has been used.
  
- Operator precedence ([additional source](http://pages.cs.wisc.edu/~willb/cs302/java-operator-precedence.pdf))
  
  | Letter | Name             | Operations             |
  | ------ | ---------------- | ---------------------- |
  | P      | Postfix          | `expr++`, `expr--`     |
  | U      | Unary/Prefix     | `++expr`, `--expr`     |
  | M      | Multiplicative   | `*`, `/`, `&`          |
  | A      | Additive         | `+`, `-`               |
  | S      | Shift            | `>>`,`<<`              |
  | R      | Relational       | `>`,`<`,`<=`,`>=`      |
  | E      | Equality         | `==`,`!=`              |
  | S      | Strict operators | `&`,`|`,`^`            |
  | L      | Lazy operators   | `&&`,`||`              |
  | A      | Assignment       | `=`,`+=`,`-=`,`*=`,... |

# Conditionals

- The `if..else if..else` statement

  - Boolean values are used to decide whether code blocks are run

    - ```java
      if (condition_1) {
          //If condition_1 is true, execute the exit the whole statement
      } else if (condition_2) {
          //If condition_1 is false, and condition_2 is true then execute the exit the whole statement
      } else if (condition_3) {
          //If condition_1 is false, condition_2 is false, and condition_3 is true then execute the exit the whole statement
      } else {
          //If none of the preceeding conditions are true, execute
      }
      ```

  - The curly braces can be omitted for single line statements, however, this is less readable, and can lead to issues such as the dangling else, so in my opinion should be avoided

- Ternary operators are a shorthand for assigning values using `if..else` statements

  - They evaluate as follows `output = (condition) ? (valueIfTrue) : (valueIfFalse)`
  - They can be nested, but this is code smell, and should probably be reverted to an `if..else if..else` statement instead

- The `switch` statement

  - `Switch` statements offer a way to select control flow based off the value of a variable as opposed to a boolean condition. In some cases, this can be more simple, and hence preferred over `if` statements

  - ```java
    switch (variable) {
            case value_1:
                //If the variable is equal to value_1, execute the code, then exit the statement due to the break statement
                break;
            case value_2:
                //If the variable is not equal to value_1, and is equal to value_2 then execute the code, and continue executing each block until the next break statement occurs, or the statement ends
            case value_3:
                //If the variable is not equal to value_1, and is equal to either value_2 (since it is running through with no break statement) or value_3, then execute the code, then exit the statement due to the break statement
            	break;
            default:
            	//If no cases have been satisfied, or the code runs through due to no break statements, execute the code
    }
    ```

  - Both the `break` and `default` sections are optional

  - Objects should not be switched on, only primitives, as the equality will compare their memory reference with the `==` operator, rather than calling the `.equals()` function.

  - Switch statements are faster than `if..else if.. else` statements when there are a large number of cases, as lookup tables can be used, which are fast

- We can reason carefully about what happens in conditionals by writing out the preconditions (similar idea to the explanation above, but easier to do it in boolean expressions to be terse)

# Iteratives

- Bounded repetition

  - Bounded repetition is when the number of times to loop is known in advance

  - We know "where we wish to start, where we wish to end, and what iterative step to take each repetition"

  - The `for` statement implements bounded repetition

    ```Java
    //for (initialisation; booleanExpression; iteration) {}
    
    //Each iteration, increment a counter from 0 by 1 until it is greater than five, i.e. repeat six times
    for (int i=0; i<=5; i++) {
        //Execute some code
    }
    ```

- Unbounded repetition

  - Unbounded repetition is when the number of times to loop is not known in advance, merely the condition to stop looping. The can lead to infinite loops
  - The `while` statement implements "pre-test" unbounded repetition
    - A boolean expression is checked, then a code block is repeatedly run if it is true, and it moves on if it is false
    - The expression is checked **before** the code is run
  - The `do..while` statement implements "post-test" unbounded repetition
    - A code block is run, then a boolean expression is checked, then the code block is repeatedly run if it is true, and it moves on if it is false
    - The expression is checked **after** the code is run
  - Both statements are equivalent, as external boolean statements can be used to make the first test on the `while` loop always true

- The `break` and `continue` keywords

  - `break` causes immediate exit from the highest level loop (current scope), moving on to the code after it
  - `continue` skips the current iteration of the highest level loop (current scope), going back to the start of the next iteration at the beginning of the statement

# Arrays and Methods

- Arrays

  - Store "lists of monomorphic data" in a neat (single variable name) and efficient (indexable items) way
  - Created with the syntax `[type][] name;` e.g. `int[] arrayOfInts`
  - Arrays can be multidimensional, with each item itself being an array, but all items must be of the same type throughout. E.g. `int[][] matrix`
  - When the array is declared, it starts at the special value null, and Java doesn't allocate any memory to it, since it doesn't know its size
  - Arrays can be instantiated using the `new` keyword, which reserves a block of contiguous memory. The size of the array must be provided at this point, so Java can calculate how much memory should be allocated given the number of items and the size of each item from its data type. An example of this is `int[] arrayOfInts = new int[5] `
  - The items in arrays can be "indexed", looking up values at a cell location. Indexing starts at 0 for the first cell, and the syntax is `array[index]`. Values can be got and set this way
  - Arrays can also be defined inline using the shorthand syntax `int[] arrayOfInts = {1,2,3,4}` 
  - Arrays have only one property, which is `array.length`. This tells us the number of indexable cells the array contains

- Methods

  - The main method is a special method which is the "entry point" for java programs

  - All methods have "signatures", which describe their name, return type, access modifiers, and parameters

    - Example: `public static void main(String[] args) {  }`

      [Access modifier] [keyword (optional)] [return type] [name] ([arguments..]) {  }

  - The `return` statement

    - Tells the method which value to return. This value must be of the type indicated in the method signature

  - Methods can call themselves in a process called recursion (See the cult of CS141...)

  - Method overloading

    - Two methods can have the same name, as long as they have different parameters. Which method will be run will be selected by which one's signature fits the type and number of parameters passed

  - Scope

    - Variables have scope, which is defined as the block and all child blocks in which it is initialised
    - When a function is called a new "computation environment" is created, and all variables declared outside the function do not have scope within it
    - Variables must have a unique name within a scope
    - Sometimes we want to be able to access variables across many methods. We can define class variables to do this, which are variables which have scope across the entire object. These are defined at the start of the class, not within a method, before the constructor

- Passing by value and by reference

  - We said that primitive variables directly store their values in memory. When they are provided as parameters, they are "passed by value". This means that the value of the variable is cloned in memory to a new variable in the new scope. If the variable is changed in that new scope, it is unaffected outside of it, where it was passed from
  - Non-primitive variables (e.g. objects and arrays) are not store in memory. When they are provided as parameters, they are "passed by reference". This means that if they are mutated in the new scope, they will also change in the old one, since both references point to the same place, and the item is being changed in that place

# Objects

- Paradigm shift from procedural/imperative to object oriented programming
  - Procedural defines a sequence of steps to achieve a goal
  - Object oriented models systems as data (properties) and operations on the data (behaviour) in a structure called an object
    - The operations on the data are procedural, so object oriented encompasses procedural
    - Essentially just a way of organising code to bundle data and operations together, providing a convenient abstraction
    - Object oriented programs can be considered as a collection of co-operating objects
- Classes are the "blueprint" for objects, defining what data will be stored, and the implementation of the algorithms on it, whereas objects are "instances" of classes, containing the data, and allowing the operations to be performed on it
- The `class` keyword defines a new class, and `public` classes need their own file (by convention, classes start with a capital letter)
- Constructor methods are special methods that have the same name as the class and return no value. They "set up" the object, normally assigning values to variables. (Objects needn't have constructors, they are merely overriding the default constructor, which performs no operations)
- New instances of an object are created using the `new` keyword, which allocates space in memory and calls the constructor. If an object is declared but not instantiated, it takes the special `null` value as with arrays
- The `this` keyword refers to class variables. It can be omitted normally, unless there is a name clash with a local variable of the same name.
- Objects are created from classes using the `new` keyword. This reserves space in memory for the default values of the object, and calls the constructor
- To compare objects, we need to use the `.equals()` method, which compares the state of the all variables within the object (and can be overridden), as opposed to the `==` operator, which compares the actual memory location, so would only return true for two references to the same object instance, not just two objects with the same values

## Pillars of object oriented programming

There is some argument over the number of "pillars", but the four commonly accepted ones are:

- Abstraction
  - "Exposing essential features, whilst hiding irrelevant detail" 
  - Used so that the purpose/interface of a function can be separated from its implementation, making code re-usable, more understandable, and easier to modify (won't break things, changes need to be made in fewer places)

- Encapsulation
  - "Bundling data and operations that can be performed on that data together - leads to data hiding"
    - Encapsulation is not data hiding, but it does lead to it
    - Making state variables `private` and the methods that act on them `public` is called data hiding
  - "Wrapping data and the code acting on it together as a single unit", so users must use the external interface, rather than just changing things internally.
  - "The action of enclosing something in or as if in a capsule", so that an object controls its own internal state, and should only be able to be changed through its interface. This is often implemented through/results in data hiding, which is removing access to variables within a class by making the `private`, and then providing getters and setters, rather than direct access to the data. This means that some internal variables can not be externally changed if no method is provided, and operations such as validation can be performed, instead of just directly changing the data
  - This has the beneficial property of the internal implementation being hidden, so it can be changed and improved without changing the external interface, so other code doesn't break. Additionally, it hides the unnecessary detail of the implementation, and clarifies responsibility for the code

- Inheritance
  - "Creating new classes from existing ones, reducing programmer effort"
  - "Allowing an object to acquire the properties and methods of another object", so that code can be re-used, along with helping abstractions and hierarchy.
    - A subclass inherits all the properties from its superclass, and may have some additional ones of its own, and/or override some of the parents, however, it must not remove methods from the parent class (known as Liskov substitution principle)
  - The `super` keyword is used to refer to methods and properties of the superclass
  - Method overriding is when a subclass changes the definition of a function defined by its superclass. It is somewhat like overloading with different numbers of parameters for methods. If a method defined in the superclass is re-defined for the subclass, it will override it. This means that instances of the superclass will run the superclass definition, but those of the subclass will run the subclass definition, without the parent class being modified, and the subclass still retaining all other parent properties without having to redefine them
    - Class constructors are not inherited from their superclasses. To keep the functionality of their parent class, they can call the parent class constructor using `super()`, and then add any additional functionality later in the constructor. This method call must always be the first thing in the subclass constructor if it is used.
  - A subclass can only inherit from one superclass, to avoid name-collisions if two superclasses implemented the same method signature differently

- Polymorphism (English definition "the condition of occurring in several different forms")

  - Using objects that can take many forms - allows us to invoke operations from derived classes while using a base class reference at run-time
  - "Polymorphism describes its ability to process objects of different types through a single uniform interface"
  - Static polymorphism ([source](https://pediaa.com/what-is-the-difference-between-static-and-dynamic-polymorphism-in-java/))
    - Occurs at compile time
    - An example is method overloading
      - Different behaviour is exhibited by the same method (in a given class) dependent on its interface, e.g. having different input types
  - Dynamic polymorphism ([source](https://pediaa.com/what-is-the-difference-between-static-and-dynamic-polymorphism-in-java/))
    - Occurs at runtime
    - An example is method overriding
      - Methods in subclasses having different behaviour to the equivalent method in their superclass
      - Allows us to cast objects which are stored in their superclass to their subclass, so we can use their subclass interfaces
      - "Relating to the class hierarchy within a program"



## SOLID design principles

- Single Responsibility Principle
  - "A class should have one and only one reason to change, meaning that a class should have only one job."
  - Removes "coupling" that would occur if one class encoded two things, as if one of them had to be changed, the other would be forced to to, even if it could stay unchanged if it were not coupled
- Open/Closed Principle
  - “Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.”
  - This means that you should be able to add new functionality without having to modify existing code
  - This further means that adding new functionality doesn't force you to change any existing interfaces, which would otherwise require changing all calls to instances of the object
- Liskov Substitution Principle
  -  "Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it"
  -  "Let $$\Phi(x)$$ be a property provable about objects *x* of type *T*. Then  $$\Phi(y)$$ should be true for objects *y* of type *S* where *S* is a subtype of *T*."
     - This means that subclasses cannot remove functionality (e.g. implementations of methods) provided by their superclasses, nor impose tighter restrictions on the inputs into or outputs from them
  -  This avoids errors which might be caused from casting between super and sub classes
- Interface Segregation Principle
  - "Many client-specific interfaces are better than one general-purpose interface."
  - “Clients should not be forced to depend upon interfaces that they do not use.”
  - If one large general interface is used, and part of it has to be changed, then all other places which rely on it must be changed. If this is split up into multiple smaller interfaces, then only one needs to be changed, and only the places which rely directly on that one interface need be changed
- Dependency Inversion
  - "Depend upon abstractions, [not] concretions."
    - High-level modules should not depend on low-level modules, instead, both should depend on the same abstractions
    - Abstractions should only implement high-level logic, not detail, which can then be specified in concretions

Additional resources: [here](https://stackify.com/solid-design-principles/) and [here](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)



# Modifiers

- Access modifiers

  - `public` - can be accessed everywhere in the program
  - `private` - can only be accessed from within its own class (including different instances of the same class)
  - `protected` - can only be accessed from within its own class, or any subclasses (like `private`, but including subclasses)
  - These restrict the access to an objects properties an behaviours, meaning we can more closely control the interface provided for the object
  - Which modifier should be picked with common sense, and thinking about what interface we're trying to provide
  - The constructor clearly must be `public`, as we need to access it from outside the object, as the object doesn't exist yet
  - No access modifier defaults to "package private", which is somewhere between `private` and `protected` (can only be accessed by objects in the same package)

- Encapsulation and data hiding

  - Usually all state variables are `private`, but we might need a way to change the externally
  - Making state variables `private` and the methods that act on them `public` is called data hiding
  - Encapsulation is "wrapping data and the code acting on it together as a single unit", so users must use the external interface, rather than just changing things internally.
  - Encapsulation is not data hiding, but it does lead to it
  - This has the beneficial property of the internal implementation being hidden, so it can be changed and improved without changing the external interface, so other code doesn't break. Additionally, it hides the unnecessary detail of the implementation, and clarifies responsibility for the code
  - Data hiding is often implemented using "getters" and "setters", which are methods which allow internal private variables to be changed. This means that some internal variables can not be externally changed if no method is provided, and operations such as validation can be performed, instead of just directly changing the data

- Class/instance/final variables

  - Instance variables have values attached to a specific instance of a class. This is the default for variables defined in classes
  - Class variables allow data to be shared between instances of a class. This is denoted by the `static` keyword, and starts to act more like imperative programming
    - Static properties are shared across all instances of the class
    - Static methods are called from the class name, not the object name
    - Static blocks are only executed once, the first time the class is loaded in memory
    - These static variables/methods can be called/interacted with anywhere through the name of the class, but a static method cannot call a non-static method
  - `final` variables define a constant whose value cannot be changed after it has been set

- Enumerated types

  - A special type of class that hold a range of constant values

# Inheritance

- Inheritance is properties that a taken from a parent
  - We call the "parent" a base of a super class
  - We call the "child" a derived or sub class
  - A class is indicated as inheriting from a superclass using the `extends` keyword, e.g. `public class Lion extends Cat`. Classes can only inherit from one superclass in Java (but sometimes more in other languages)
  - A subclass inherits all the properties from its superclass, and may have some additional ones of its own, and/or override some of the parents, however, it must not remove methods from the parent class (known as Liskov substitution principle - not examined)
  - Overriding is when a subclass changes the definition of a function defined by its superclass. It is somewhat like overloading with different numbers of parameters for methods. If a method defined in the superclass is re-defined for the subclass, it will override it. This means that instances of the superclass will run the superclass definition, but those of the subclass will run the subclass definition, without the parent class being modified, and the subclass still retaining all other parent properties without having to redefine them
  - The `super` keyword is like the `this` keyword for the superclass. We can then use `super()` to call the superclass constructor in the subclass, so it doesn't need to be redefined (it must be the first thing that happens in the subclass constructor)
  - `protected` properties cannot be "seen" externally, but can be "seen" by subclasses, meaning we needn't go through or define accessor methods for such properties
  - All classes are ultimately subclasses of the `Object` class
- Polymorphism
  - "Polymorphism describes its ability to process objects of different types through a single uniform interface"
  - "Inheritance lets us  inherit attributes and methods from another class. Polymorphism  uses those methods to perform different tasks. This allows us to perform a single  action in different ways." (https://www.w3schools.com/java/java_polymorphism.asp)
  - We can pass `Object` classes into any method that takes any non-primitive type, since everything is a subclass of it
  - Static vs dynamic polymorphism
    - Static polymorphism ([source](https://pediaa.com/what-is-the-difference-between-static-and-dynamic-polymorphism-in-java/))
      - Occurs at compile time, for example method overloading
      - Different behaviour is exhibited by the same method dependent on the input types
      - An example is method overloading
    - Dynamic polymorphism ([source](https://pediaa.com/what-is-the-difference-between-static-and-dynamic-polymorphism-in-java/))
      - Occurs at runtime
      - Allows us to cast objects which are stored in their superclass to their subclass, so we can use their subclass interfaces
      - An example is method overriding
  - The `instanceof` keyword tells us whether an object is of a given type of class. We can use this to check when we need to cast objects so we can use their appropriate interfaces



# Abstract classes, interfaces, and enumerated types

## Abstract classes

- When superclasses become so generic they contain methods which are only well-defined for their subclasses, they are called "abstract".

- Abstract classes cannot be instantiated, but it can be inherited from, acting as a pattern for subclasses

- Abstract classes can still have a constructor and this can be referred to using `super()`. This is particularly useful for when all subclasses share a name or ID

- They can contain a mix of abstract and concrete methods

  - Abstract methods provide the method signature, but not the implementation. All abstract methods must be implemented by any subclasses.
  - Concrete/non-abstract methods are ones which will be the same for all subclasses, so they are fully defined, and will just be inherited as usual into the subclasses

- Typically, abstract classes are used to "provide partial implementation"

- Example:

  ```java
  abstract class GraphicObject {
      //Member variables
      private int x, y;
      
      //Optional constructor, default no arg constructor is inferred otherwise.
      
      //A concrete method
      public void moveTo(int newX, int newY) {
          this.x = newX;
          this.y = newY;
      }
      
      //Abstract method
      public abstract void draw();
  }
  
  class Circle extends GraphicObject {
      private int radius;
      
      //Constructor
      public Circle(int radius) {
          this.radius = radius;
      }
      
      //moveTo already implemented through inheritance
      
      public void draw() {
          //Implementation must be provided
          System.out.println("Circle");
      }
  }
  ```

- Additional links:

  - https://www.tutorialspoint.com/java/java_abstraction.htm
  - https://www.journaldev.com/1582/abstract-class-in-java
  - https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html

## Interfaces

- If an abstract class contains *only* abstract methods, and no concrete implementations of methods, it is called an "interface"

  - Hence, interfaces have similar properties to abstract classes, including not being able to be instantiated, so having no constructor

- Whilst only one class can be inherited, multiple interfaces can be "implemented". This is to avoid the issue of two superclasses providing different implementations for the same method. The `implements` keyword is used instead of the `extends` one to indicate inheritance/implementation

- Concrete classes implement interfaces, but interfaces can still extend other interfaces to inherit from them

- Typically, interfaces are used to "encapsulate a small subset of functionality"

- Example

  ```java
  interface Foo {
      //No constructor
      
      //Only providing method signatures without implementations
      public void doFoo();
      public void undoFoo();
  }
  
  class Bar implements Foo {
      private int x;
      
      //Provide implementations for all of the interface methods
      public void doFoo() {
          x++;
      }
      
      public void undoFoo() {
          x--;
      }
  }
  ```

- Additional links:

  - https://www.w3schools.com/java/java_interface.asp
  - https://docs.oracle.com/javase/tutorial/java/concepts/interface.html

## Enumerated types

- A special type of class that hold a range of constant values, which can be used instead of defining a large number of final variables

- Example

  ```java
  enum Level {
    LOW,
    MEDIUM,
    HIGH
  }
  
  Level myVar = Level.MEDIUM; 
  ```

- Additional links:

  - https://www.w3schools.com/java/java_enums.asp
  - https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html





# Exceptions

- The `try..catch..finally` statement
  - The code in the `try` block is executed. If an exception is generated, the appropriate `catch` block is executed
  - Multiple `catch` blocks can be used, but they act like a `switch` statement, moving downwards, selecting the first to match, so if a subclass lies below a superclass, it will never be executed
  - The `finally` block is run irrespective of whether the `catch` was executed, even if there is a `return` within one of the blocks
- The `throws` keyword can be added to a method declaration to indicate that it might "throw" a checked exception of a given type
- The `throw` keyword is used to throw a new exception from a method, and must be followed by an object that is an instance of the Throwable class (e.g. `throw new RuntimeException`)
- The "Exceptions hierarchy" (image credit [GeeksForGeeks](https://www.geeksforgeeks.org/exceptions-in-java/))
  ![exceptionsDiagram](C:\Users\egood\Desktop\dcs-notes.github.io\media\exceptionsDiagram.png)
  - In Java all errors and exceptions are subclasses of the `Throwable` class
    - Errors are "serious problem that a reasonable application should not try to catch". These are rare, so we will largely ignore them
    - Exceptions are "conditions that a reasonable application might want to catch"
  - There are two types of exceptions
    - Checked exceptions, which must be caught or re-thrown for the code to compile
    - Unchecked exceptions are subclasses of either `Error` or `RuntimeException`. They do not need to be specifically thrown, and while they can be caught, they don't need to be for compilation
  - Custom exceptions can be written
    - The exception should inherit from the most specific exception class that encapsulates it
      - Must be a subclass of `Throwable`
      - Any subclass of `Exception` will be checked, and any subclass of `RunTimeException` will be unchecked
    - Normally, they are just defined as inheriting from the superclass exception, then overriding the polymorphic constructors to provide appropriate error messages etc. All methods such as `printStackTrace` are then inherited from the superclass.

# Generics

- Generics allow code to enforce strong type checking at compile time, which helps avoid errors such as invalid casting at runtime. Additionally, they minimise the number of type casts needed, as they automatically return data of the specified type, and they allow writing generic algorithms to be tailored to different types
- Angle brackets are used to indicate the variables that will be used, e.g. `new MyClass<String>`
- Type placeholders are used in the class definition to receive which class the generic is instantiated with, e.g. `public class MyClass<V>`
  - The naming convention for the placeholders is a single uppercase letter, often of the start of what it refers to, e.g. E from element, or V from value
  - The type placeholder is then used instead of a concrete type e.g. `Object` throughout the class
- Primitives cannot be provided as generics, as they are not objects. Instead, we need to use their object wrapper (e.g. `int` becomes `Integer`). Then, if an integer is passed in as a parameter, it will be "autoboxed" into its object wrapper to be handled internally.
- Types of generics can be restricted by using the extends keyword in the instantiation, e.g. `public class MyClass<V extends Number>`
- During the process of converting Java code to bytecode, the type information about objects is checked for errors, but ultimately discarded in the bytecode. For generics, the type checking before discarding the types is performed by substituting all of the instances of type variables with the type they are specified with in the instance in the code, so it can be type-checked, raising errors at compile time, not runtime.
