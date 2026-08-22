# Lesson 03: Variables and Types

> Module: Rust · Lesson 3 of 7
> Estimated time: 30–45 minutes

Welcome to Lesson 03. Now that you can create and run a Cargo project, it is time to actually write Rust code. This lesson introduces **variables**, **immutability** (Rust's biggest surprise for beginners coming from Python or JavaScript), **shadowing** (a feature unique-ish to Rust), and the most important **primitive types** — the building blocks of every Rust program.

Variables in Rust look familiar but behave differently from what you might expect. The single most important thing to remember is that **Rust variables are immutable by default** — once you bind a value to a name, you cannot change that binding unless you explicitly opt in with the `mut` keyword. This is not a quirk; it is one of Rust's core safety features, and by the end of this lesson you will understand why.

---

## Learning Objectives

After this lesson, you will be able to:

1. Declare variables with `let`, both immutable and mutable, and explain why immutability is the default in Rust.
2. Use shadowing to re-declare a variable with the same name, possibly with a different type.
3. Identify Rust's primitive types: signed and unsigned integers, floats, `bool`, and `char` (a 4-byte Unicode scalar).
4. Use `const`, tuples, and arrays, and explain the difference between `char` in Rust and `char` in C.

---

## 1. Variables with `let`

You declare variables in Rust with the `let` keyword. Here is the simplest possible example:

```rust
fn main() {
    let x = 5;
    println!("x is {}", x);
}
```

Let's break this down.

- `let` — the keyword that introduces a variable binding. It tells the compiler "I am about to declare a new variable."
- `x` — the name of the variable. By convention, Rust variable names are `snake_case` (lowercase with underscores between words), just like in Python.
- `=` — the assignment operator. It binds the value on the right to the name on the left.
- `5` — an integer literal. Rust will infer this is of type `i32` (a 32-bit signed integer), the default integer type.
- `;` — the semicolon ending the statement.
- `println!("x is {}", x);` — prints `x is 5` to the terminal. The `{}` is a placeholder replaced by the value of `x`.

Now try to mutate `x` after declaring it:

```rust
fn main() {
    let x = 5;
    x = 6;  // Try to change x
    println!("x is {}", x);
}
```

Save this in `src/main.rs` and run `cargo run`. The compiler refuses to build it:

```text
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:3:5
  |
2 |     let x = 5;
  |             - first assignment to `x`
3 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable `x`
```

This is **Rust's biggest surprise for beginners**. In Python, JavaScript, and most other languages, once you write `x = 5` you can immediately write `x = 6` to overwrite it. In Rust, you cannot — `let` bindings are **immutable by default**. This is a deliberate design choice: most variables in a well-written program never need to change, and making immutability the default catches a whole class of bugs (accidentally overwriting a value) for free.

---

## 2. `mut` for Mutable Variables

When you genuinely need to change a variable's value, you opt in to mutability with the `mut` keyword:

```rust
fn main() {
    let mut x = 5;
    println!("x is {}", x);
    x = 6;
    println!("x is now {}", x);
}
```

Let's break this down.

- `let mut x = 5;` — declares a variable `x` with the initial value `5`, but the `mut` keyword marks this binding as **mutable**. You can reassign it later.
- `println!("x is {}", x);` — prints `x is 5`.
- `x = 6;` — reassigns `x` to `6`. This is now legal because `x` was declared `mut`.
- `println!("x is now {}", x);` — prints `x is now 6`.

When you run this, the output is:

```text
x is 5
x is now 6
```

### Why default immutability?

You might wonder why Rust makes you type `mut` every time you want a mutable variable. Three reasons. **First, safety**: a large fraction of bugs come from variables being mutated unexpectedly, often far from where they were declared. If a variable is immutable, you can read it anywhere without worrying that some other code path has changed it. **Second, readability**: when you see `let mut x` in code, you immediately know this value will be modified — the mutation is signposted, not hidden. **Third, optimization**: when the compiler knows a value will not change, it can do aggressive optimizations like inlining the value or reordering reads without fear of breaking anything. Default immutability is a small inconvenience that pays off in correctness and speed.

Note that immutability in Rust applies to the **binding**, not the value. If a binding is `let s = String::from("hi")`, the binding `s` cannot be reassigned, but the `String` itself (the heap-allocated data) can still be mutated through methods like `s.push_str("!")` — but only if the binding is `mut`. So `let s` means "you cannot reassign `s` AND you cannot mutate the contents of `s`," while `let mut s` allows both.

---

## 3. Shadowing

Rust has a feature called **shadowing** that lets you declare a new variable with the same name as a previous one, in the same scope. The new variable "shadows" (hides) the old one:

```rust
fn main() {
    let x = 5;
    let x = x + 1;
    let x = x * 2;
    println!("x is {}", x);
}
```

Let's break this down line by line.

- `let x = 5;` — declares `x` bound to `5`.
- `let x = x + 1;` — declares a **new** variable also named `x`, this time bound to the value of the previous `x` plus `1` — so `6`. The old `x` is shadowed; you can no longer refer to it.
- `let x = x * 2;` — declares another new `x`, bound to the value of the previous `x` times `2` — so `12`. The previous `x` is now shadowed too.
- `println!("x is {}", x);` — prints `x is 12`.

When you run this, the output is:

```text
x is 12
```

This looks like mutation, but it is not. Each `let x = ...` creates a **brand-new binding** that happens to have the same name as the previous one. The compiler treats them as completely separate variables that happen to live in the same name slot.

### Shadowing vs `mut`

You might wonder why Rust has both `mut` and shadowing. The key difference is that **shadowing lets you change the type** of the variable. With `mut`, you cannot — once `x` is an `i32`, it stays an `i32`. With shadowing, you can do this:

```rust
fn main() {
    let name = "Ada";          // name is &str (a string slice)
    let name = name.len();     // name is now usize (a number)
    println!("Name length is {}", name);
}
```

Let's break this down.

- `let name = "Ada";` — declares `name` as a `&str` (a string slice — we will cover strings in Lesson 06) bound to the text `"Ada"`.
- `let name = name.len();` — declares a **new** `name`, this time bound to the length of the previous `name` string. The new `name` has type `usize` (an unsigned integer). The type has changed from `&str` to `usize`.
- `println!("Name length is {}", name);` — prints `Name length is 3`.

If you tried this with `mut`, the compiler would refuse: `let mut name = "Ada"; name = name.len();` would fail with `mismatched types, expected \`&str\`, found \`usize\``. Shadowing gives you a clean way to transform a value through multiple types without inventing new variable names like `name_str` and `name_len`. It is most commonly used for type-transforming conversions like this one.

---

## 4. Primitive Types

Rust is **statically typed**, which means every value has a type known at compile time. The compiler can usually infer the type from how you use the value (see Section 6), but you will sometimes need to write types explicitly. Here is the full menu of primitive types.

### Integers

Rust provides both **signed** (can hold negative values) and **unsigned** (only non-negative) integers in several sizes:

| Type   | Signed? | Size     | Range (approximate)               |
|--------|---------|----------|-----------------------------------|
| `i8`   | yes     | 8 bits   | -128 to 127                       |
| `i16`  | yes     | 16 bits  | -32,768 to 32,767                 |
| `i32`  | yes     | 32 bits  | -2.1 billion to 2.1 billion       |
| `i64`  | yes     | 64 bits  | -9.2 quintillion to 9.2 quintillion |
| `i128` | yes     | 128 bits | absurdly large                    |
| `isize`| yes     | arch-dependent | pointer-sized signed int   |
| `u8`   | no      | 8 bits   | 0 to 255                          |
| `u16`  | no      | 16 bits  | 0 to 65,535                       |
| `u32`  | no      | 32 bits  | 0 to 4.3 billion                  |
| `u64`  | no      | 64 bits  | 0 to 18.4 quintillion             |
| `u128` | no      | 128 bits | absurdly large                    |
| `usize`| no      | arch-dependent | pointer-sized unsigned int |

The default integer type — the one Rust picks when you write `let x = 5;` with no other clues — is `i32`. This is large enough for almost all counting and small enough to be the fastest integer on most CPUs. Use `i64` if you expect very large numbers. Use `usize` for sizes, counts, and array indices — `array.len()` returns `usize`, and indexing with `array[0]` expects a `usize`. Use `u8` for raw byte data (e.g., reading a file's bytes). The default `i32` choice is rarely wrong for beginners.

### Floating-point numbers

Rust has two float types: `f32` (32-bit, single precision) and `f64` (64-bit, double precision). The default is `f64` because on modern CPUs it is actually faster than `f32` due to hardware optimizations. Floats follow the IEEE 754 standard — the same one used by Python's `float`, JavaScript's `Number`, and Java's `double` — so all the usual gotchas apply: `0.1 + 0.2` is not exactly `0.3`, and comparing floats with `==` is unreliable.

```rust
fn main() {
    let x = 2.0;       // f64 by default
    let y: f32 = 3.0;  // f32, explicit annotation
    println!("x + y = {}", x as f32 + y);
}
```

- `let x = 2.0;` — Rust infers `f64` from the literal.
- `let y: f32 = 3.0;` — the type annotation `: f32` forces `y` to be `f32`.
- `x as f32 + y` — casts `x` to `f32` so the types match, then adds them. You cannot add `f64` and `f32` directly.

### Boolean

The `bool` type has only two values, `true` and `false`. It is one byte in size. Booleans are usually produced by comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) and logical operators (`&&`, `||`, `!`).

```rust
fn main() {
    let is_ready = true;
    let is_admin = false;
    let can_proceed = is_ready && !is_admin;
    println!("Can proceed: {}", can_proceed);
}
```

- `let is_ready = true;` — declares a `bool` bound to `true`.
- `let is_admin = false;` — declares a `bool` bound to `false`.
- `let can_proceed = is_ready && !is_admin;` — `is_ready && !is_admin` evaluates to `true && !false` = `true && true` = `true`.

### Char

The `char` type in Rust is a **4-byte Unicode scalar value**, NOT a single byte like `char` in C. This is a critical difference. In C, a `char` is one byte and cannot represent most of the world's writing systems. In Rust, a `char` can hold any character from any language, including emoji:

```rust
fn main() {
    let letter = 'A';
    let emoji = '🎉';
    println!("letter: {}, emoji: {}", letter, emoji);
}
```

- `let letter = 'A';` — declares a `char` bound to the character `A`. Note: **single quotes** for chars, double quotes for strings. This is the same convention as in C and Java.
- `let emoji = '🎉';` — declares a `char` bound to the party popper emoji. This works because `char` is 4 bytes, big enough for any Unicode scalar value.

When you run this, the output is:

```text
letter: A, emoji: 🎉
```

This is the single most important thing to know about `char` in Rust: it is not a tiny integer like in C, it is a full Unicode code point. If you ever need a single byte (for reading binary data), use `u8` instead.

---

## 5. Type Inference

Rust has powerful **type inference** — the compiler figures out the type of each variable from how it is used. Most of the time, you do not need to write types at all:

```rust
fn main() {
    let x = 5;          // inferred as i32
    let y = 2.5;        // inferred as f64
    let z = x as f64 + y;  // z is f64
    println!("z = {}", z);
}
```

- `let x = 5;` — Rust sees the integer literal `5` and infers `i32` (the default integer type).
- `let y = 2.5;` — Rust sees the float literal `2.5` and infers `f64`.
- `let z = x as f64 + y;` — `x` is cast to `f64` so it can be added to `y` (which is `f64`). Rust infers that `z` is `f64`.

If Rust ever cannot infer the type, or if you want to be explicit for clarity, you can annotate it with a colon:

```rust
fn main() {
    let x: i32 = 5;
    let y: u64 = 10;
    let z: f64 = 3.14;
    println!("x = {}, y = {}, z = {}", x, y, z);
}
```

- `let x: i32 = 5;` — explicitly declares `x` as `i32`.
- `let y: u64 = 10;` — explicitly declares `y` as `u64`.
- `let z: f64 = 3.14;` — explicitly declares `z` as `f64`.

Note that **function parameters always require explicit types** — Rust never infers those. You will see this in Lesson 05. For local `let` bindings, type inference works perfectly and most Rust code omits the annotation when the type is obvious.

---

## 6. Constants with `const`

Rust also has **constants**, declared with the `const` keyword. Constants are different from `let` variables in several important ways:

```rust
const MAX_USERS: u32 = 1000;

fn main() {
    println!("Max users: {}", MAX_USERS);
}
```

- `const MAX_USERS: u32 = 1000;` — declares a constant named `MAX_USERS` of type `u32` with the value `1000`. By convention, constant names are `SCREAMING_SNAKE_CASE`.

The differences between `const` and `let`:

1. **Constants must always have an explicit type annotation.** Rust does not infer types for constants.
2. **Constants cannot use `mut`.** They are immutable by definition.
3. **Constants can be declared at any scope**, including the global (top-level) scope — outside of any function. `let` bindings can only be inside a function body.
4. **Constants must be set to a value that can be determined at compile time**, not something computed at runtime. `const MAX: u32 = 1000;` works; `const NOW: u32 = some_function();` does not.

Use constants for fixed, named values that do not change — like `MAX_USERS`, `API_VERSION`, `PI`, or `DEFAULT_TIMEOUT`. They make your code more readable and let you change the value in one place.

---

## 7. Tuples

A **tuple** is a fixed-length collection of values of possibly different types. Tuples are written as comma-separated values inside parentheses:

```rust
fn main() {
    let point: (i32, i32) = (3, 4);
    let (x, y) = point;          // destructuring
    println!("x = {}, y = {}", x, y);
    println!("point.0 = {}, point.1 = {}", point.0, point.1);
}
```

- `let point: (i32, i32) = (3, 4);` — declares a tuple of two `i32` values. The type annotation `(i32, i32)` is optional — Rust would infer it.
- `let (x, y) = point;` — **destructures** the tuple: binds `x` to `3` and `y` to `4` in one line.
- `point.0` and `point.1` — access tuple elements by index (starting from 0). The `.` syntax works because tuples have a fixed length known at compile time.

Tuples can hold different types:

```rust
fn main() {
    let mixed: (i32, f64, &str) = (42, 3.14, "hello");
    println!("{} {} {}", mixed.0, mixed.1, mixed.2);
}
```

- `let mixed: (i32, f64, &str) = (42, 3.14, "hello");` — a tuple of three different types: an integer, a float, and a string slice.

A tuple with no elements is written `()` and is called the **unit type**. It is the default return type of functions that do not return a value (you will see this in Lesson 05).

---

## 8. Arrays

An **array** is a fixed-length collection of values of the **same** type, stored contiguously in memory (on the stack):

```rust
fn main() {
    let nums = [1, 2, 3, 4, 5];
    let first = nums[0];
    let last = nums[4];
    println!("First: {}, Last: {}", first, last);
    println!("Length: {}", nums.len());
}
```

- `let nums = [1, 2, 3, 4, 5];` — declares an array of 5 `i32` values.
- `nums[0]` — accesses the first element (arrays are zero-indexed).
- `nums[4]` — accesses the fifth element (the last).
- `nums.len()` — returns the length of the array, which is `usize`.

You can also declare an array of repeated values:

```rust
fn main() {
    let zeros = [0; 10];   // 10 zeros
    println!("zeros has {} elements, first is {}", zeros.len(), zeros[0]);
}
```

- `let zeros = [0; 10];` — declares an array of 10 elements, each initialized to `0`. The semicolon inside the brackets separates the value (`0`) from the count (`10`).

**Important**: Rust arrays have a **fixed length** that cannot change. If you need a growable array, use `Vec<T>` (a heap-allocated vector) — but you will meet that in a later module. Also, accessing an array out of bounds causes a **runtime panic** in Rust (the program crashes safely, rather than reading random memory like in C):

```rust
fn main() {
    let nums = [1, 2, 3];
    let bad = nums[10];   // panics at runtime: index out of bounds
    println!("{}", bad);
}
```

If you run this in debug mode, the program will panic with `index out of bounds: the len is 3 but the index is 10`. This is a safety feature: Rust would rather crash cleanly than silently read garbage memory.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Immutable vs Mutable

Write a program that declares a variable `let x = 5;`, then attempts to reassign `x = 6;`. Confirm you get a compile error. Then change `let x` to `let mut x` and confirm the program now compiles and prints `6`. Write a one-line comment in your own words explaining what the error meant.

### Exercise 2: Shadowing Through Types

Declare a variable `let s = "42"` (a `&str`), then shadow it with `let s = s.parse::<i32>().unwrap();` to convert it to an integer. Print the result and its type using `std::any::type_name::<>()` (look this up if you are unsure). Notice that the type of `s` changed from `&str` to `i32` without using `mut` — this is the key benefit of shadowing.

### Exercise 3: Build a Tuple and Array

Declare a tuple `let person = ("Ada", 36, true);` representing a name, age, and is-admin flag. Destructure it with `let (name, age, is_admin) = person;` and print each piece. Then declare an array `let scores = [85, 90, 78, 92, 88];` and print the average by summing the elements and dividing by `scores.len()`. Hint: use a `for` loop or convert the elements to `f64` to avoid integer division truncation.

---

## Common Mistakes

### Mistake 1: Mutating an immutable variable

```rust
// WRONG — x was declared immutable
fn main() {
    let x = 5;
    x = 6;
    println!("{}", x);
}
```

```rust
// RIGHT — add mut to allow mutation
fn main() {
    let mut x = 5;
    x = 6;
    println!("{}", x);
}
```

The single most common beginner error in Rust. If you forget `mut`, the compiler will refuse with `cannot assign twice to immutable variable \`x\``. The fix is always the same: add `mut` between `let` and the variable name. Only add `mut` when you actually need to mutate — leaving it off is a signal to readers that the variable will not change.

### Mistake 2: Type mismatch between integer types

```rust
// WRONG — adding i32 to i64 is a type error in Rust
fn main() {
    let a: i32 = 5;
    let b: i64 = 10;
    let c = a + b;
    println!("{}", c);
}
```

```rust
// RIGHT — explicitly convert to a common type
fn main() {
    let a: i32 = 5;
    let b: i64 = 10;
    let c = (a as i64) + b;
    println!("{}", c);
}
```

Rust does not silently promote between integer types. `i32 + i64` is a type error, not an automatic conversion. The compiler will say `cannot add \`i64\` to \`i32\``. To fix it, use the `as` operator to convert one of them to the other type. This is a deliberate choice: silent integer promotions are a classic source of bugs in C and C++.

### Mistake 3: Integer overflow panics in debug mode

```rust
// WRONG — 200 + 200 does not fit in u8 (max 255), panics in debug
fn main() {
    let x: u8 = 200;
    let y: u8 = 200;
    let z = x + y;
    println!("{}", z);
}
```

```rust
// RIGHT — use a larger integer type
fn main() {
    let x: u16 = 200;
    let y: u16 = 200;
    let z = x + y;
    println!("{}", z);
}
```

In debug mode, integer overflow causes a **runtime panic** — the program halts with a clear error message. This is a safety feature to catch bugs early. In release mode (`cargo build --release`), overflow wraps silently (using two's-complement arithmetic), which matches the behavior of most C compilers with `-O2`. If you actually want wrapping behavior in debug mode too, use the explicit `wrapping_add` method: `x.wrapping_add(y)`. The panic is the right default for beginners — it forces you to pick the right integer size.

### Mistake 4: Adding a float and an int directly

```rust
// WRONG — cannot add f64 to i32
fn main() {
    let x = 5;      // i32
    let y = 2.5;    // f64
    let z = x + y;
    println!("{}", z);
}
```

```rust
// RIGHT — convert the int to float first
fn main() {
    let x = 5;      // i32
    let y = 2.5;    // f64
    let z = (x as f64) + y;
    println!("{}", z);
}
```

Like with the integer types, Rust refuses to silently convert between `i32` and `f64`. You must explicitly cast with `as`. If you forget, the compiler will say `cannot add \`f64\` to \`i32\``. This sounds annoying at first, but it prevents a whole class of subtle bugs where you accidentally lose precision by mixing int and float arithmetic.

---

## Summary

- Variables are declared with `let`. By default, they are **immutable** — you cannot reassign them.
- Use `let mut` to declare a mutable variable that can be reassigned. Use it sparingly — only when mutation is truly needed.
- **Shadowing** lets you declare a new variable with the same name as an old one, possibly with a different type. This is how you transform values through types cleanly.
- Rust's signed integer types are `i8`, `i16`, `i32`, `i64`, `i128`, and `isize`. Unsigned types are `u8` through `u128` and `usize`. The default is `i32`.
- Float types are `f32` and `f64`; the default is `f64`. Booleans are `bool` (`true` / `false`). Characters are `char`, a 4-byte Unicode scalar — not a single byte like in C.
- Rust has powerful **type inference** — you usually do not need to write types on `let` bindings.
- Constants are declared with `const NAME: Type = value;` — they require explicit types, are always immutable, and must be compile-time-known.
- Tuples group values of different types: `(1, 2.5, "hi")`. Arrays hold fixed-length same-type values: `[1, 2, 3]`.
- Out-of-bounds array access panics at runtime in debug mode — a safety feature that prevents reading random memory.

You now know the basic data types Rust offers. In Lesson 04 you will learn how to make decisions and repeat work — `if` expressions, `loop`, `while`, `for`, and Rust's powerful `match`.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
