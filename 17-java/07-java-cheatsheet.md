# Lesson 07: Java Cheatsheet

> Module: Java · Lesson 7 of 7
> Estimated time: 30–45 minutes (or use as a reference)

This lesson is a one-page summary of everything you learned in Lessons 01 through 06. Use it as a quick reference when you are writing Java and need to remember the exact syntax for a `for` loop, the difference between `==` and `.equals()`, or which access modifier does what. Skim it now to cement the concepts in your memory, then bookmark it for later.

This is the final lesson in the Java module. After this cheatsheet, the "What's Next?" section points you to the next module in the curriculum — C++ — which builds on Java's syntax and OOP model while adding manual memory management and templates.

---

## Learning Objectives

After this lesson, you will be able to:

1. Quickly recall Java syntax for any common operation (variables, control flow, methods, classes).
2. Identify the 10 most-used standard library packages and know what each provides.
3. Recognize and avoid the most common Java pitfalls (`==` vs `.equals()`, integer division, pass-by-value, fixed-size arrays).
4. Decide what to learn next based on what Java topics are NOT covered in this module.

---

## 1. Hello, World!

The minimal Java program:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Save as `HelloWorld.java` (filename MUST match class name). Compile with `javac HelloWorld.java` (produces `HelloWorld.class` bytecode). Run with `java HelloWorld`.

---

## 2. JDK Commands

| Command   | Purpose                                                            |
|-----------|-------------------------------------------------------------------|
| `javac`   | Compiler — turns `.java` source into `.class` bytecode.            |
| `java`    | JVM launcher — runs `.class` bytecode.                              |
| `jar`     | Archives `.class` files into a single `.jar` (Java archive).        |
| `javadoc` | Generates HTML documentation from `/** ... */` comments.            |
| `jshell`  | Interactive REPL (Java 9+) for experimenting with snippets.        |
| `jdb`     | Java debugger (similar to `gdb` for C/C++).                        |
| `jps`     | Lists running Java processes (useful for killing stuck JVMs).       |

The most common day-to-day commands are `javac` (compile) and `java` (run). `jshell` is great for trying out syntax without creating a whole file — just type `jshell` in your terminal and start typing Java expressions.

---

## 3. Primitive Types

| Type      | Size      | Range                                            | Default  | Literal Example        |
|-----------|-----------|--------------------------------------------------|-----------|------------------------|
| `byte`    | 8 bits    | -128 to 127                                      | 0         | `(byte) 42`            |
| `short`   | 16 bits   | -32,768 to 32,767                                | 0         | `(short) 1000`         |
| `int`     | 32 bits   | -2,147,483,648 to 2,147,483,647                  | 0         | `42`, `1_000_000`      |
| `long`    | 64 bits   | -9.2×10^18 to 9.2×10^18                          | 0L        | `42L`                  |
| `float`   | 32 bits   | ~6-7 sig digits                                  | 0.0f      | `3.14f`                |
| `double`  | 64 bits   | ~15-16 sig digits                                | 0.0       | `3.14`, `3.14d`        |
| `char`    | 16 bits   | 0 to 65,535 (UTF-16 code unit)                   | `'\u0000'`| `'A'`, `'\n'`, `'\u03A9'` |
| `boolean` | 1 bit*    | `true` or `false`                                | `false`   | `true`, `false`        |

`int` is the default integer type (literal `5` is `int`). `double` is the default floating-point type (literal `3.14` is `double`). Use `L` suffix for `long` literals, `f` for `float` literals. Underscores are allowed for readability: `1_000_000`.

---

## 4. Reference Types

| Type        | Description                                                   | Example                          |
|-------------|---------------------------------------------------------------|----------------------------------|
| `String`    | Immutable text, lives on heap                                 | `String s = "hello";`            |
| Array       | Fixed-length collection, lives on heap                        | `int[] nums = {1, 2, 3};`        |
| Wrapper     | Object version of primitive (for collections/generics)      | `Integer x = 5;` (autoboxed)     |
| Class       | Anything you write with `class`                               | `Person p = new Person();`       |
| Interface   | A contract — `interface Foo { void bar(); }`                 | `class C implements Foo { ... }` |

Reference variables hold a pointer to the heap object, not the object itself. `null` is a valid value for any reference type.

---

## 5. Operators

**Arithmetic**: `+`, `-`, `*`, `/`, `%` (modulo), `++` (increment), `--` (decrement).
- `int / int` returns `int` (integer division — `5 / 2 == 2`).
- `%` returns the remainder — `7 % 3 == 1`.

**Relational**: `==`, `!=`, `<`, `>`, `<=`, `>=`. Return `boolean`.

**Logical**: `&&` (AND, short-circuits), `||` (OR, short-circuits), `!` (NOT).

**Bitwise** (rare in everyday code): `&`, `|`, `^` (XOR), `~` (NOT), `<<` (left shift), `>>` (right shift with sign), `>>>` (unsigned right shift).

**Assignment**: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`.

**Ternary**: `cond ? valueIfTrue : valueIfFalse`.

---

## 6. Control Flow

### `if` / `else if` / `else`

```java
if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else {
    grade = 'F';
}
```

Conditions MUST be `boolean` (no truthiness — `if (1)` does NOT compile).

### `switch` (classic, with `break`)

```java
switch (day) {
    case 1: case 2: case 3: case 4: case 5:
        System.out.println("Weekday");
        break;
    case 6: case 7:
        System.out.println("Weekend");
        break;
    default:
        System.out.println("Unknown");
}
```

### `switch` expression (Java 14+, arrow form — no fall-through)

```java
String type = switch (day) {
    case 1, 2, 3, 4, 5 -> "Weekday";
    case 6, 7 -> "Weekend";
    default -> "Unknown";
};
```

### `for` loops

```java
// Classic
for (int i = 0; i < 10; i++) { ... }

// Enhanced (for-each)
for (int n : nums) { ... }
for (String name : names) { ... }
```

### `while` and `do-while`

```java
while (cond) { ... }       // checks before, body may never run
do { ... } while (cond);   // checks after, body runs at least once — note the `;`
```

### `break` and `continue`

```java
for (int i = 0; i < 10; i++) {
    if (i == 3) continue;   // skip 3
    if (i == 7) break;       // exit loop entirely
    System.out.println(i);   // prints 0, 1, 2, 4, 5, 6
}

// Labeled break (exits outer loop)
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 5; j++) {
        if (i + j > 6) break outer;
    }
}
```

---

## 7. Methods

```java
[modifiers] returnType name(params) { body; return value; }

public static int add(int a, int b) {
    return a + b;
}
```

- **Modifiers**: `public`, `private`, `protected`, `static`, `final`.
- **Return type**: any type, or `void` for no return value.
- **Parameters**: comma-separated `Type name` pairs.

### Overloading

```java
public static int add(int a, int b) { return a + b; }
public static double add(double a, double b) { return a + b; }
public static int add(int a, int b, int c) { return a + b + c; }
```

Same name, different parameter lists (count/types/order). Return type is NOT part of the signature.

### Varargs

```java
public static int sum(int... nums) {
    int total = 0;
    for (int n : nums) total += n;
    return total;
}
// sum(1, 2, 3) → 6; sum() → 0; sum(new int[]{1,2,3}) → 6
```

Must be the last parameter; only one per method.

### Recursion

```java
public static int factorial(int n) {
    if (n <= 1) return 1;             // base case
    return n * factorial(n - 1);      // recursive case
}
```

Always have a base case to stop; otherwise `StackOverflowError`. Java does NOT optimize tail recursion.

---

## 8. Classes

```java
public class Person {
    // Fields (typically private for encapsulation)
    private String name;
    private int age;

    // Constructor — same name as class, no return type
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getter
    public String getName() { return name; }

    // Setter (with validation)
    public void setAge(int age) {
        if (age < 0) throw new IllegalArgumentException("Age cannot be negative");
        this.age = age;
    }

    // Method
    public String greet() {
        return "Hello, I am " + name;
    }
}

// Usage:
Person p = new Person("Alice", 30);
p.greet();         // "Hello, I am Alice"
p.getName();       // "Alice"
p.setAge(31);
```

### `this`

`this` refers to the current object. Use it to disambiguate fields from parameters (`this.name = name;`), call other constructors (`this(name, 0);`), or pass the current object to another method (`register(this);`).

### Access Modifiers Table

| Modifier         | Same Class | Same Package | Subclass (diff pkg) | World |
|------------------|:----------:|:------------:|:-------------------:|:-----:|
| `public`         |     ✓      |       ✓      |          ✓          |   ✓   |
| `protected`      |     ✓      |       ✓      |          ✓          |   ✗   |
| *(no modifier)*  |     ✓      |       ✓      |          ✗          |   ✗   |
| `private`        |     ✓      |       ✗      |          ✗          |   ✗   |

---

## 9. Inheritance

```java
public class Person {
    protected String name;

    public Person(String name) { this.name = name; }

    public String greet() { return "Hi, I'm " + name; }
}

public class Student extends Person {
    private String school;

    public Student(String name, String school) {
        super(name);          // call parent constructor — MUST be first statement
        this.school = school;
    }

    @Override                // override inherited method
    public String greet() {
        return super.greet() + " (at " + school + ")";   // super.greet() calls parent's version
    }
}
```

- **Single inheritance only**: a class can `extends` exactly ONE parent.
- Every class implicitly extends `Object` if it extends nothing else.
- `super(...)` calls the parent's constructor (must be first statement).
- `super.method()` calls the parent's version of an overridden method.
- `@Override` annotation catches typos (compile error if you do not actually override).
- `final class` cannot be extended (`String` is `final`).
- `final method` cannot be overridden.

---

## 10. Interfaces

```java
public interface Greeter {
    void greet();                          // abstract method — no body

    default void greetLoudly() {           // Java 8+ — default method with body
        System.out.print(">>> ");
        greet();
        System.out.println(" <<<");
    }
}

public interface Named {
    String getName();
}

// A class can implement MULTIPLE interfaces
public class Person implements Greeter, Named {
    private String name;

    public Person(String name) { this.name = name; }

    @Override public void greet() { System.out.println("Hi, I'm " + name); }

    @Override public String getName() { return name; }
}
```

- Interfaces define a contract; classes `implement` them.
- A class can implement MULTIPLE interfaces (workaround for single class inheritance).
- Java 8+ allows `default` methods (with bodies) and `static` methods in interfaces.
- Java 9+ allows `private` methods in interfaces (helper methods for default methods).
- An interface can `extends` one or more other interfaces.

---

## 11. Common Idioms

### `try-with-resources` (Java 7+)

Automatically closes resources (anything `AutoCloseable`):

```java
try (Scanner scanner = new Scanner(System.in);
     BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    String line = reader.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
}
// Both `reader` and `scanner` are automatically closed when the block exits —
// even if an exception is thrown.
```

### `Optional<T>` (Java 8+)

A container that may or may not hold a value — a safer alternative to returning `null`:

```java
public Optional<String> findUser(int id) {
    if (userExists(id)) return Optional.of(getUserName(id));
    return Optional.empty();
}

// Caller:
Optional<String> name = findUser(42);
if (name.isPresent()) {
    System.out.println(name.get());
}
// Or:
name.ifPresent(System.out::println);
String result = name.orElse("Unknown");
```

### Streams (Java 8+)

Functional-style operations on collections:

```java
import java.util.List;
import java.util.stream.Collectors;

List<String> names = List.of("Alice", "Bob", "Charlie", "Dave");

List<String> filtered = names.stream()
    .filter(n -> n.length() > 3)             // keep names longer than 3 chars
    .map(String::toUpperCase)                // convert each to uppercase
    .sorted()                                 // sort alphabetically
    .collect(Collectors.toList());           // collect into a List

// filtered: [ALICE, CHARLIE]

long count = names.stream().filter(n -> n.startsWith("A")).count();
// count: 1
```

Stream operations are chained and lazy — they only run when a terminal operation (`collect`, `count`, `forEach`) is called.

### `var` (Java 10+)

Local variable type inference:

```java
var name = "Alice";                                    // String
var nums = new ArrayList<Integer>();                  // ArrayList<Integer>
var map = new HashMap<String, List<Integer>>();       // HashMap<String, List<Integer>>
```

Only for local variables (not fields, params, or return types). The type is inferred at compile time — Java remains statically typed.

### Records (Java 14+, stable in 16+)

Immutable data-holder classes with auto-generated constructor, getters, `equals`, `hashCode`, and `toString`:

```java
public record Point(int x, int y) {}

// Usage:
Point p = new Point(3, 4);
p.x();                  // 3 — note: accessor is `x()`, NOT `getX()`
p.y();                  // 4
System.out.println(p);  // Point[x=3, y=4]
Point q = new Point(3, 4);
p.equals(q);            // true
```

Records replace boilerplate-heavy data classes. The compiler generates the constructor, accessors (`x()`, `y()` — note the no-get-prefix convention), `equals`, `hashCode`, and `toString` for you.

---

## 12. Common Pitfalls

### `==` vs `.equals()` for strings

```java
String a = new String("hello");
String b = new String("hello");

a == b;          // FALSE — different objects
a.equals(b);     // TRUE — same content
```

Always use `.equals()` for string (and object) content comparison. `==` compares references.

### Integer division

```java
int a = 5, b = 2;
double wrong = a / b;             // 2.0 — integer division!
double right = (double) a / b;    // 2.5 — cast at least one operand first
double right2 = 5.0 / 2;          // 2.5 — use double literal
```

`int / int` returns `int` — the fractional part is discarded BEFORE assignment.

### Pass-by-value (Java is ALWAYS pass-by-value)

```java
void tryToChange(int x) { x = 99; }                  // primitives are copied
void tryToReassign(int[] arr) { arr = new int[]{9}; } // reassigning param is invisible to caller
void mutateArray(int[] arr) { arr[0] = 99; }         // mutating shared object IS visible

int n = 5; tryToChange(n);   // n is still 5
int[] nums = {1, 2, 3}; mutateArray(nums);   // nums[0] is now 99
int[] nums2 = {1, 2, 3}; tryToReassign(nums2);  // nums2 unchanged (still [1,2,3])
```

For primitives, the value is copied. For references, the reference (address) is copied — you can mutate the shared object but not reassign the caller's variable.

### Autoboxing NPE

```java
Integer x = null;
int n = x;   // NullPointerException — auto-unboxing null

// Fix:
if (x != null) { int n = x; }
// Or use primitives: int y = 0;
// Or use map.getOrDefault(key, 0);
```

### Fixed-size arrays

```java
int[] nums = {1, 2, 3};
// nums.length = 10;              // COMPILE ERROR — length is final
// nums.add(4);                    // COMPILE ERROR — arrays have no add()
nums[3] = 4;                     // ArrayIndexOutOfBoundsException

// Use ArrayList for resizable:
ArrayList<Integer> list = new ArrayList<>();
list.add(1); list.add(2); list.add(3);
```

---

## 13. The 10 Most-Used Standard Library Packages

Java's standard library is huge — over 4,000 classes. These ten packages cover the most common everyday tasks. Learn to recognize them by name; you will encounter them constantly.

| Package                  | What it provides                                                | Example classes                                  |
|--------------------------|-----------------------------------------------------------------|--------------------------------------------------|
| `java.lang`              | Core language — auto-imported                                   | `String`, `Math`, `System`, `Object`, `Integer`, `Thread`, `Exception` |
| `java.util`              | Collections, dates, random, scanning                            | `List`, `ArrayList`, `Map`, `HashMap`, `Set`, `Scanner`, `Random`, `Arrays`, `Collections` |
| `java.io`                | Byte streams (legacy I/O)                                       | `File`, `InputStream`, `OutputStream`, `BufferedReader`, `FileReader` |
| `java.nio.file`          | Modern file API (Java 7+)                                       | `Path`, `Files`, `FileSystem`                   |
| `java.time`              | Modern date/time API (Java 8+)                                  | `LocalDate`, `LocalTime`, `LocalDateTime`, `Instant`, `Duration`, `ZonedDateTime` |
| `java.util.stream`       | Functional streams (Java 8+)                                    | `Stream`, `Collectors`, `IntStream`              |
| `java.util.regex`        | Regular expressions                                             | `Pattern`, `Matcher`                             |
| `java.net`               | Networking — sockets, URLs                                      | `URL`, `URI`, `Socket`, `ServerSocket`, `HttpURLConnection` |
| `java.math`              | Arbitrary-precision math                                        | `BigInteger`, `BigDecimal`                       |
| `java.util.concurrent`   | Concurrency utilities                                           | `Executor`, `ExecutorService`, `ConcurrentHashMap`, `Future`, `CompletableFuture`, atomic types |

Notes:
- `java.lang` is **auto-imported** — you never write `import java.lang.String;`.
- `java.util.Date` and `java.util.Calendar` are legacy; prefer `java.time.*` (Java 8+).
- `java.io` is byte-oriented; `java.nio.file` is the modern, friendlier file API.
- `java.util.concurrent` is essential for multithreaded code — it provides thread-safe collections, thread pools, and high-level concurrency abstractions.

Beyond the standard library, the most important third-party libraries you will encounter are: **SLF4J/Logback** (logging), **JUnit** (testing), **Maven/Gradle** (build tools), **Spring / Spring Boot** (web framework), **Hibernate** (ORM), **Jackson** (JSON), **Apache Commons** (utility helpers), and **Guava** (Google utilities).

---

## What's Next?

You have completed the Java module. You now understand:

- The JVM and how "Write Once, Run Anywhere" works
- Java's syntax: classes, packages, imports, the eight primitive types, `var`
- Control flow: `if`/`else`, `switch` (both forms), `for`, `while`, `do-while`, `break`/`continue`
- Methods: definition, overloading, varargs, recursion, Java's pass-by-value semantics
- Object-oriented programming: classes, objects, fields, constructors, access modifiers, getters/setters, `static`, inheritance, polymorphism, method overriding, interfaces (with default methods), and abstract classes

The next module is **C++**. C++ shares Java's C-style syntax (braces, semicolons, the `if`/`for`/`while` keywords) and its object-oriented model (classes, inheritance, polymorphism), but it adds two big things: **manual memory management** (no garbage collector — you `new` and `delete` yourself, or use smart pointers) and **templates** (C++'s version of generics, more powerful than Java's but more complex). C++ also lacks a JVM — it compiles to native machine code, like C and Rust. Many ideas from Java translate directly: a Java class becomes a C++ class; a Java `interface` becomes a C++ pure abstract class; a Java static method becomes a C++ free function or a static method. But C++ gives you direct control over memory layout, performance, and the hardware that Java deliberately hides from you. Head to the C++ module to learn how.

**Next:** Continue to [Module 18: C++ →](/home/z/my-project/learn-dev/18-cpp/)

---

Congratulations on finishing Module 17. The enterprise world of Java — Spring Boot, Jakarta EE, Hadoop, Spark, Kafka — is now within your reach. Take a short break, then continue to the next module.
