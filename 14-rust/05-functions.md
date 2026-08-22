# Lesson 05: Functions

> Module: Rust · Lesson 5 of 7
> Estimated time: 30–45 minutes

Welcome to Lesson 05. You have been writing code inside `fn main()` for four lessons now — it is time to learn how to write your own functions. Functions are how you package up a piece of behavior so you can reuse it, name it, and call it from anywhere in your program. Rust functions look familiar if you have used any other language, but there is one twist you must understand: the difference between **statements** and **expressions**.

The statement/expression distinction is the single most important concept in this lesson, and it will explain everything you have seen so far — why some lines end with semicolons, why `if` can return a value, why forgetting a semicolon causes errors. Get this concept right and the rest of Rust feels natural. Get it wrong and you will be confused for the rest of the module.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define functions with `fn`, with parameters and return values, using the `-> Type` syntax.
2. Explain the difference between statements (do not return values) and expressions (do), and identify each in Rust code.
3. Use `return` for early returns and the trailing-expression syntax for normal returns.
4. Explain why adding a semicolon after the last expression in a function body makes it return `()` instead of the intended value.

---

## 1. Defining Functions with `fn`

You declare a function in Rust with the `fn` keyword, followed by the function name, parameters in parentheses, an optional return type after `->`, and the body in braces:

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    let result = add(3, 4);
    println!("3 + 4 = {}", result);
}
```

Let's break this down line by line.

- `fn add(a: i32, b: i32) -> i32 {` — declares a function named `add`.
  - `a: i32` — the first parameter, named `a`, of type `i32`. Note the colon and explicit type annotation — **Rust requires types on function parameters**, no inference.
  - `b: i32` — the second parameter, named `b`, of type `i32`.
  - `-> i32` — the return type, an `i32`. The arrow `->` separates the parameter list from the return type.
  - `{` — opening brace of the function body.
- `a + b` — the body of the function, a single expression that adds `a` and `b`. Notice there is **no semicolon** here — this is intentional, and you will see why in Section 2.
- `}` — closing brace.
- `let result = add(3, 4);` — calls `add` with arguments `3` and `4`, binds the result to `result`.
- `println!("3 + 4 = {}", result);` — prints `3 + 4 = 7`.

When you run this, the output is:

```text
3 + 4 = 7
```

### Function Naming Convention

Rust function names are `snake_case` — lowercase with underscores between words. So `add_two_numbers`, `compute_average`, `is_ready`. The compiler does not enforce this, but `cargo clippy` will warn you if you deviate. Idiomatic Rust code follows this convention almost universally.

### Parameters Need Explicit Types

This is the single most important difference between function parameters and local variables. Local variables can use type inference: `let x = 5;` works without a type annotation. Function parameters **cannot**: you must always write the type, even if it could in principle be inferred. This is a deliberate design choice — it makes function signatures self-documenting and prevents refactoring inside a function body from accidentally changing its public interface. When you read a Rust function signature, you know exactly what types go in and what type comes out.

### Functions Can Be Defined in Any Order

Unlike C, where you must declare a function before using it (or write a forward declaration), Rust lets you put functions in any order in the file. The compiler reads the whole file before generating code:

```rust
fn main() {
    greet();   // greet is defined below — that's fine
}

fn greet() {
    println!("Hello!");
}
```

This is convenient — you can put `main` at the top and helpers below, or organize your file however you like.

---

## 2. Statements vs Expressions

This is the single most important concept in this lesson, and possibly in the entire module. Read this section carefully. **A statement does something but does not produce a value. An expression evaluates to a value.**

The classic examples:

- `let x = 5;` — a **statement**. It binds the value `5` to the name `x`, but the statement itself does not evaluate to anything you can use. You cannot write `let y = (let x = 5);` in Rust — the inner `let x = 5;` is not a value.
- `5 + 3` — an **expression**. It evaluates to the value `8`. You can use it anywhere a value is needed: `let y = 5 + 3;` is legal.
- `{ let y = 5; y + 1 }` — a **block expression**. The whole block evaluates to `6`, because the last expression in the block (without a semicolon) is `y + 1`.

### The Semicolon Rule

Here is the rule that ties it all together: **adding a semicolon to an expression turns it into a statement, and a statement evaluates to `()`** (the unit type, pronounced "unit", which means "no meaningful value").

So:

- `5 + 3` (no semicolon) — an expression that evaluates to `8`.
- `5 + 3;` (with semicolon) — a statement that evaluates to `()`.

This means the **last expression in a function body** is the function's return value — but only if it has no semicolon. If you add a semicolon, the function now returns `()` instead.

Let's see this in action. Here is the correct way to write `add`:

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b       // no semicolon — this is the return value
}
```

And here is the broken way — with a semicolon added:

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b;      // semicolon — this is now a statement returning ()
}
```

The second version does not compile. The error is:

```text
error[E0308]: mismatched types
 --> src/main.rs:1:24
  |
1 | fn add(a: i32, b: i32) -> i32 {
  |    ---                      ^^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
```

The compiler is telling you: "You said the function returns `i32`, but the body's last expression evaluates to `()`." This is the single most common error Rust beginners hit, and once you understand the statement/expression distinction, the error message makes complete sense.

### Block Expressions

Any block `{ ... }` in Rust is an expression that evaluates to its **last expression** (without semicolon). This works inside function bodies, `if` branches, `match` arms, and even standalone:

```rust
fn main() {
    let y = {
        let x = 5;
        x + 1
    };
    println!("y = {}", y);
}
```

Let's break this down.

- `let y = { ... };` — the entire block in braces is an expression.
- `let x = 5;` — a statement inside the block. It binds `5` to `x`. This statement returns `()` (as statements do), but that does not matter because it is not the last expression in the block.
- `x + 1` — the last expression in the block, **no semicolon**. This is the value the block evaluates to.
- So `y` becomes `6`.

When you run this, the output is:

```text
y = 6
```

This is the same mechanism that makes `if` an expression (Lesson 04): the chosen branch's last expression (no semicolon) is the value of the `if`. Same for `match`. The "last expression, no semicolon" rule is consistent across Rust.

---

## 3. Return Values

There are two ways to return a value from a Rust function. The first is the **trailing expression** syntax you have already seen — the last expression in the body, with no semicolon, is the return value. This is the idiomatic way:

```rust
fn square(x: i32) -> i32 {
    x * x
}
```

The second is the **`return` keyword**, which is mostly used for early returns from inside an `if` or `loop`:

```rust
fn abs(x: i32) -> i32 {
    if x < 0 {
        return -x;       // early return
    }
    x                    // trailing expression, normal return
}
```

Let's break this down.

- `fn abs(x: i32) -> i32 {` — declares a function `abs` that takes an `i32` and returns an `i32`. The function returns the absolute value of `x`.
- `if x < 0 { return -x; }` — if `x` is negative, immediately return `-x` (which is positive). The `return` keyword exits the function with the given value.
- `x` — if we reach this line, `x` is non-negative, so we return it as-is. This is the trailing expression — note the absence of a semicolon.

Note the semicolon after `return -x;` — `return` is a statement that needs a semicolon. The trailing `x` does not have one. Both are correct Rust.

### When to use `return`

Most Rust code uses the trailing-expression form for normal returns. The `return` keyword is reserved for early exits — when something goes wrong, when you find what you were looking for inside a loop, or when you need to bail out of a function before reaching the end. Using `return` for the normal case (e.g., `return x * x;` at the end of `square`) is technically valid but unidiomatic — Rust developers will look at it sideways.

---

## 4. Functions Without Return Values

If a function does not return anything meaningful, you omit the `-> Type` entirely. Rust treats this as returning `()` (the unit type):

```rust
fn greet(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    greet("Ada");
    greet("Grace");
}
```

- `fn greet(name: &str) {` — no `-> Type`, so the function returns `()` implicitly. The parameter `name` is of type `&str` (a string slice — you will meet these formally in Lesson 06).
- `println!("Hello, {}!", name);` — the body. This statement returns `()`, which matches the implicit `()` return type.

When you run this, the output is:

```text
Hello, Ada!
Hello, Grace!
```

You can also write the return type explicitly as `-> ()` if you want to be verbose, but this is rare and not recommended. Just omit the `->` for functions that do not return a meaningful value.

---

## 5. Expressions in Detail

Let's solidify the statement/expression distinction with one more example. Consider this function that computes the maximum of two numbers:

```rust
fn max(a: i32, b: i32) -> i32 {
    if a > b {
        a
    } else {
        b
    }
}
```

Let's break this down.

- `fn max(a: i32, b: i32) -> i32 {` — function declaration.
- `if a > b { a } else { b }` — this entire `if` expression is the trailing expression of the function. Whichever branch runs, its value becomes the return value of the function.
- Inside the `if` branch: `a` (no semicolon) — the value of the branch is `a`.
- Inside the `else` branch: `b` (no semicolon) — the value of the branch is `b`.
- No `return` keyword needed — the trailing expression does the work.

If you added semicolons like this:

```rust
fn max(a: i32, b: i32) -> i32 {
    if a > b {
        a;          // statement, evaluates to ()
    } else {
        b;          // statement, evaluates to ()
    }
}
```

The function would fail to compile with `mismatched types, expected \`i32\`, found \`()\``. Adding the semicolons turned `a` and `b` (expressions) into statements (which return `()`), so the `if` expression evaluates to `()`, which does not match the declared return type `i32`.

This is the #1 mistake every Rust beginner makes. The fix is always the same: **remove the semicolon after the last expression in a block**.

### A Loop Example

Recall from Lesson 04 that `loop` and `break` can return values. Now you can see why: `loop` is an expression, and `break VALUE` is what makes it evaluate to that value:

```rust
fn find_first_even(numbers: &[i32]) -> i32 {
    for n in numbers {
        if n % 2 == 0 {
            return *n;
        }
    }
    -1
}
```

Let's break this down.

- `fn find_first_even(numbers: &[i32]) -> i32 {` — the parameter is a slice of `i32` (a reference to an array, covered in Lesson 06). The function returns an `i32`.
- `for n in numbers { ... }` — iterates over the slice. Each `n` is a reference to an `i32` (`&i32`).
- `if n % 2 == 0 { return *n; }` — if `n` is even, dereference it (`*n`) and return it early.
- `-1` — if no even number was found, the trailing expression returns `-1` as a sentinel value.

This is a perfectly normal Rust function. It uses `return` for the early exit (when the even number is found) and the trailing-expression form for the normal case (when no even number was found).

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Write a Celsius-to-Fahrenheit Function

Write a function `fn celsius_to_fahrenheit(c: f64) -> f64` that returns `c * 9.0 / 5.0 + 32.0`. Call it from `main` with `0.0`, `100.0`, and `37.0`, and print the results. Confirm you get `32`, `212`, and `98.6` (approximately). Hint: do not put a semicolon after the return expression.

### Exercise 2: Write a Function with Early Return

Write a function `fn first_word(s: &str) -> &str` that returns the first word of a string (up to the first space). Use `s.split(' ').next().unwrap_or("")` as the body. (You do not need to fully understand this code yet — focus on the function signature and how to call it.) Call it from `main` with `"Hello world from Rust"` and print the result. You should see `Hello`.

### Exercise 3: Break It on Purpose

Write a function `fn double(x: i32) -> i32 { x * 2; }` — note the semicolon after `x * 2`. Try to compile it. Read the error message carefully. Then remove the semicolon and confirm it compiles and works. Write a one-sentence note explaining why the semicolon broke the function.

---

## Common Mistakes

### Mistake 1: Adding a semicolon after the last expression

```rust
// WRONG — semicolon makes the body return () instead of i32
fn double(x: i32) -> i32 {
    x * 2;
}
```

```rust
// RIGHT — no semicolon on the last expression
fn double(x: i32) -> i32 {
    x * 2
}
```

This is the most common Rust beginner mistake, full stop. Adding a semicolon to the last expression turns it into a statement, which evaluates to `()` — and then the function returns `()` instead of the declared type. The compiler error is `mismatched types, expected \`i32\`, found \`()\``. The fix is to remove the trailing semicolon. Get used to scanning the end of each function for stray semicolons.

### Mistake 2: Forgetting the parameter type

```rust
// WRONG — parameters need explicit types in Rust
fn add(a, b) {
    a + b
}
```

```rust
// RIGHT — every parameter has a type annotation
fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

Unlike local `let` bindings (where Rust infers the type), function parameters **always** need explicit type annotations. The compiler refuses to infer them. The error message is `expected one of \`:\`, \`@\`, or \`|\`, found \`)\`` — which is cryptic until you realize it means "I expected a type annotation here." Add `: Type` after each parameter name and the error goes away.

### Mistake 3: Forgetting the `->` for the return type

```rust
// WRONG — missing arrow before the return type
fn square(x: i32) i32 {
    x * x
}
```

```rust
// RIGHT — -> separates parameters from return type
fn square(x: i32) -> i32 {
    x * x
}
```

The return type is introduced with the arrow `->`, not by just putting the type after the closing paren. If you forget the arrow, the compiler will complain about a missing `->`. The arrow is borrowed from functional-programming notation and is also used in Swift and Haskell. Just remember: parameters in parens, arrow, return type.

### Mistake 4: Returning a different type than declared

```rust
// WRONG — declared i32, returning &str
fn maybe_double(x: i32) -> i32 {
    if x > 10 {
        return "too big";   // &str, not i32
    }
    x * 2
}
```

```rust
// RIGHT — both branches return i32
fn maybe_double(x: i32) -> i32 {
    if x > 10 {
        return x;   // i32
    }
    x * 2            // also i32
}
```

Every `return` statement (and the trailing expression) must produce a value of the declared return type. If you accidentally return a `&str` from a function declared `-> i32`, the compiler will say `mismatched types, expected \`i32\`, found \`&str\``. The fix is to make sure every code path actually produces a value of the declared type. If you genuinely need to return different types, use an enum (a topic for a later module).

---

## Summary

- Functions are declared with `fn name(params) -> return_type { body }`.
- Function parameters **always** need explicit type annotations — Rust does not infer them.
- A **statement** does not produce a value (`let x = 5;` is a statement). An **expression** does (`5 + 3` is an expression).
- Adding a semicolon to an expression turns it into a statement, which returns `()`.
- The **last expression in a function body** (with no semicolon) is the return value — this is the idiomatic way to return.
- Use the `return` keyword for **early returns** — when you need to exit the function before reaching the end.
- Functions without `-> Type` return `()` (the unit type, meaning "no meaningful value").
- Block expressions `{ ... }` evaluate to their last expression (no semicolon), just like function bodies.
- Every code path that produces a return value must produce the **same type** as declared.

You now have all the basic tools — variables, types, control flow, and functions — to write real Rust programs. The next lesson is the one that makes Rust *Rust*: **ownership and borrowing**. Take a deep breath, then continue. The borrow checker is about to become your strictest, most helpful teacher.

---

**Next:** [Lesson 06: Ownership and Borrowing →](./06-ownership-and-borrowing.md)
