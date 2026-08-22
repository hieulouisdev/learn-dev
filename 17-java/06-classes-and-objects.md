# Lesson 06: Classes and Objects

> Module: Java · Lesson 6 of 7
> Estimated time: 60–90 minutes

This is the biggest lesson in the entire Java module, and for good reason: Java is an object-oriented language, and classes are how Java expresses object orientation. Everything you have written so far — `public class HelloWorld`, `public static void main` — has used classes, but only as containers for static methods. In this lesson we make the leap from "classes as containers" to "classes as blueprints for objects" — which is what classic OOP is all about.

We will cover the full OOP stack: classes, fields, methods, constructors, the `this` keyword, access modifiers (public, private, protected, package-private), getters and setters, the `static` keyword revisited, inheritance with `extends` and `super`, polymorphism with method overriding and `@Override`, interfaces (Java's answer to multiple inheritance), and abstract classes. This is a lot of material — take it slowly, type out every example, and do not be afraid to re-read sections. By the end you will have the conceptual vocabulary to read almost any Java codebase, including Spring, JUnit, and the standard library itself.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define a class with fields, constructors, and methods, and create objects from it with `new`.
2. Use access modifiers (`public`, `private`, `protected`, package-private) correctly to enforce encapsulation.
3. Use inheritance (`extends`, `super`) and method overriding (`@Override`) to model "is-a" relationships.
4. Define interfaces, implement them in classes, and explain why Java allows multiple interface implementation but only single class inheritance.

---

## 1. Classes and Objects

A **class** is a blueprint for objects. An **object** (also called an **instance**) is a concrete value created from that blueprint. Here is a minimal class:

```java
public class Person {
    String name;       // field — variable inside the class
    int age;           // field

    // Method — function inside the class
    public String greet() {
        return "Hello, my name is " + name + " and I am " + age + " years old.";
    }
}
```

This class defines what every `Person` has (a `name` field and an `age` field) and what every `Person` can do (a `greet` method). To create an actual `Person` object, you use the `new` keyword:

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Person();   // create a new Person object
        p.name = "Alice";          // set the name field
        p.age = 30;                 // set the age field
        System.out.println(p.greet());   // Hello, my name is Alice and I am 30 years old.

        Person q = new Person();   // another object, independent
        q.name = "Bob";
        q.age = 25;
        System.out.println(q.greet());   // Hello, my name is Bob and I am 25 years old.
    }
}
```

Here is what happens when you write `Person p = new Person();`:

- `new Person()` allocates memory on the heap for a new `Person` object.
- The JVM initializes the fields to their defaults (`name` is `null`, `age` is `0`).
- The object's address is stored in the variable `p`.

Each `new Person()` creates a separate, independent object. Modifying `p.name` does not affect `q.name` — they are different objects. The variable `p` is a reference (a pointer) to the object, not the object itself.

This is the same model as in Python (`p = Person()`) and JavaScript (`const p = new Person()`), but Java is stricter about types. The variable `p` is declared as type `Person`, and you can only call `Person` methods on it. If `Person` had a private field, you could not access it from outside the class — that is encapsulation, which we cover in Section 5.

The class definition goes in a file named `Person.java` (the filename must match the `public class` name). The `Main` class goes in `Main.java`. You compile both with `javac *.java` (the `*` matches all `.java` files in the folder), then run with `java Main`.

---

## 2. Fields and Methods

**Fields** (also called **instance variables** or **member variables**) are variables declared inside a class but outside any method. Each object has its own copy of every field — when you create two `Person` objects, each gets its own `name` and `age`. Fields are typically declared `private` (we will explain why in Section 5) and at the top of the class body:

```java
public class BankAccount {
    private String owner;
    private double balance;
}
```

**Methods** are functions declared inside the class. Instance methods (non-static methods) operate on a specific object — they can read and write that object's fields. The `greet()` method in `Person` reads the `name` and `age` fields of "the current object" — the one the method was called on. When you call `p.greet()`, `name` inside `greet` refers to `p`'s name. When you call `q.greet()`, the same code refers to `q`'s name. This implicit "current object" is called `this`.

Together, fields and methods are called the **members** of the class. Fields hold state (data); methods define behavior. Java's strong typing means the compiler tracks the type of every field and the signature of every method — you cannot accidentally assign a `String` to an `int` field or call a method that does not exist.

There is one more category: **static members**, which we cover in Section 6. Static members belong to the class itself, not to any instance.

---

## 3. Constructors

A **constructor** is a special method that runs when you create an object with `new`. Its job is to initialize the object's fields. Constructors look like methods but with two key differences: they have no return type (not even `void`), and their name MUST match the class name exactly.

```java
public class Person {
    private String name;
    private int age;

    // Constructor — same name as class, no return type
    public Person(String name, int age) {
        this.name = name;     // `this.name` is the field; `name` is the parameter
        this.age = age;
    }

    public String greet() {
        return "Hello, my name is " + name + " and I am " + age + " years old.";
    }
}
```

Usage:

```java
Person p = new Person("Alice", 30);   // calls the constructor with "Alice" and 30
System.out.println(p.greet());        // Hello, my name is Alice and I am 30 years old.
```

The constructor takes `name` and `age` as parameters and uses them to initialize the object's fields. The `this` keyword refers to "the current object" — `this.name` is the field, `name` (without `this`) is the parameter. Because both have the same name, you MUST use `this` to disambiguate. If the parameter had a different name (`String n`), you could write `name = n;` without `this`.

If you do not define ANY constructor, Java gives you a **default no-argument constructor** automatically — `public Person() { }` that does nothing. The fields get their default values (`null`, `0`, `false`). That is why `new Person()` worked in Section 1 even though we had not defined a constructor. BUT: the moment you define ANY constructor (with parameters), Java STOPS providing the no-arg constructor. If you then write `new Person()` with no arguments, you get a compile error. The fix is to explicitly write a no-arg constructor if you need one:

```java
public class Person {
    private String name;
    private int age;

    // No-arg constructor — explicitly defined
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }

    // Parameterized constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

This is **constructor overloading** — multiple constructors with different parameter lists, just like method overloading. A common pattern is to have one "real" constructor that takes all fields, and have the other constructors call it with default values using `this(...)`:

```java
public Person() {
    this("Unknown", 0);    // calls Person(String, int) with defaults
}

public Person(String name) {
    this(name, 0);         // calls Person(String, int) with default age
}

public Person(String name, int age) {
    this.name = name;
    this.age = age;
}
```

The `this(...)` call must be the **first statement** in the constructor. This pattern lets you centralize initialization logic in one constructor and have others delegate to it.

---

## 4. Access Modifiers

Java has four access levels, controlled by keywords placed before classes, fields, methods, and constructors. The access level determines which other code can see and use that member. The four levels, from most to least permissive:

| Modifier        | Same Class | Same Package | Subclass (Different Package) | World |
|-----------------|:----------:|:------------:|:----------------------------:|:-----:|
| `public`        |     ✓      |       ✓      |              ✓               |   ✓   |
| `protected`     |     ✓      |       ✓      |              ✓               |   ✗   |
| *(no modifier)* |     ✓      |       ✓      |              ✗               |   ✗   |
| `private`       |     ✓      |       ✗      |              ✗               |   ✗   |

- **`public`** — accessible from anywhere. The `main` method is `public` so the JVM (which is outside your class) can call it. The `String` class is `public` so anyone can use it.
- **`protected`** — accessible within the same package AND by subclasses (even in different packages). Used for things that subclasses should be able to override or call but unrelated code should not.
- **(no modifier)** — also called **package-private** or **default**. Accessible only within the same package. This is what you get if you write no modifier at all. It is more restrictive than `protected` (subclasses in different packages cannot access it).
- **`private`** — accessible only inside the same class. Not even other instances of the same class can directly access another instance's private fields (though this rarely matters, since methods of the same class can access private members of any instance of that class).

The convention for fields is **always `private`**. This is called **encapsulation** — the internal state of an object is hidden from outside code, and access is mediated through methods (getters and setters). Why? Because if you later want to validate (e.g., "age must be non-negative"), you can add validation in the setter without breaking any code that uses your class. If the field were `public`, every line of code in the codebase could set it directly, and you would have to find them all and change them.

For classes (top-level, not nested), only `public` or package-private is allowed. A `public class` is visible everywhere; a package-private class is visible only within its own package. You cannot have a `private` top-level class — privacy only makes sense for members of a class.

---

## 5. Getters and Setters

**Encapsulation** is the practice of keeping fields `private` and exposing controlled access through methods. The convention is:

- A **getter** for a field `name` is a method named `getName()` that returns the field's value. For `boolean` fields, the convention is `isName()` (not `getName()`).
- A **setter** for a field `name` is a method named `setName(String name)` that takes a new value and assigns it to the field.

```java
public class Person {
    private String name;
    private int age;
    private boolean active;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        this.active = true;
    }

    // Getter for name
    public String getName() {
        return name;
    }

    // Setter for name
    public void setName(String name) {
        this.name = name;
    }

    // Getter for age (with validation!)
    public int getAge() {
        return age;
    }

    // Setter for age — validates input
    public void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        this.age = age;
    }

    // Getter for boolean uses `is` prefix
    public boolean isActive() {
        return active;
    }

    public void setActive(boolean active) {
        this.active = active;
    }
}
```

Now external code cannot set `age` to a negative number — the setter throws an exception. Without encapsulation, you would have to find every place in the codebase that sets `age` and add a check. With encapsulation, the check lives in one place.

```java
Person p = new Person("Alice", 30);
p.setAge(31);                 // OK
p.setAge(-5);                 // throws IllegalArgumentException
p.name = "Bob";               // COMPILE ERROR — name is private
String n = p.getName();       // OK — public method
```

Getters and setters are tedious to write, and IDEs like IntelliJ generate them automatically (right-click → Generate → Getter and Setter). Modern Java (Java 14+) introduces **records** as a shortcut for simple data-holder classes — they generate the constructor, getters, `equals`, `hashCode`, and `toString` for you. We mention records in the cheatsheet.

Not every field needs a setter. If a field should be set once at construction and never changed, make it `private final` and provide only a getter. This is called an **immutable** field — read-only after construction. The `String` class is fully immutable, which is why strings are safe to share between threads.

---

## 6. `static` Members

A **static member** (field or method) belongs to the **class itself**, not to any particular instance. All instances of the class share the same static field. You access static members via the class name (`ClassName.member`), not via an instance.

```java
public class Counter {
    private static int instanceCount = 0;   // shared by all instances
    private int id;

    public Counter() {
        instanceCount++;                     // increment shared counter
        this.id = instanceCount;             // each instance gets a unique id
    }

    public int getId() {
        return id;
    }

    // Static method — callable without an instance
    public static int getInstanceCount() {
        return instanceCount;
    }
}

public class Main {
    public static void main(String[] args) {
        Counter a = new Counter();
        Counter b = new Counter();
        Counter c = new Counter();

        System.out.println(a.getId());                          // 1
        System.out.println(b.getId());                          // 2
        System.out.println(c.getId());                          // 3
        System.out.println(Counter.getInstanceCount());         // 3 — shared counter
    }
}
```

The `instanceCount` field is shared — every constructor call increments the same counter. The `getInstanceCount` static method accesses it without needing an instance. This is exactly how `Math.sqrt` works — `Math` is a class, `sqrt` is a static method, you call `Math.sqrt(16.0)` without ever writing `new Math()`.

Static fields are sometimes used as **constants**: `public static final double PI = 3.14159;`. The `final` keyword means the value cannot be reassigned after initialization. `public static final` makes it a class-level constant visible everywhere. Java's `Math.PI` is `public static final double PI = 3.141592653589793;` — exactly this pattern.

Static methods cannot access non-static fields or methods directly — they have no `this`. From a static method, `name` (an instance field) is meaningless: which object's `name`? You can only access other static members of the same class directly. This is why `main` (which is static) cannot directly call instance methods — you must first create an object with `new`, then call the method on it.

---

## 7. Inheritance

**Inheritance** lets one class inherit the fields and methods of another. The class being inherited from is the **parent class** (also called **superclass** or **base class**); the class that inherits is the **child class** (also called **subclass** or **derived class**). Java uses the `extends` keyword:

```java
public class Person {
    protected String name;
    protected int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String greet() {
        return "Hello, I am " + name;
    }
}

// Student IS-A Person, plus more
public class Student extends Person {
    private String school;

    public Student(String name, int age, String school) {
        super(name, age);          // call the parent's constructor
        this.school = school;
    }

    public String getSchool() {
        return school;
    }
}

public class Main {
    public static void main(String[] args) {
        Student s = new Student("Alice", 20, "MIT");
        System.out.println(s.greet());      // "Hello, I am Alice" — inherited from Person!
        System.out.println(s.getSchool());  // "MIT"
    }
}
```

A few important things to notice:

- `Student extends Person` — `Student` is a subclass of `Person`. `Student` automatically has the `name` and `age` fields and the `greet` method, even though they are not written in `Student`. You can call `s.greet()` because `Student` inherited it.
- The first line of a subclass constructor MUST be `super(...)`. This calls the parent's constructor to initialize the inherited fields. If you forget, the compiler inserts `super();` implicitly — but if the parent has no no-arg constructor, the compile fails. So always be explicit.
- `protected` fields are visible to subclasses (even in different packages) — that is why we used `protected` for `name` and `age` instead of `private`.
- A subclass inherits everything `public` and `protected` from its parent, but does NOT inherit `private` members (it cannot see them directly).

**Java allows only SINGLE class inheritance.** A class can `extend` at most ONE parent class. There is no multiple inheritance like in Python or C++. The reason is the "diamond problem": if a class inherits from two parents that both define the same method, the compiler does not know which one to use. Java avoids the problem entirely by forbidding multiple class inheritance. Interfaces (Section 9) provide a workaround — a class can implement MULTIPLE interfaces.

Every class in Java implicitly extends `java.lang.Object` if it does not extend anything else. `Object` provides methods like `equals`, `hashCode`, and `toString` that every class inherits. When you write `p.toString()`, you are calling a method inherited from `Object` (or overridden — see the next section).

---

## 8. Polymorphism

**Polymorphism** means "many forms" — the same code can work with objects of different types, as long as they share a common parent (or interface). The classic example: a variable of type `Person` can hold a `Person` OR a `Student` (because a `Student` IS-A `Person`):

```java
Person p = new Student("Alice", 20, "MIT");   // OK — Student is a Person
System.out.println(p.greet());                 // calls Student's greet if overridden
// p.getSchool();                              // COMPILE ERROR — Person doesn't have getSchool
```

This is **upcasting** — treating a `Student` as a `Person`. The compiler only lets you call methods that exist on `Person` (the declared type), even if the actual object is a `Student`. To call `getSchool`, you must **downcast** explicitly:

```java
Student s = (Student) p;   // cast Person back to Student
System.out.println(s.getSchool());   // "MIT"
```

Downcasting is dangerous — if `p` is actually a `Person` (not a `Student`), the cast throws `ClassCastException`. Modern Java uses `instanceof` to check first:

```java
if (p instanceof Student) {
    Student s = (Student) p;   // safe cast
    System.out.println(s.getSchool());
}

// Java 16+ pattern matching for instanceof:
if (p instanceof Student s) {
    System.out.println(s.getSchool());   // `s` is already cast — no explicit cast needed
}
```

**Method overriding** is the key mechanism that makes polymorphism useful. A subclass can provide its own version of a method inherited from its parent:

```java
public class Student extends Person {
    private String school;

    public Student(String name, int age, String school) {
        super(name, age);
        this.school = school;
    }

    @Override   // annotation — tells compiler we mean to override
    public String greet() {
        return "Hi, I'm " + name + ", a student at " + school;
    }
}

// Usage:
Person p = new Student("Alice", 20, "MIT");
System.out.println(p.greet());   // "Hi, I'm Alice, a student at MIT"
// Runtime decides which greet() to call based on the actual object type, not the variable type
```

The `@Override` annotation is optional but recommended. It tells the compiler "I intend to override a parent method; if I mistyped the name or got the signature wrong, give me a compile error." Without `@Override`, a typo (e.g., `great()` instead of `greet()`) would silently create a NEW method instead of overriding the parent's, and your code would mysteriously call the parent's version.

**Runtime dispatch** (also called dynamic dispatch or virtual method dispatch) is how Java decides which `greet` to call. The JVM looks at the actual type of the object (`Student`, not `Person`) and calls the most-derived override. This is why `p.greet()` calls `Student.greet` even though `p`'s declared type is `Person`. This is polymorphism in action — the same call (`p.greet()`) does different things depending on the actual object.

You cannot override `static` methods (they belong to the class, not the instance — there is nothing to dispatch). You cannot override `final` methods (the `final` keyword forbids overriding). You cannot override `private` methods (they are not visible to subclasses).

---

## 9. Interfaces

An **interface** is a contract: a list of method signatures that any implementing class MUST provide. Interfaces look like classes with no bodies — just method declarations:

```java
public interface Greeter {
    void greet();    // no body — just the signature
}

public interface Named {
    String getName();
}
```

A class **implements** an interface using the `implements` keyword. It must provide bodies for every method in the interface:

```java
public class Person implements Greeter, Named {   // implement BOTH interfaces
    private String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public void greet() {              // required by Greeter
        System.out.println("Hello, I am " + name);
    }

    @Override
    public String getName() {          // required by Named
        return name;
    }
}
```

Notice that a class can implement **multiple** interfaces (separated by commas) — this is how Java gets around the single-inheritance rule. You can only `extends` ONE class, but you can `implements` any number of interfaces.

Why interfaces? They let you write code that works with any class that fulfills a contract, regardless of where it sits in the class hierarchy:

```java
public class Main {
    // Takes any Greeter — Person, Robot, Cat, whatever implements Greeter
    public static void sayHello(Greeter g) {
        g.greet();
    }

    public static void main(String[] args) {
        Person p = new Person("Alice");
        sayHello(p);   // OK — Person implements Greeter
    }
}
```

The `sayHello` method does not care whether `g` is a `Person`, a `Robot`, or a `Cat` — it only requires that `g` has a `greet()` method. This is the most powerful form of polymorphism in Java, and it is the basis of every major Java framework. Spring's `@RestController` is essentially a marker interface. `java.util.List` is an interface — `ArrayList` and `LinkedList` both implement it, and you can write code that takes a `List` and works with either.

### Default Methods (Java 8+)

Java 8 (2014) added **default methods** to interfaces — methods with a body that subclasses inherit by default but can override:

```java
public interface Greeter {
    void greet();

    // Default method — has a body, subclasses inherit it
    default void greetLoudly() {
        System.out.print(">>> ");
        greet();                          // calls the abstract greet above
        System.out.println(" <<<");
    }
}

public class Person implements Greeter {
    @Override
    public void greet() {
        System.out.println("Hello");
    }
    // Person does NOT have to implement greetLoudly — it gets the default
}

Person p = new Person();
p.greet();        // Hello
p.greetLoudly();  // >>> Hello <<<
```

Default methods let interface authors add new methods without breaking existing implementations — the existing classes get a default behavior. They are how Java's Collections Framework got the new `stream()` method in Java 8 without rewriting every `List` implementation in the world.

---

## 10. Abstract Classes

An **abstract class** sits between a regular class and an interface. Like an interface, it cannot be instantiated directly (you cannot `new AbstractClass()`). Like a regular class, it can have fields, constructors, and concrete (non-abstract) methods. It can also have **abstract methods** — method declarations with no body that subclasses must implement.

```java
public abstract class Shape {
    protected String name;

    public Shape(String name) {
        this.name = name;
    }

    // Concrete method — subclasses inherit it as-is
    public String describe() {
        return "A " + name + " with area " + area();
    }

    // Abstract method — subclasses MUST implement
    public abstract double area();
}

public class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        super("Circle");
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

public class Rectangle extends Shape {
    private double width, height;

    public Rectangle(double width, double height) {
        super("Rectangle");
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }
}

// Usage:
Shape s = new Circle(5.0);
System.out.println(s.describe());   // "A Circle with area 78.53981633974483"
```

A few notes:

- `Shape s = new Circle(5.0);` is upcasting again — a `Circle` IS-A `Shape`, so a `Shape` variable can hold one.
- `s.area()` calls `Circle.area()` at runtime (dynamic dispatch) — even though `Shape.area()` is abstract (has no body).
- You CANNOT write `new Shape("Foo")` — abstract classes cannot be instantiated. You must create a concrete subclass.
- Abstract classes can have constructors (they run when a subclass is created via `super(...)`).

When to use an abstract class vs an interface? Use an **abstract class** when subclasses share state (fields) or significant implementation (concrete methods that subclasses will use). Use an **interface** when you just want to define a contract (a set of method signatures) without sharing state. Java's standard library uses both: `java.util.List` is an interface (no state, just methods); `java.util.AbstractList` is an abstract class (provides default implementations of some methods).

---

## Try It Yourself

Before moving on, do these three exercises. They should take 10–15 minutes each. This is the most important lesson in the module — take the time.

### Exercise 1: A `BankAccount` Class

Create a `BankAccount` class with a `private double balance` field, a constructor that takes an initial balance, and methods `deposit(double amount)` and `withdraw(double amount)`. Both methods should validate that the amount is positive (`if (amount <= 0) throw new IllegalArgumentException(...)`) and `withdraw` should refuse to overdraft (throw if `amount > balance`). Add a `getBalance()` getter. In `main`, create an account, deposit 100, withdraw 30, try to withdraw 200 (you should see an exception), and print the final balance. Hint: catch the exception with a `try/catch` block (we have not covered exceptions formally — for now, just let the program crash and read the error message).

### Exercise 2: Inheritance — `SavingsAccount extends BankAccount`

Extend your `BankAccount` from Exercise 1. Create a `SavingsAccount` subclass that has a `private double interestRate` field (e.g., `0.05` for 5%). Its constructor should take an initial balance AND an interest rate, calling `super(balance)` to initialize the parent. Add an `applyInterest()` method that deposits `balance * interestRate` into the account (use `getBalance()` to read the balance, then call `deposit(...)` to add the interest). In `main`, create a `SavingsAccount` with $1000 and 5% interest, call `applyInterest()`, and confirm the new balance is $1050.

### Exercise 3: An Interface — `Comparable`

Define an interface `Comparable` with a method `int compareTo(Object other)` that returns negative if `this < other`, zero if equal, positive if `this > other`. (This is exactly `java.lang.Comparable`, but you are writing your own version.) Make `Person` (from earlier in the lesson) implement it: compare by age. Create three `Person` objects with different ages, then loop through them and print who is older. Finally, write a static method `Person findOldest(Person[] people)` that uses `compareTo` to find the oldest — without knowing anything about `Person`'s internal fields. This is polymorphism: the method works through the interface.

---

## Common Mistakes

### Mistake 1: Forgetting `new` when creating an object

```java
// WRONG — Person is a class, you must use `new` to create an object
Person p = Person("Alice", 30);   // COMPILE ERROR
```

```java
// RIGHT — `new` allocates the object and calls the constructor
Person p = new Person("Alice", 30);
```

In Java, you cannot call a constructor like a regular method. You MUST use the `new` keyword, which allocates memory on the heap, initializes the object's fields to defaults, then runs the constructor. Forgetting `new` gives `cannot find symbol: method Person(String,int)` — the compiler thinks you are trying to call a regular method called `Person`, which does not exist. The fix is to add `new` before the class name. This is different from Python (`p = Person("Alice", 30)` — no `new`) and similar to JavaScript (`new Person(...)` — with `new`) and C++ (with `new` for heap allocation, without for stack). When in doubt, always use `new` in Java.

### Mistake 2: Accessing private fields directly from outside the class

```java
public class Person {
    private String name;
    public Person(String name) { this.name = name; }
}

public class Main {
    public static void main(String[] args) {
        Person p = new Person("Alice");
        // System.out.println(p.name);   // COMPILE ERROR — name is private
    }
}
```

```java
// RIGHT — use a public getter method
public class Person {
    private String name;
    public Person(String name) { this.name = name; }
    public String getName() { return name; }
}

public class Main {
    public static void main(String[] args) {
        Person p = new Person("Alice");
        System.out.println(p.getName());   // OK — public method
    }
}
```

A `private` field is invisible outside the class that declares it. If you write `p.name`, the compiler stops with `name has private access in Person`. The fix is to add a public getter method (`getName()`) that returns the field's value. This is encapsulation — the field is hidden, but controlled access is provided through methods. The getter can do extra work if needed (lazy initialization, logging, validation), and the field's type can change later without breaking callers. If you genuinely need direct field access, use a less restrictive modifier (`protected`, package-private, or `public`), but for fields the default should always be `private`.

### Mistake 3: Forgetting `super(...)` in a subclass constructor

```java
public class Person {
    private String name;
    public Person(String name) { this.name = name; }
    // NO no-arg constructor!
}

public class Student extends Person {
    private String school;
    // COMPILE ERROR — implicit super() cannot find Person() (no-arg constructor)
    public Student(String school) {
        this.school = school;
    }
}
```

```java
// RIGHT — explicitly call super(...) with the required arguments
public class Student extends Person {
    private String school;
    public Student(String name, int age, String school) {
        super(name);                  // call Person(String) — MUST be first statement
        this.school = school;
    }
}
```

Every subclass constructor must call a parent constructor as its FIRST statement. If you do not write an explicit `super(...)` call, the compiler inserts `super();` (a no-arg call) implicitly. This implicit call works ONLY if the parent has a no-arg constructor. If the parent has only parameterized constructors (like `Person(String name)`), the implicit `super()` call fails to find a matching constructor, and the compiler stops with `constructor Person in class Person cannot be applied to given types`. The fix is to explicitly call `super(name, age)` (or whatever arguments the parent's constructor needs) as the first line of the subclass constructor. Note that `super(...)` MUST be the first statement — you cannot do `this.school = school; super(name, age);`.

### Mistake 4: Confusing `extends` (class) with `implements` (interface)

```java
// WRONG — using `extends` for an interface
public interface Greeter { void greet(); }
public class Person extends Greeter {   // COMPILE ERROR — interfaces are implemented, not extended
    public void greet() { System.out.println("Hi"); }
}
```

```java
// RIGHT — use `implements` for an interface, `extends` for a class
public interface Greeter { void greet(); }
public class Person implements Greeter {   // OK
    public void greet() { System.out.println("Hi"); }
}
// And for class inheritance:
public class Student extends Person { ... }   // extends a class
```

Java uses two different keywords for two different relationships. `extends` is for **class inheritance** — a subclass extends ONE parent class. `implements` is for **interface implementation** — a class can implement many interfaces (separated by commas). You cannot `extends` an interface from a class (compile error: `no interface expected here`), and you cannot `implements` a class (compile error: `interface expected here`). The rule is simple: classes are extended, interfaces are implemented. (An interface can extend another interface using `extends` — that is the one exception, and it just adds to the contract.) When you see a compile error like this, check whether you used the right keyword for the type you are inheriting from.

---

## Summary

- A **class** is a blueprint; an **object** (instance) is a concrete value created with `new`. Fields hold state, methods define behavior. Together they are the "members" of the class.
- **Constructors** initialize objects. They have no return type and the name MUST match the class. If you define any constructor, Java stops providing the no-arg default — write one explicitly if you need it. Use `this(...)` to delegate between constructors.
- **Access modifiers**: `public` (everywhere), `protected` (same package + subclasses), package-private / no modifier (same package only), `private` (same class only). Always make fields `private` (encapsulation).
- **Getters and setters** (`getName()`, `setName(...)`) expose `private` fields in a controlled way. Setters can validate input; getters can compute on demand. IDEs generate them automatically. Boolean getters use the `is` prefix (`isActive()`).
- **`static`** members belong to the class, not instances. Static fields are shared by all instances; static methods can be called via `ClassName.method()` without an object. Static methods cannot access instance fields directly (no `this`).
- **Inheritance** (`extends`) lets a subclass inherit fields and methods from a parent. Java allows ONLY single class inheritance (no multiple parents). Use `super(...)` as the first statement of a subclass constructor to call the parent's constructor. Every class implicitly extends `Object` if it extends nothing else.
- **Polymorphism** means the same code works with objects of different types sharing a parent or interface. Upcast implicitly (`Person p = new Student()`); downcast explicitly with a check (`if (p instanceof Student s)`).
- **Method overriding** (`@Override`) lets a subclass provide its own version of an inherited method. Runtime dispatch picks the right version based on the actual object type. Always use the `@Override` annotation to catch typos.
- **Interfaces** define a contract (a set of method signatures). A class `implements` MULTIPLE interfaces (Java's workaround for single inheritance). Java 8+ supports `default` methods in interfaces (with bodies).
- **Abstract classes** cannot be instantiated directly; they can have fields, constructors, concrete methods, AND abstract methods (declarations without bodies that subclasses must implement). Use abstract classes when subclasses share state; use interfaces when you just need a contract.

You have now covered the heart of Java's object model. Almost every Java framework you encounter — Spring, Hibernate, JUnit, the standard library — uses these concepts heavily. In Lesson 07 we provide a one-page cheatsheet that summarizes the entire module for quick reference.

---

**Next:** [Lesson 07: Java Cheatsheet →](./07-java-cheatsheet.md)
