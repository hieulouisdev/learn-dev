# Lesson 05: Methods

> Module: Java · Lesson 5 of 7
> Estimated time: 30–45 minutes

In Lesson 04 you learned control flow. In this lesson we cover methods — Java's word for functions. You have been calling methods since Lesson 01 (`System.out.println`, `String.length()`, `Math.sqrt`). Now you will learn to define your own. Methods let you break a long program into named, reusable pieces — and once you write a method, you can call it from anywhere in your class.

The two trickiest concepts in this lesson are **`static`** (which you have been writing in `public static void main` without explanation — we will finally explain it) and **pass-by-value**. Java is pass-by-value, always, no exceptions. This surprises developers coming from Python (which is pass-by-reference-of-objects) and C# (which has `ref` and `out` keywords). We will spend time on this because it is one of the most-misunderstood aspects of Java.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define methods with the correct syntax: `[modifiers] returnType name(params) { body }`.
2. Explain what `static` means and why `main` is always `static`.
3. Describe Java's pass-by-value semantics, including the subtle case of object references.
4. Use method overloading, varargs (`int... nums`), and write a recursive method.

---

## 1. Defining Methods

A **method** is a named block of code that can be called from elsewhere. The general form is:

```text
[modifiers] returnType methodName(parameters) {
    // body — the code that runs when the method is called
    return value;    // optional — only if returnType is not void
}
```

Here is a complete example:

```java
public class Calculator {

    // A method that adds two integers and returns the result
    public static int add(int a, int b) {
        int sum = a + b;
        return sum;
    }

    public static void main(String[] args) {
        int result = add(5, 3);
        System.out.println("5 + 3 = " + result);    // 5 + 3 = 8
    }
}
```

Let's break down the `add` method:

- `public` — access modifier. This method can be called from any other class. (We cover access modifiers in Lesson 06.)
- `static` — the method belongs to the class itself, not to an instance of the class. We explain this in Section 2 below.
- `int` — return type. The method promises to return an `int` value. The caller can use the result as an `int`.
- `add` — method name. By convention, method names are camelCase (first word lowercase, subsequent words capitalized: `add`, `calculateTotal`, `isValid`).
- `(int a, int b)` — parameter list. Each parameter has a type and a name. The caller must pass two `int` values when calling `add`. Inside the method, `a` and `b` are local variables holding the arguments passed in.
- `{ ... }` — body. The code that runs when the method is called.
- `return sum;` — sends `sum` back to the caller and ends the method. Every non-`void` method must have a `return` statement on every code path.

The call `add(5, 3)` evaluates to `8`, which is then assigned to `result`. The arguments `5` and `3` are matched to the parameters `a` and `b` positionally — first argument to first parameter, second to second. The values are copied into the parameters (more on this in Section 3).

Methods can take any number of parameters (including zero), and return any type (including `void` for no return value). A method with no parameters looks like `public static String greet() { return "Hello!"; }` — note the empty parentheses in both the declaration and the call (`greet()`).

---

## 2. `static`

The `static` keyword is one of the most confusing Java concepts for beginners. Here is the conceptual explanation: a **static** method belongs to the **class itself**, not to any particular instance (object) of the class. You can call a static method without first creating an object of the class.

```java
public class MathHelper {
    public static int square(int x) {
        return x * x;
    }
}

// Call from anywhere:
int result = MathHelper.square(5);   // 25 — no `new MathHelper()` needed
```

You call `MathHelper.square(5)` directly — you do NOT need to write `new MathHelper()` first. This is why `main` is always `static`: the JVM starts your program by calling `main`, and at that moment no objects exist yet. The JVM has no instance to call `main` on, so `main` must be `static` (callable without an instance).

A **non-static** method (also called an **instance method**) belongs to an object. You must create an object first, then call the method on it:

```java
public class Greeter {
    private String name;

    public Greeter(String name) {
        this.name = name;
    }

    // Non-static method — uses `this.name`
    public String greet() {
        return "Hello, " + this.name + "!";
    }
}

// Usage:
Greeter g = new Greeter("Alice");
String message = g.greet();    // "Hello, Alice!"
// Greeter.greet();   // ERROR — must call on an instance, not the class
```

Instance methods can access instance fields (`this.name`). Static methods cannot — they have no `this`, because they are not tied to an instance. A static method can only access other static fields and static methods of the same class directly.

For now, all the methods you write will be `static` (so you can call them from `main`). We revisit instance methods in Lesson 06 when we cover classes and objects in depth. The rule of thumb: **`static` = "this method does not need an object"; non-static = "this method operates on a specific object".**

Static fields are also possible — `static int count = 0;` declares a counter shared by all instances of the class. We cover this in Lesson 06.

---

## 3. Parameters and Arguments — Java is Pass By Value

This is the most misunderstood topic in Java. **Java is pass-by-value, always, no exceptions.** There is no `&` reference parameter like in C++, no `ref`/`out` like in C#, no pass-by-reference-of-object like in Python. Java always copies the value of the argument into the parameter.

The subtlety is what "value" means for reference types. There are two cases:

**Case 1: Primitives (int, double, boolean, etc.)**. The value is the primitive itself. The parameter is a copy of the argument. Modifying the parameter inside the method does NOT affect the caller's variable:

```java
public static void tryToChange(int x) {
    x = 99;        // modifies the local copy only
}

public static void main(String[] args) {
    int a = 5;
    tryToChange(a);
    System.out.println(a);    // still 5 — caller's variable unchanged
}
```

**Case 2: Reference types (objects, arrays, Strings)**. The value is the **reference** (the memory address). The parameter is a copy of the reference — both the caller's variable and the parameter point to the same object. Modifying the object's fields through the parameter DOES affect the caller, because they are the same object. But reassigning the parameter to a new object does NOT affect the caller:

```java
public static void mutateArray(int[] arr) {
    arr[0] = 99;          // changes the array the caller sees — same object
    arr = new int[]{0};   // makes `arr` (the local copy) point to a new array
    arr[0] = 100;         // changes the new array — caller does not see this
}

public static void main(String[] args) {
    int[] nums = {1, 2, 3};
    mutateArray(nums);
    System.out.println(nums[0]);   // 99 — the first mutation stuck
    System.out.println(nums.length); // 3 — the array was NOT replaced
}
```

This is the part that confuses people. "If Java is pass-by-value, why can `mutateArray` change the array?" Because the value being passed is the reference (the address). The parameter `arr` and the caller's `nums` are two different variables, but they both point to the same array object. Mutating the object through either variable is visible from the other. Reassigning either variable to point to a different object is NOT visible from the other — each variable is independent.

The clearest way to remember this: **Java never passes the variable itself — it always passes a copy of the variable's contents.** For primitives, the contents are the value. For references, the contents are the address. Either way, the caller's variable is untouched; only the parameter (the copy) can be reassigned.

---

## 4. Return Values

A method's return type determines what it gives back to the caller. `void` means "no return value" — the method just does its work and returns control to the caller:

```java
public static void printGreeting(String name) {
    System.out.println("Hello, " + name + "!");
    // no return statement — falls off the end
}
```

Any other return type (`int`, `String`, `double`, `boolean`, an array, an object) means the method MUST return a value of that type on every code path:

```java
public static int max(int a, int b) {
    if (a > b) {
        return a;       // path 1
    } else {
        return b;      // path 2
    }
    // Every path returns — compiler is happy
}
```

If you forget a `return` on some path, the compiler complains: `missing return statement`. For example:

```java
public static int signum(int x) {
    if (x > 0) return 1;
    if (x < 0) return -1;
    // COMPILE ERROR — what if x == 0? No return!
}
```

The fix is to add a final `return 0;` (for the `x == 0` case) outside the `if`s. Java's compiler performs flow analysis to ensure every path returns — it is a strict checker, and that strictness catches bugs.

The `return` statement immediately ends the method. Any code after a `return` (on the same path) is unreachable and is itself a compile error:

```java
public static int foo() {
    return 5;
    // System.out.println("after return");  // COMPILE ERROR — unreachable
}
```

Methods can return any type: primitives, references, arrays, even `null` (for reference types). Returning `null` is legal but can cause `NullPointerException` at the call site — modern Java prefers `Optional<T>` for methods that might not have a value to return (covered in the cheatsheet lesson).

---

## 5. Method Overloading

**Method overloading** means defining multiple methods with the **same name** but **different parameter lists**. The compiler picks the right one based on the arguments you pass:

```java
public class Printer {

    public static void print(int x) {
        System.out.println("Integer: " + x);
    }

    public static void print(double x) {
        System.out.println("Double: " + x);
    }

    public static void print(String x) {
        System.out.println("String: " + x);
    }

    public static void print(int x, int y) {
        System.out.println("Two ints: " + x + ", " + y);
    }

    public static void main(String[] args) {
        print(5);           // calls print(int)
        print(3.14);        // calls print(double)
        print("hello");     // calls print(String)
        print(5, 10);       // calls print(int, int)
    }
}
```

The compiler distinguishes overloads by **parameter count, types, and order**. Two methods with the same name and the same parameter types in the same order — even if the parameter NAMES differ — are considered the same signature, and the compiler refuses: `method print(int) is already defined`. The return type is NOT part of the signature, so you cannot overload on return type alone.

Overloading is everywhere in Java's standard library. `System.out.println` has 10+ overloads — one for `int`, one for `double`, one for `String`, one for `boolean`, one for `char`, one for `Object`, and so on. When you call `println(5)`, the compiler picks `println(int)`. When you call `println("hello")`, it picks `println(String)`. This is overloading in action.

A common beginner mistake is to overload when you meant to override (Lesson 06 — overriding is a subclass providing its own version of an inherited method). Overloading is compile-time polymorphism; overriding is runtime polymorphism. They are different concepts; we cover overriding in Lesson 06.

---

## 6. Variable Arguments (`varargs`)

Java 5 (2004) added **varargs** — a way to pass an arbitrary number of arguments of the same type. The syntax is `Type... name` (three dots after the type):

```java
public static int sum(int... nums) {
    int total = 0;
    for (int n : nums) {        // nums is an int[] inside the method
        total += n;
    }
    return total;
}

public static void main(String[] args) {
    System.out.println(sum(1, 2, 3));           // 6
    System.out.println(sum(10, 20, 30, 40));     // 100
    System.out.println(sum());                   // 0 — no arguments is fine
    int[] arr = {1, 2, 3, 4, 5};
    System.out.println(sum(arr));                // 15 — passing an array also works
}
```

Inside the method, `nums` is treated as an `int[]` — you can call `nums.length`, iterate with a for-each loop, or index into it. The compiler creates the array for you at the call site. `sum(1, 2, 3)` is equivalent to `sum(new int[]{1, 2, 3})` — but you do not have to type the array literal.

Rules for varargs:
- A method can have **at most one** varargs parameter.
- The varargs parameter must be the **last** parameter in the list.

```java
// OK — varargs last
public static void log(String tag, String... messages) { ... }

// COMPILE ERROR — varargs not last
// public static void log(String... messages, String tag) { ... }
```

`System.out.printf` is a famous example of varargs — its signature is `printf(String format, Object... args)`. You can pass any number of arguments after the format string. `String.format` works the same way.

---

## 7. Recursion

A **recursive** method is one that calls itself. Every recursive method needs a **base case** (when to stop) and a **recursive case** (when to recurse). The classic example is factorial: `n! = n × (n-1)!` for `n > 0`, and `0! = 1`:

```java
public static int factorial(int n) {
    if (n <= 1) {
        return 1;                  // base case — stop recursing
    }
    return n * factorial(n - 1);   // recursive case — call itself with n-1
}

public static void main(String[] args) {
    System.out.println(factorial(5));   // 120 — 5*4*3*2*1
    System.out.println(factorial(0));    // 1
}
```

Trace through `factorial(5)`:
1. `factorial(5)` returns `5 * factorial(4)`
2. `factorial(4)` returns `4 * factorial(3)`
3. `factorial(3)` returns `3 * factorial(2)`
4. `factorial(2)` returns `2 * factorial(1)`
5. `factorial(1)` returns `1` (base case)
6. Unwinding: `2 * 1 = 2`, `3 * 2 = 6`, `4 * 6 = 24`, `5 * 24 = 120`

Without a base case, the recursion never stops — Java runs out of stack space and throws `StackOverflowError`. Always make sure your recursive method makes progress toward the base case on every call. For factorial, `factorial(n - 1)` reduces `n`, so we approach the base case (`n <= 1`).

Another classic: Fibonacci numbers. `fib(0) = 0`, `fib(1) = 1`, `fib(n) = fib(n-1) + fib(n-2)`:

```java
public static int fib(int n) {
    if (n < 2) return n;             // base case
    return fib(n - 1) + fib(n - 2);  // recursive case — TWO recursive calls
}
```

This naive Fibonacci is famously slow — it recomputes the same values many times (`fib(5)` calls `fib(4)` and `fib(3)`; `fib(4)` calls `fib(3)` and `fib(2)` — `fib(3)` is computed twice). For real use, prefer an iterative version (a `for` loop). But the recursive form is a clear teaching example.

Recursion is best when a problem is naturally self-similar — tree traversal, divide-and-conquer algorithms (merge sort, quick sort), or mathematical definitions. For linear iteration (factorial, fibonacci), a `for` loop is usually faster and uses less memory. Java does NOT optimize tail recursion (unlike Scheme or Rust in some modes), so deep recursion in Java can blow the stack. The default stack is typically 512 KB to 1 MB, which limits recursion depth to a few thousand frames.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Overload a Greeting Method

Write a class `Greeter` with three overloaded `greet` methods:
- `greet()` — prints `Hello!`
- `greet(String name)` — prints `Hello, Alice!` (using the name)
- `greet(String name, int hour)` — prints `Good morning, Alice!` if hour is before 12, `Good afternoon, Alice!` if 12–17, `Good evening, Alice!` if 18+. Call all three from `main` with different arguments and confirm the compiler picks the right overload each time.

### Exercise 2: Pass-By-Value Investigation

Write a method `public static void modify(int[] arr, int x)` that does `arr[0] = 100; x = 100;`. In `main`, create `int[] nums = {1, 2, 3};` and `int p = 5;`, call `modify(nums, p);`, then print `nums[0]` and `p`. Predict the output before running. Confirm `nums[0]` is 100 (the array was mutated through the shared reference) but `p` is still 5 (the primitive was copied, not modified). This exercise cements the pass-by-value-of-reference rule.

### Exercise 3: Recursive Power

Write a recursive method `power(double base, int exp)` that returns `base^exp`. Use the rule: `power(b, 0) = 1` (base case), `power(b, e) = b * power(b, e-1)` (recursive case). Test it: `power(2, 10)` should return `1024.0`, `power(5, 0)` should return `1.0`, `power(2, -1)` should... what? Your method probably returns 1 (because the base case `exp <= 0` triggers). Fix it to handle negative exponents correctly (`b^-1 = 1/b^1`). Hint: add a check at the top: `if (exp < 0) return 1 / power(base, -exp);`.

---

## Common Mistakes

### Mistake 1: Forgetting `return` in a non-void method

```java
// WRONG — not all paths return a value
public static int max(int a, int b) {
    if (a > b) {
        return a;
    }
    // COMPILE ERROR — what if a <= b? No return.
}
```

```java
// RIGHT — every path returns
public static int max(int a, int b) {
    if (a > b) {
        return a;
    } else {
        return b;
    }
}
// Or simpler:
public static int max(int a, int b) {
    return (a > b) ? a : b;     // ternary — always returns
}
```

The compiler performs **definite assignment analysis** — it checks that every possible execution path through the method ends in a `return` statement (or throws an exception, which also counts as "ending"). If any path can fall off the end without returning, you get `missing return statement`. The fix is to ensure every path returns. The most reliable pattern is to have a single `return` at the end of the method (after computing the result into a variable), or to use `if/else` so every branch returns.

### Mistake 2: Overloading with the same parameter types

```java
// WRONG — same signature, even though parameter names differ
public static int add(int a, int b) { return a + b; }
public static int add(int x, int y) { return x + y; }   // COMPILE ERROR
```

```java
// RIGHT — different parameter lists (different types or counts)
public static int add(int a, int b) { return a + b; }
public static int add(int a, int b, int c) { return a + b + c; }   // OK — 3 params
public static double add(double a, double b) { return a + b; }    // OK — double type
```

Overloading requires the parameter lists to differ in **count, types, or order of types**. Two methods with the same name and the same parameter types in the same order are the SAME signature, even if the parameter names differ. The compiler stops with `method add(int,int) is already defined in class ...`. The return type is NOT part of the signature — you cannot have two methods that differ only in return type. The fix is to either rename one of the methods or actually change the parameter list (more parameters, different types, or different order).

### Mistake 3: Thinking Java passes objects by reference

```java
// WRONG assumption — Java is NOT pass-by-reference, even for objects
public static void swap(Integer a, Integer b) {
    Integer tmp = a;
    a = b;
    b = tmp;
    // This swaps the LOCAL variables a and b — the caller sees nothing
}

public static void main(String[] args) {
    Integer x = 1;
    Integer y = 2;
    swap(x, y);
    System.out.println(x + ", " + y);   // 1, 2 — NOT swapped!
}
```

```java
// RIGHT — understand that Java passes the value of the reference (a copy of the pointer)
// To swap, you cannot reassign the caller's variables — you can only mutate shared objects.
// Use an array or wrapper:
public static void swap(int[] arr, int i, int j) {
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
    // This works — we mutate the array through the shared reference
}
```

Java is pass-by-value, always. For object parameters, the value being passed is a copy of the reference (a copy of the pointer). The method gets its own pointer variable; reassigning it inside the method has no effect on the caller's pointer. This is why the `swap` example fails — the swap only affects the local copies of the references, not the caller's variables. To swap two values held by the caller, you need a mutable container (like an array or a list) that both sides can mutate. Or refactor your code so swapping is unnecessary.

### Mistake 4: Using `void` and trying to `return` a value

```java
// WRONG — void method returns a value
public static void printGreeting(String name) {
    return "Hello, " + name;   // COMPILE ERROR — void method cannot return a value
}
```

```java
// RIGHT — either change the return type or remove the return value
// Option A: return the string
public static String getGreeting(String name) {
    return "Hello, " + name;
}
// Option B: print and return nothing
public static void printGreeting(String name) {
    System.out.println("Hello, " + name);
    // no return — or `return;` with no value to exit early
}
```

A `void` method does not return a value. You can write `return;` (no value) to exit the method early — useful for guard clauses (`if (input == null) return;`). But you cannot write `return value;` in a `void` method — the compiler stops with `incompatible types: unexpected return value`. The fix is one of two: change the return type to match what you are returning (e.g., `String` instead of `void`), or remove the value being returned and just print / store the result. Pick based on what makes sense — if the caller needs the result, return it; if the method is just doing a side effect (printing, mutating state), use `void`.

---

## Summary

- Methods are declared as `[modifiers] returnType name(params) { body }` and called with `name(args)`.
- `static` means the method belongs to the class itself, not an instance. `main` is always static because the JVM calls it without an instance.
- **Java is pass-by-value, always.** For primitives, the value is copied. For references, the reference (address) is copied — the method can mutate the shared object but cannot reassign the caller's variable.
- `void` methods return nothing; non-`void` methods MUST `return` a value of the declared type on every code path.
- **Overloading**: multiple methods with the same name but different parameter lists (count/types/order). Return type is NOT part of the signature.
- **Varargs**: `Type... name` accepts any number of arguments, treated as an array inside the method. Must be the last parameter; only one per method.
- **Recursion**: a method calls itself. Always include a base case to stop; otherwise `StackOverflowError`. Java does not optimize tail recursion — prefer iteration for deep recursion.
- Use `return;` (no value) to exit a `void` method early; use `return value;` to send back a value from a non-`void` method.

You can now write modular Java code with your own methods. In Lesson 06 we cover the biggest topic in the module: classes and objects. That is where Java's object-oriented heart truly lives, and where you will learn inheritance, polymorphism, and interfaces — the building blocks of every Java framework you will ever use.

---

**Next:** [Lesson 06: Classes and Objects →](./06-classes-and-objects.md)
