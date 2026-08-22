# Lesson 03: Variables and Types

> Module: Java · Lesson 3 of 7
> Estimated time: 30–45 minutes

In Lesson 02 you learned the anatomy of a Java source file. In this lesson we go one level deeper: the type system. Java has eight primitive types (`int`, `double`, `boolean`, and friends) and an unlimited number of reference types (classes). Java is statically typed, so every variable must have a declared type, and the compiler checks that the types match before the program runs. This is one of Java's biggest differences from Python and JavaScript, where types are dynamic.

The most important practical takeaway from this lesson is the difference between `==` and `.equals()` for comparing strings. Java beginners trip over this constantly — `==` compares references (memory addresses), `.equals()` compares contents. If you take only one thing from this lesson, take that. We will also cover the eight primitives, the `var` keyword (introduced in Java 10), type conversion, arrays, and the wrapper classes that bridge primitives and objects.

---

## Learning Objectives

After this lesson, you will be able to:

1. List all eight Java primitive types with their sizes and typical use cases.
2. Distinguish primitive types from reference types and explain how they are stored.
3. Use `var` (Java 10+) for local variable type inference and explain its limitations.
4. Compare strings with `.equals()` instead of `==`, and explain why `==` gives wrong results.

---

## 1. Primitive Types

Java has exactly **eight primitive types**. They are NOT objects — they are raw values stored directly on the stack (or inside an object's fields). They are the only types in Java that are not objects, and they exist for performance: primitives can be stored inline without the overhead of an object header or a reference. Every other type in Java is a reference type (class, array, interface).

Here is the complete table:

| Type      | Size      | Range                                                    | Default Value | Use Case                       |
|-----------|-----------|----------------------------------------------------------|---------------|--------------------------------|
| `byte`    | 8 bits    | -128 to 127                                              | 0             | Compact storage of small ints   |
| `short`   | 16 bits   | -32,768 to 32,767                                       | 0             | Rare; use `int` instead         |
| `int`     | 32 bits   | -2,147,483,648 to 2,147,483,647 (about 2.1 billion)     | 0             | Default integer type           |
| `long`    | 64 bits   | -9.2 × 10^18 to 9.2 × 10^18                              | 0L            | Large counts, timestamps        |
| `float`   | 32 bits   | ~6-7 significant decimal digits                           | 0.0f          | Rare; use `double` instead      |
| `double`  | 64 bits   | ~15-16 significant decimal digits                         | 0.0           | Default floating-point type    |
| `char`    | 16 bits   | 0 to 65,535 (a single Unicode character)                 | `'\u0000'`    | A single character              |
| `boolean` | 1 bit*    | `true` or `false`                                        | `false`       | Truth values                    |

(*The JVM does not specify the exact size of `boolean`; it is implementation-dependent and typically stored as a byte internally.)

A few practical notes. **`int` is the default integer type.** When you write `5` in your code, the compiler treats it as an `int` literal. To write a `long` literal, add an `L` suffix: `long big = 100L;` (uppercase `L` is preferred because lowercase `l` looks like `1`). **`double` is the default floating-point type.** The literal `3.14` is a `double`. To write a `float` literal, add an `f` suffix: `float pi = 3.14f;`. If you write `float pi = 3.14;` (no suffix), the compiler refuses because `3.14` is a `double` and Java's strict typing prevents the implicit narrowing.

**`char` is a 16-bit unsigned integer** holding a single Unicode character (UTF-16 code unit). You write character literals in single quotes: `'A'`, `'7'`, `'\n'` (newline), `'\t'` (tab), `'\u03A9'` (Greek omega Ω, by Unicode code point). `char` is technically a numeric type — you can do arithmetic on it (`'A' + 1` equals `66`, the code for `'B'`).

**`boolean` has only two values: `true` and `false`.** Note the lowercase — Java is strict about this. Unlike Python (where `True` and `False` are capitalized) and unlike JavaScript (where any value can be truthy or falsy), Java does NOT have truthiness. `if (1)` does not compile in Java — the condition must be a `boolean`. This is a frequent source of compile errors for Python and JavaScript developers.

The default value column matters for fields: when you declare `int x;` as a field inside a class (and do not assign it), Java initializes it to `0`. Local variables inside a method do NOT get a default — you must assign them before use, or the compiler refuses. This is a deliberate safety feature to catch uninitialized-variable bugs at compile time.

---

## 2. Reference Types

Everything that is not a primitive is a **reference type**. `String`, arrays, and every class you write are reference types. The variable does not hold the object directly — it holds a **reference** (a memory address) that points to the object on the heap. This is the same model as Python and JavaScript; the only difference is that Java makes the distinction explicit.

Here is a diagram:

```text
Primitive:                Reference:
int x = 5;                String s = "hello";

  Stack                       Stack               Heap
  +-----+                    +-------+          +----------+
  |  5  |  (value)           |  •----|--------->| "hello"  |
  +-----+                    +-------+          +----------+
                              reference         actual object
```

When you write `int x = 5;`, the variable `x` literally holds the value `5`. When you write `String s = "hello";`, the variable `s` holds a reference (a pointer, conceptually) to a `String` object that lives on the heap. The string itself is somewhere in heap memory; `s` just remembers where.

This distinction matters for assignment. With primitives, `int y = x;` copies the value `5` into `y`. Modifying `y` does not affect `x`. With reference types, `String t = s;` copies the **reference** — both `s` and `t` now point to the same `"hello"` object. For mutable objects, this matters: change the object through `t`, and you see the change through `s` too, because they are the same object. (Strings are immutable, so this rarely causes surprises with `String` specifically, but with `ArrayList` it can.)

---

## 3. Variable Declaration

In Java, you must declare a variable's type before using it. The general form is `Type name = value;`:

```java
int age = 30;
double temperature = 22.5;
boolean isAdmin = true;
char grade = 'A';
String name = "Alice";
```

The type goes on the LEFT, the name in the middle, the value on the right. This is the same as in C, Go, Rust, and TypeScript — Java uses **type-first** declaration. You can also declare without assigning (for fields, which get a default; for locals, you must assign later):

```java
int count;          // declared but uninitialized — compiler error if used before assigned
int x = 5, y = 10;  // declare and initialize two at once (rare in real code)
```

Java is **statically typed**. The compiler checks that the type of every value matches the type of the variable it is assigned to. `int x = "hello";` is a compile error: `incompatible types: String cannot be converted to int`. You cannot change a variable's type — once `int x`, always `int x`. There is no `x = "now a string"` like in JavaScript.

A useful detail: integer literals can use underscores for readability since Java 7. `int million = 1_000_000;` is the same as `int million = 1000000;` but easier to read. The compiler ignores the underscores. Use this for large numbers — your future self will thank you.

---

## 4. `var` (Java 10+)

Java 10 (released March 2018) introduced **`var`** — local variable type inference. With `var`, you let the compiler figure out the type from the right-hand side:

```java
var name = "Alice";          // inferred as String
var age = 30;                // inferred as int
var nums = new int[]{1, 2};  // inferred as int[]
var list = new java.util.ArrayList<String>();  // inferred as ArrayList<String>
```

`var` is **syntactic sugar** — the variable still has a fixed type at compile time, the compiler just figures it out for you. `var name = "Alice";` is exactly the same as `String name = "Alice";` at runtime. There is no dynamic typing happening.

`var` has important limitations. **It only works for local variables inside methods** — not for fields, not for method parameters, not for return types. The compiler must be able to infer the type from the initializer, so `var x;` (no initializer) is a compile error. `var x = null;` is also a compile error because the type cannot be inferred. And `var` does not change Java's static typing — once `name` is inferred as `String`, you cannot later assign `name = 42;`.

When should you use `var`? Use it when the type is obvious from the right-hand side (`var name = "Alice";` is clearly a String) or when the type is long and noisy (`var list = new ArrayList<HashMap<String, List<Integer>>>();` is more readable than `ArrayList<HashMap<String, List<Integer>>> list = new ArrayList<>();`). Avoid `var` when the type is not obvious (`var result = process(data);` — what is `result`? A String? An int? A custom class?). Use `var` judiciously; it is a tool for readability, not a license to hide types.

---

## 5. Type Conversion

Java converts between primitive types in two ways: **implicit widening** and **explicit narrowing**.

**Widening conversions** happen automatically when the target type can hold every possible value of the source type. The chain is `byte → short → int → long → float → double`. So you can write:

```java
int i = 100;
long l = i;       // int → long (widening, automatic)
float f = l;      // long → float (widening, automatic)
double d = f;     // float → double (widening, automatic)
```

The compiler inserts the conversion for you because no information is lost (well, `long → float` can lose precision for very large longs, but Java allows it anyway).

**Narrowing conversions** require an explicit cast because information may be lost. The compiler refuses to do it implicitly:

```java
double d = 3.14;
// int i = d;            // COMPILE ERROR — narrowing requires cast
int i = (int) d;          // explicit cast — truncates the decimal, i is now 3
```

The cast `(int)` tells the compiler "yes, I know this might lose data, do it anyway." The fractional part is truncated (not rounded). `3.14` becomes `3`, `3.99` becomes `3`, `-2.7` becomes `-2`. To round, use `Math.round(d)` which returns a `long` you can cast to `int`.

Between `char` and `int`, conversions are explicit both ways because `char` is unsigned and `int` is signed:

```java
char c = 'A';
int code = c;          // char → int, automatic widening — code is 65
char back = (char) 65; // int → char, explicit cast — back is 'A'
```

For strings, conversion is more verbose — you cannot cast a String to an int. You must use parsing methods:

```java
String s = "42";
int n = Integer.parseInt(s);       // String → int — throws NumberFormatException if not numeric
double d = Double.parseDouble("3.14");
String back = Integer.toString(n); // int → String
String back2 = String.valueOf(n); // alternative — works for any type
String back3 = "" + n;             // shortcut — concatenation with empty string
```

For object types (reference types), you can cast between related types in an inheritance hierarchy — covered in Lesson 06.

---

## 6. Strings

Strings in Java are objects of the `String` class (in `java.lang`, so auto-imported). They are **immutable** — once created, a String's contents cannot change. Every method that "modifies" a string actually returns a new String:

```java
String s = "hello";
s.toUpperCase();        // returns "HELLO" but s is still "hello"
s = s.toUpperCase();    // reassigns s to "HELLO"
```

This surprises beginners. `s.toUpperCase()` does NOT change `s` — it returns a new uppercase copy. You must capture the result. This is the same model as Python strings (also immutable) and unlike JavaScript strings (also immutable, but JavaScript developers often forget).

The `+` operator concatenates strings. `String greeting = "Hello, " + name + "!";` builds a new string by joining the parts. This is convenient but can be slow in loops — every `+` creates a new String object. For performance-sensitive code, use `StringBuilder`:

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i).append(", ");
}
String result = sb.toString();   // one string built, not 1000
```

Here are the most-used String methods:

```java
String s = "Hello, World!";
int len = s.length();           // 13 — number of characters
char c = s.charAt(0);           // 'H' — character at index 0
String sub = s.substring(7, 12); // "World" — from index 7 to 12 (exclusive end)
String upper = s.toUpperCase(); // "HELLO, WORLD!"
String lower = s.toLowerCase(); // "hello, world!"
boolean empty = s.isEmpty();    // false
boolean starts = s.startsWith("Hello"); // true
String trimmed = "  hi  ".trim(); // "hi" — removes leading/trailing whitespace
String[] parts = "a,b,c".split(","); // ["a", "b", "c"]
```

Note the indices are zero-based, like in every C-family language.

The most important string comparison: **use `.equals()`, NOT `==`.** This is the single most common Java beginner bug:

```java
String a = "hello";
String b = "hello";
String c = new String("hello");

a.equals(b);    // true — same content
a == b;         // true (sometimes!) — same reference, due to string interning
a.equals(c);    // true — same content
a == c;         // FALSE — different references!
```

The `==` operator compares references (memory addresses), not contents. For two strings to be `==`, they must point to the exact same object in memory. Sometimes Java "interns" string literals (reuses the same object), so `a == b` may happen to be true — but this is a compiler optimization you cannot rely on. `a == c` is false because `new String(...)` always creates a fresh object. **Always use `.equals()` to compare string contents.** The same rule applies to any other reference type (arrays, ArrayLists, custom classes).

For case-insensitive comparison, use `a.equalsIgnoreCase(b)`.

---

## 7. Arrays

An **array** is a fixed-length collection of values of the same type. Java arrays are objects (reference types) but their elements can be primitives or references. Two ways to create one:

```java
int[] nums = new int[5];           // creates [0, 0, 0, 0, 0] — defaults to 0
int[] nums2 = {1, 2, 3, 4, 5};     // creates [1, 2, 3, 4, 5] — literal syntax
String[] names = new String[3];    // creates [null, null, null] — defaults to null
String[] names2 = {"Alice", "Bob"};
```

The `new int[5]` form creates an array of length 5 filled with the default value for `int` (which is `0`). The `{...}` form is an array literal — the compiler figures out the length from the number of elements. Both produce an `int[]` (an array of `int`).

Access elements with `array[index]`, where index is zero-based:

```java
int[] nums = {10, 20, 30};
int first = nums[0];        // 10
nums[1] = 99;               // now nums is [10, 99, 30]
int length = nums.length;   // 3 — note: NO parentheses (length is a field, not a method)
```

Pay attention to that last line. `nums.length` is a field — no parentheses. This is different from `String.length()` (which is a method, with parentheses). Beginners often write `nums.length()` and get a compile error.

**Arrays are fixed-length.** You cannot grow or shrink an array after creation. If you need a resizable list, use `ArrayList`:

```java
import java.util.ArrayList;
ArrayList<String> list = new ArrayList<>();  // empty
list.add("Alice");                            // append
list.add("Bob");
list.get(0);                                  // "Alice"
list.size();                                  // 2 — note: this IS a method
list.set(0, "Charlie");                       // replace index 0
list.remove(1);                               // remove "Bob"
```

`ArrayList` is part of the Java Collections Framework and is covered more in Lesson 06. For this lesson, just remember: arrays are fixed-length and lightweight; `ArrayList` is resizable and a bit heavier.

To iterate over an array, use the enhanced for-loop (covered in Lesson 04):

```java
int[] nums = {1, 2, 3, 4, 5};
for (int n : nums) {
    System.out.println(n);
}
```

---

## 8. Wrapper Classes

For every primitive type, Java provides a **wrapper class** — an object version of the primitive. The eight pairs are: `byte`/`Byte`, `short`/`Short`, `int`/`Integer`, `long`/`Long`, `float`/`Float`, `double`/`Double`, `char`/`Character`, `boolean`/`Boolean`. Wrapper classes are reference types — they hold the primitive value inside an object.

Why do they exist? **Generics require objects.** When you write `ArrayList<int>`, the compiler refuses — generics only work with reference types. You must write `ArrayList<Integer>` instead. The wrapper class lets you store primitives inside collections:

```java
ArrayList<Integer> nums = new ArrayList<>();
nums.add(5);          // autoboxing — int 5 becomes Integer.valueOf(5)
nums.add(10);
int first = nums.get(0);   // auto-unboxing — Integer 5 becomes int 5
```

The process of automatically converting a primitive to its wrapper is called **autoboxing**; the reverse is **auto-unboxing**. This happens automatically so you rarely think about it. But there is a trap: if a wrapper reference is `null` and you try to unbox it, you get a `NullPointerException`:

```java
Integer x = null;
int n = x;   // NullPointerException — cannot unbox null
```

This is the source of the "common mistake" later in this lesson. When in doubt, prefer primitives (`int`) over wrappers (`Integer`) unless you specifically need an object (collections, generics, `Optional`).

Wrappers also provide useful static methods:

```java
int max = Integer.MAX_VALUE;       // 2147483647
int min = Integer.MIN_VALUE;       // -2147483648
int parsed = Integer.parseInt("42"); // String → int
String binary = Integer.toBinaryString(10); // "1010"
double sqrt = Math.sqrt(16.0);     // 4.0 — Math is a utility class, not a wrapper
boolean b = Boolean.parseBoolean("true"); // true
```

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Primitive Overflow

Write a program that declares `int max = Integer.MAX_VALUE;` and prints it. Then compute `max + 1` and print it. You should see a negative number (Java's `int` wraps around — this is called integer overflow). Now do the same with `long` and `Long.MAX_VALUE`. Confirm that `long` gives you a much larger range. The lesson: integer overflow is silent in Java — no exception is thrown. Always pick a type wide enough for your data.

### Exercise 2: String Comparison Trap

Write this code and predict the output before running it:

```java
String a = "hello";
String b = "hello";
String c = new String("hello");

System.out.println(a == b);
System.out.println(a == c);
System.out.println(a.equals(b));
System.out.println(a.equals(c));
```

Run it. Confirm `a == b` is true (string interning) but `a == c` is false (different objects). Confirm both `.equals()` checks are true. Now change `c` to `String c = "he" + "llo";` and predict what `a == c` will be — then run it. (Spoiler: it depends on whether the compiler constant-folds the concatenation. Test it and see.)

### Exercise 3: Read and Convert Input

Write a program that uses `Scanner` (from `java.util`) to read a line of input from the user. Convert that line to an `int` with `Integer.parseInt`. Then read another line and convert to a `double` with `Double.parseDouble`. Print their sum with `System.out.printf("%.2f%n", sum)`. Test it with `5` and `3.5` — you should see `8.50`. Now test it with `hello` — you should see a `NumberFormatException`. (We will cover exceptions in Lesson 06 and the cheatsheet.)

---

## Common Mistakes

### Mistake 1: Using `==` to compare strings

```java
// WRONG — == compares references, not contents
String userInput = new String("yes");
if (userInput == "yes") {
    System.out.println("User said yes");   // Never runs! references differ
}
```

```java
// RIGHT — use .equals() to compare contents
String userInput = new String("yes");
if (userInput.equals("yes")) {
    System.out.println("User said yes");   // Runs correctly
}
```

The `==` operator on strings checks whether two variables point to the exact same object in memory. When the user types `"yes"` into a `Scanner`, Java creates a fresh `String` object — `== "yes"` is false even though the contents match. Use `.equals()` to compare contents. If you want case-insensitive comparison, use `.equalsIgnoreCase()`. This is the single most common Java beginner bug — internalize it now and you will save yourself hours of debugging later.

### Mistake 2: Integer division gives wrong results

```java
// WRONG — 5 / 2 is 2, not 2.5 (integer division)
int a = 5;
int b = 2;
double result = a / b;   // result is 2.0, not 2.5!
```

```java
// RIGHT — cast at least one operand to double before dividing
int a = 5;
int b = 2;
double result = (double) a / b;   // result is 2.5 — cast happens first
// Or:
double result2 = a / (double) b;  // also 2.5
// Or use double literals:
double result3 = 5.0 / 2;          // also 2.5
```

In Java, `int / int` returns an `int` — the fractional part is discarded before assignment. So `5 / 2` is `2`, and assigning that to a `double` gives `2.0`, not `2.5`. To get `2.5`, you must cast at least one operand to `double` first (so the division happens in floating-point), or use `double` literals (`5.0 / 2`). This is the same behavior as C, Go, and TypeScript — Java does not implicitly promote integer division to floating-point. If your average comes out wrong, check for integer division.

### Mistake 3: Auto-unboxing `null` causes NullPointerException

```java
// WRONG — Integer is null, auto-unboxing crashes
Integer x = null;
int n = x;   // NullPointerException at runtime
```

```java
// RIGHT — check for null before unboxing, or use primitives
Integer x = null;
if (x != null) {
    int n = x;   // safe — unboxing happens only if x is not null
}
// Or just use the primitive type:
int y = 0;       // always safe — primitives cannot be null
```

When you assign an `Integer` to an `int` (or pass an `Integer` to a method expecting `int`), Java auto-unboxes it. If the `Integer` is `null`, the unboxing throws `NullPointerException`. This is especially common when you pull values out of a `Map<Integer, Integer>` — if a key is missing, `map.get(key)` returns `null`, and assigning that to an `int` crashes. The fix is to either check for `null` first, or prefer primitives (`int`) when null is not a valid value. Modern Java also offers `Optional` and `map.getOrDefault(key, 0)` to handle this safely.

### Mistake 4: Treating arrays as resizable

```java
// WRONG — you cannot resize an array after creation
int[] nums = {1, 2, 3};
nums[3] = 4;                // ArrayIndexOutOfBoundsException
nums.add(4);               // COMPILE ERROR — arrays have no add() method
nums.length = 10;          // COMPILE ERROR — length is final
```

```java
// RIGHT — use ArrayList for resizable lists, or copy to a new array
import java.util.ArrayList;
ArrayList<Integer> nums = new ArrayList<>();
nums.add(1);
nums.add(2);
nums.add(3);
nums.add(4);               // works fine — ArrayList grows as needed
```

Arrays in Java are fixed-length at creation. You cannot append to an array, you cannot change its length, and `array[index]` past the end throws `ArrayIndexOutOfBoundsException`. If you need a resizable collection, use `ArrayList` (or one of the other Collections Framework classes — `LinkedList`, `HashSet`, `HashMap`, etc.). If you must work with arrays, the only way to "grow" one is to create a larger array and copy the elements over with `System.arraycopy` or `Arrays.copyOf` — but in practice, just use `ArrayList`.

---

## Summary

- Java has eight primitive types: `byte`, `short`, `int`, `long`, `float`, `double`, `char`, `boolean`. They are NOT objects — they are raw values.
- Reference types (`String`, arrays, all classes) are stored on the heap; the variable holds a reference (pointer) to the object.
- `int` is the default integer type (32 bits); `double` is the default floating-point type (64 bits). Use `L` suffix for `long` literals, `f` for `float`.
- `var` (Java 10+) infers local variable types from the initializer — only for locals, not fields or parameters.
- Widening conversions (`int → long → double`) are automatic; narrowing (`double → int`) requires an explicit cast and may lose data.
- Strings are immutable; use `+` for concatenation and `StringBuilder` for performance in loops.
- Compare string contents with `.equals()`, NOT `==` — `==` compares references. This is the #1 Java beginner bug.
- Arrays are fixed-length; `nums.length` is a field (no parentheses). Use `ArrayList` for resizable lists.
- Wrapper classes (`Integer`, `Double`, `Boolean`, etc.) are object versions of primitives, used in collections and generics. Autoboxing can produce `NullPointerException` on `null`.

You now understand Java's type system well enough to read most Java code. In Lesson 04 we cover control flow: `if`/`else`, `switch`, loops (`for`, `while`, `do-while`), and the all-important `break`/`continue` keywords.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
