# Lesson 04: Control Flow

> Module: Java · Lesson 4 of 7
> Estimated time: 30–45 minutes

In Lesson 03 you learned Java's type system. In this lesson we put types to work inside control-flow constructs: `if`/`else`, `switch`, loops (`for`, `while`, `do-while`), and the `break`/`continue` keywords that let you jump around inside loops. If you have written Python, Rust, Go, or TypeScript, most of this will look familiar — Java uses C-style syntax, so the keywords and braces are the same as Go's and TypeScript's. The differences are subtle but important.

The single biggest surprise for Python and JavaScript developers is Java's strictness about `boolean` types. There is **no truthiness** in Java — `if (1)` does not compile. The condition must be a `boolean` value (`true` or `false`, or an expression that evaluates to one). This catches bugs but takes getting used to. By the end of this lesson, you will be fluent in all of Java's control flow and ready to write real logic.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write `if`/`else if`/`else` chains and explain why conditions must be `boolean` (no truthiness).
2. Use `switch` statements correctly (with `break`!) and write Java 14+ switch expressions with `->`.
3. Use all four loop forms — classic `for`, enhanced `for`, `while`, `do-while` — and know when to choose each.
4. Use `break`, `continue`, and labeled `break` to control loop execution, plus the ternary operator.

---

## 1. `if` / `else if` / `else`

Java's `if` statement uses the same C-style syntax you saw in Go and TypeScript. The condition goes in parentheses, and the body goes in braces:

```java
int score = 85;

if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");
} else if (score >= 70) {
    System.out.println("Grade: C");
} else {
    System.out.println("Grade: F");
}
```

The structure: `if (condition) { ... } else if (condition) { ... } else { ... }`. The braces are technically optional for single-statement bodies (`if (x > 0) System.out.println("positive");`), but **always use braces** — this is the universal Java style rule. Apple's infamous 2014 `goto fail` security bug was caused by an unbraced `if` in C; the same risk exists in Java. Modern IDEs and linters enforce braces.

The condition must evaluate to a `boolean`. This trips up Python and JavaScript developers:

```java
int x = 5;
// if (x) { ... }              // COMPILE ERROR — int is not boolean
// if (x = 5) { ... }          // COMPILE ERROR — assignment returns int, not boolean
if (x == 5) { ... }            // OK — == returns boolean
if (x != 0) { ... }            // OK — != returns boolean
if (x > 0) { ... }             // OK
```

Java has no truthiness. `0`, `""` (empty string), `null` — none of these are "falsy" in Java. Every condition must explicitly compare to something. The classic C bug `if (x = 5)` (assignment instead of comparison) is impossible in Java because the assignment operator `=` returns the assigned value (an `int`), which is not a `boolean`, so the compiler refuses. This is a deliberate safety feature.

You can also combine conditions with `&&` (logical AND) and `||` (logical OR), and negate with `!`:

```java
if (age >= 18 && age <= 65) { ... }      // both must be true
if (name.equals("Alice") || name.equals("Bob")) { ... }  // either true
if (!isWeekend) { ... }                  // logical NOT
```

These operators **short-circuit**: `&&` stops evaluating as soon as it sees `false`, and `||` stops as soon as it sees `true`. This is useful for null checks: `if (obj != null && obj.isValid()) { ... }` is safe because `obj.isValid()` is only called when `obj` is not null.

---

## 2. `switch`

The `switch` statement compares a value against a list of cases. The value must be an `int`, `String`, `enum`, or `char` (Java 17+ also allows patterns, which is more advanced):

```java
int day = 3;
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
    case 4:
        System.out.println("Thursday");
        break;
    case 5:
        System.out.println("Friday");
        break;
    default:
        System.out.println("Weekend");
}
```

Here is the critical rule: **`switch` cases fall through by default.** If you forget `break;` at the end of a case, execution continues into the next case. This was a deliberate design choice in C (for some legitimate use cases) but it causes more bugs than it solves. Most Java code uses `break;` after every case. The `default:` case runs if no case matches — it does not need a `break` (it is last), but adding one is harmless.

`switch` on `String` (added in Java 7) works the same way:

```java
String color = "red";
switch (color) {
    case "red":
        System.out.println("Stop");
        break;
    case "yellow":
        System.out.println("Caution");
        break;
    case "green":
        System.out.println("Go");
        break;
    default:
        System.out.println("Unknown");
}
```

### Java 14+: Switch Expressions

Java 14 (2020) introduced a cleaner **switch expression** syntax that uses `->` (arrow) instead of `:` and does NOT fall through. This is much safer:

```java
int day = 3;
String dayName = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4 -> "Thursday";
    case 5 -> "Friday";
    default -> "Weekend";
};
System.out.println(dayName);   // Wednesday
```

The arrow `->` form does not fall through, so you do not need `break`. The switch now returns a value (assigned to `dayName`), making it an expression. If a case needs multiple statements, use a block with `yield`:

```java
String result = switch (day) {
    case 1, 2, 3, 4, 5 -> {
        System.out.println("Weekday");
        yield "Weekday";       // yield returns the value from a block
    }
    case 6, 7 -> "Weekend";
    default -> "Unknown";
};
```

Prefer the arrow form in new code — it eliminates the fall-through bug entirely. This module uses both forms, because you will encounter the old form in many existing codebases.

---

## 3. The `for` Loop

Java has two `for` loops. The **classic for-loop** (C-style) has three parts separated by semicolons:

```java
for (int i = 0; i < 10; i++) {
    System.out.println(i);
}
```

- `int i = 0` — initialization. Runs once before the loop starts. Declares a counter variable `i`.
- `i < 10` — condition. Checked before each iteration. If `true`, the body runs; if `false`, the loop ends.
- `i++` — update. Runs after each iteration. Increments `i` by 1 (the postfix `++` operator).

All three parts are optional. `for (;;)` is an infinite loop. You can declare multiple variables: `for (int i = 0, j = 10; i < j; i++, j--) { ... }`. But most loops are simple — count from 0 to N.

The classic `for` loop is best when you need the index — for example, accessing array elements by position:

```java
int[] nums = {10, 20, 30, 40, 50};
for (int i = 0; i < nums.length; i++) {
    System.out.println("Index " + i + ": " + nums[i]);
}
```

### Enhanced for-loop (for-each)

Java 5 (2004) added the **enhanced for-loop** (sometimes called "for-each"), which iterates over arrays and collections without exposing the index:

```java
int[] nums = {10, 20, 30, 40, 50};
for (int n : nums) {              // for each int n in nums
    System.out.println(n);
}

String[] names = {"Alice", "Bob", "Charlie"};
for (String name : names) {
    System.out.println("Hello, " + name);
}
```

The colon `:` is read as "in" — "for each `int n` in `nums`". This is the equivalent of Python's `for n in nums:` and JavaScript's `for (const n of nums)`. Use the enhanced for-loop whenever you do not need the index — it is more readable and less error-prone.

---

## 4. The `while` Loop

The `while` loop checks the condition BEFORE each iteration. If the condition is false initially, the body never runs:

```java
int count = 0;
while (count < 5) {
    System.out.println("Count is " + count);
    count++;
}
```

This prints `Count is 0` through `Count is 4`. The condition `count < 5` is checked before each iteration; when `count` reaches 5, the loop exits. If `count` started at 5 (or higher), the body would never run.

`while (true) { ... }` is a common idiom for infinite loops — you exit with `break` from inside the body. The condition is still `boolean` — `while (1)` does NOT compile.

---

## 5. The `do-while` Loop

The `do-while` loop checks the condition AFTER the body, so the body runs at least once:

```java
int n = 10;
do {
    System.out.println("n is " + n);
    n--;
} while (n > 5);
```

This prints `n is 10` through `n is 6`. The body runs first, then the condition `n > 5` is checked. If `n` had started at 0 (which is not greater than 5), the body would still run once — that is the difference from `while`.

Note the semicolon at the end: `} while (n > 5);`. The `while` clause of a `do-while` loop ends with a semicolon, unlike a standalone `while` loop. Forgetting this semicolon is a common syntax error.

`do-while` is rarely used in modern Java code — most loops fit the `while` or `for` pattern more naturally. The main use case is "ask the user for input, validate, and ask again if invalid":

```java
Scanner scanner = new Scanner(System.in);
String input;
do {
    System.out.print("Enter 'yes' or 'no': ");
    input = scanner.nextLine();
} while (!input.equals("yes") && !input.equals("no"));
```

---

## 6. `break` and `continue`

`break` exits the current loop immediately. `continue` skips the rest of the current iteration and moves to the next one.

```java
for (int i = 0; i < 10; i++) {
    if (i == 3) continue;     // skip 3
    if (i == 7) break;         // stop the loop at 7
    System.out.println(i);
}
// Output: 0, 1, 2, 4, 5, 6
```

Both work in `for`, `while`, and `do-while` loops. `break` only exits the innermost loop — if you are inside two nested loops, `break` only exits the inner one. To break out of an outer loop, use a **labeled break**:

```java
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 5; j++) {
        if (i == 2 && j == 3) {
            break outer;       // exits BOTH loops
        }
        System.out.println("i=" + i + " j=" + j);
    }
}
```

The label `outer:` (followed by a colon) marks the outer loop. `break outer;` jumps out of that specific loop, not just the inner one. Labels are uncommon in real code because nested loops are usually refactorable into separate methods, but they exist when you need them. `continue outer;` similarly skips to the next iteration of the labeled loop.

---

## 7. The Ternary Operator

The **ternary operator** `? :` is a one-line `if`/`else` that returns a value. The syntax is `condition ? valueIfTrue : valueIfFalse`:

```java
int a = 5, b = 10;
int max = (a > b) ? a : b;       // max is 10
String sign = (x >= 0) ? "non-negative" : "negative";
int abs = (x >= 0) ? x : -x;    // absolute value
```

The parentheses around the condition are optional but recommended for readability. The two branches must be the same type (or convertible to a common type). The ternary is an **expression**, not a statement — it produces a value, so you can assign it to a variable or pass it to a method.

Use the ternary for simple two-way choices. Avoid nesting ternaries (`a ? b ? c : d : e`) — they become unreadable fast. If you need three or more branches, use `if`/`else` or `switch`.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: FizzBuzz

The classic interview question. Write a program that loops from 1 to 100. For each number:
- If divisible by 3, print `Fizz`.
- If divisible by 5, print `Buzz`.
- If divisible by both 3 and 5, print `FizzBuzz`.
- Otherwise, print the number itself.

Hint: check the "both" case FIRST (otherwise divisible-by-3 will catch it first). Use `i % 3 == 0` to check divisibility. The first few outputs should be: `1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz, 16, ...`.

### Exercise 2: Switch on Day of Week

Declare `int day = 3;` (where 1 = Monday, 7 = Sunday). Use BOTH a classic `switch` (with `break`) AND a Java 14+ switch expression to print the day's name. Compare the two — which is shorter? Which is harder to get wrong? Now change `day` to `8` and confirm both versions fall through to `default`.

### Exercise 3: Sum Numbers Until Negative

Write a `while` loop that reads integers from `Scanner` and adds them up, until the user enters a negative number. Then print the total. Test with `5 10 15 -1` — you should see `30` (5+10+15=30; -1 stops the loop). Test with `-1` only — the loop should not add anything, output `0`. Notice that the `while` loop never executes if the first input is negative. Now rewrite it with a `do-while` loop and observe the difference (the body runs once even on the first negative input).

---

## Common Mistakes

### Mistake 1: Forgetting `break` in `switch` (fall-through bug)

```java
// WRONG — missing break causes fall-through
int day = 1;
switch (day) {
    case 1:
        System.out.println("Monday");
    case 2:
        System.out.println("Tuesday");
    case 3:
        System.out.println("Wednesday");
        break;
}
// Output: Monday, Tuesday, Wednesday  (oops — wanted just Monday)
```

```java
// RIGHT — every case ends with break
int day = 1;
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
}
// Output: Monday
```

In the classic `switch` statement, execution falls through to the next case if there is no `break`. This was useful in C for combining cases (e.g., `case 1: case 2: case 3: print("Q1"); break;`), but it causes more bugs than it solves. The fix is to add `break;` after every case body. Even better, use the Java 14+ switch expression form (`case 1 -> "Monday";`) which does not fall through at all. If you see your `switch` printing unexpected output, check for missing `break`s.

### Mistake 2: Using `if (x = 5)` instead of `if (x == 5)`

```java
// This is actually a COMPILE ERROR in Java — but the bug you really want to avoid
// is when you compare objects with == instead of .equals()
int x = 0;
if (x = 5) {                       // COMPILE ERROR — assignment returns int, not boolean
    System.out.println("x is 5");
}
```

```java
// RIGHT — use == for comparison (primitives), .equals() for objects
int x = 0;
if (x == 5) {                      // OK — == returns boolean
    System.out.println("x is 5");
}
```

In C and C++, `if (x = 5)` is a silent bug — the assignment returns the assigned value (`5`), which is truthy, so the body always runs. Java's designers added a safety check: the condition of an `if` must be `boolean`, and assignment returns the assigned type. So `if (x = 5)` is a compile error in Java (since `int` is not `boolean`), saving you from the C-style bug. The lesson: when you see "incompatible types: int cannot be converted to boolean", check whether you typed `=` instead of `==`. For object comparisons (strings, etc.), remember that `==` compares references and `.equals()` compares contents (see Lesson 03).

### Mistake 3: Integer overflow wraps silently

```java
// WRONG — int silently wraps around on overflow, no exception
int a = Integer.MAX_VALUE;    // 2147483647
int b = a + 1;                // -2147483648 — wraps to negative!
System.out.println(b);        // prints -2147483648

// Also: factorial of large numbers overflows
int fact = 1;
for (int i = 1; i <= 20; i++) {
    fact *= i;
}
System.out.println(fact);     // 2102132736 — wrong! 20! is much larger than int max
```

```java
// RIGHT — use long for large values, or BigInteger for arbitrarily large
long fact = 1;
for (int i = 1; i <= 20; i++) {
    fact *= i;
}
System.out.println(fact);     // 2432902008176640000 — correct
```

Java's `int` is a 32-bit signed integer. If you add 1 to `Integer.MAX_VALUE` (2,147,483,647), it wraps around to `-2,147,483,648`. Java does NOT throw an exception — the wrap is silent. This is a security hole in real code (search the internet for "Java integer overflow bug" — there are famous security incidents). The fix: use `long` (64-bit, range up to ~9.2 × 10^18) when you might exceed `int`'s range, or `java.math.BigInteger` for arbitrarily large integers. Loops that compute factorials, Fibonacci numbers, or large sums need `long`. Modern Java has `Math.addExact`, `Math.multiplyExact`, etc., which throw `ArithmeticException` on overflow — use these when correctness matters more than speed.

### Mistake 4: Off-by-one errors with `<=` vs `<`

```java
// WRONG — using <= when you meant <  (loops one extra time)
int[] nums = {10, 20, 30, 40, 50};
for (int i = 0; i <= nums.length; i++) {
    System.out.println(nums[i]);   // ArrayIndexOutOfBoundsException at i=5
}
```

```java
// RIGHT — use < with .length
int[] nums = {10, 20, 30, 40, 50};
for (int i = 0; i < nums.length; i++) {
    System.out.println(nums[i]);   // prints 10, 20, 30, 40, 50
}
```

Arrays in Java are zero-indexed: an array of length 5 has indices 0, 1, 2, 3, 4. If you loop with `i <= nums.length`, the last iteration has `i == 5`, which is out of bounds — `nums[5]` throws `ArrayIndexOutOfBoundsException`. The correct bound is `i < nums.length`. This is the classic "off-by-one" error, the most common loop bug in C-family languages. Whenever you see this exception, check whether your loop bound uses `<=` instead of `<`. Better yet, use the enhanced for-loop (`for (int n : nums)`) when you do not need the index — it makes off-by-one errors impossible.

---

## Summary

- `if (cond) { ... } else if (cond) { ... } else { ... }` — conditions must be `boolean` (no truthiness; `if (1)` does not compile).
- `switch (value) { case X: ...; break; ...; default: ... }` — cases fall through by default; always use `break`.
- Java 14+ switch expressions with `->` do NOT fall through and can return values — prefer them in new code.
- Classic `for`: `for (int i = 0; i < n; i++)` — best when you need the index.
- Enhanced `for`: `for (int n : nums)` — best for iteration without index.
- `while (cond) { ... }` — checks before body; body may never run.
- `do { ... } while (cond);` — checks after body; body runs at least once. Note the trailing semicolon.
- `break` exits the loop; `continue` skips to the next iteration. Labeled `break outer;` exits an outer loop.
- Ternary: `cond ? a : b` returns a value — use for simple two-way choices, never nest them.
- Use `long` (not `int`) for values that might exceed ~2 billion; use `BigInteger` for arbitrary sizes. Overflow is silent.

You can now write real Java logic. In Lesson 05 we cover methods — how to define them, the `static` keyword (which you have been using without explanation), Java's pass-by-value semantics, method overloading, varargs, and recursion.

---

**Next:** [Lesson 05: Methods →](./05-methods.md)
