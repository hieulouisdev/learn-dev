# Lesson 07: Rust Cheatsheet

> Module: Rust · Lesson 7 of 7
> Estimated time: 20–30 minutes

Welcome to the final lesson of Module 14. This lesson is not a teaching lesson — it is a **reference card**. Everything you learned in Lessons 01 through 06 is collected here in one place, organized by topic, with the most important syntax and idioms ready to copy. Bookmark this page or print it out, and come back to it whenever you forget a syntax detail or need a quick reminder of how the borrow checker thinks.

The second half of this cheatsheet also introduces a few topics that did not fit into the seven lessons but that you will see the moment you start a real Rust project: the `Option` and `Result` types, the `?` operator, the most popular third-party crates on [crates.io](https://crates.io/), and a list of common pitfalls that catch every Rust beginner at least once. Skim those sections now so you know they exist — when you encounter them in the wild, you will remember where to look.

---

## Learning Objectives

After this lesson, you will be able to:

1. Quickly look up Rust syntax for any construct you learned in this module.
2. Identify the standard library types `Option<T>`, `Result<T, E>`, `Vec<T>`, and `HashMap<K, V>` and know when to use each.
3. Recognize the ten most popular third-party crates (libraries) used in real Rust code.
4. Avoid the four most common pitfalls that catch Rust beginners.

---

## 1. Hello, World!

The minimal Rust program. Every executable starts with this skeleton:

```rust
fn main() {
    println!("Hello, world!");
}
```

- `fn main()` — the entry point of the program.
- `println!` — the print-line macro. The `!` marks it as a macro.
- `"Hello, world!"` — a string literal, in double quotes.
- `;` — semicolon ending the statement.

---

## 2. Cargo Commands

Cargo is Rust's build tool, package manager, test runner, formatter, and linter all in one. These are the commands you will use every day.

| Command | What it does |
|---------|--------------|
| `cargo new <name>` | Create a new binary project in a folder `<name>/`. |
| `cargo new --lib <name>` | Create a new library project instead of a binary. |
| `cargo build` | Compile the project (debug profile). Produces `target/debug/<name>`. |
| `cargo build --release` | Compile with full optimizations. Produces `target/release/<name>`. |
| `cargo run` | Compile (if needed) and immediately run the binary. |
| `cargo check` | Type-check the code without producing a binary — fastest way to find errors. |
| `cargo fmt` | Format all source files in the project according to Rust style. |
| `cargo clippy` | Run the official linter for additional warnings and style suggestions. |
| `cargo test` | Run all unit tests and doctests in the project. |
| `cargo doc --open` | Generate HTML documentation and open it in your browser. |
| `cargo clean` | Delete the `target/` folder for a truly clean rebuild. |
| `cargo update` | Update the dependencies in `Cargo.lock` to the latest compatible versions. |

---

## 3. Variables

```rust
let x = 5;                  // immutable (default) — cannot reassign
let mut y = 5; y = 6;       // mutable — can reassign
const MAX: u32 = 1000;      // constant — always immutable, requires explicit type
let s = "hi"; let s = s.len();  // shadowing — new binding with same name, can change type
```

- `let` declares a variable. Variables are **immutable by default** — use `mut` to make them reassignable.
- `const NAME: Type = value;` declares a compile-time constant. Must have an explicit type. Must be `SCREAMING_SNAKE_CASE`.
- **Shadowing** lets you re-declare a variable with the same name, possibly with a different type. Each `let` creates a new binding.

---

## 4. Primitive Types

| Type(s) | Category | Notes |
|---------|----------|-------|
| `i8`, `i16`, `i32`, `i64`, `i128`, `isize` | signed integers | Default is `i32`. `isize` is pointer-sized. |
| `u8`, `u16`, `u32`, `u64`, `u128`, `usize` | unsigned integers | `usize` is for sizes and indexing. |
| `f32`, `f64` | floating-point | Default is `f64`. IEEE 754. |
| `bool` | boolean | `true` or `false`. One byte. |
| `char` | Unicode scalar | **4 bytes** — can hold emoji, any Unicode code point. |
| `(T1, T2, ...)` | tuple | Fixed length, mixed types. Access via `.0`, `.1`, etc. |
| `[T; N]` | array | Fixed length, single type. On stack. |
| `()` | unit | The "no meaningful value" type. Default function return. |

---

## 5. Strings: `String` vs `&str`

Rust has two string types, and confusing them is a rite of passage. They are different but interoperable.

| Aspect | `String` | `&str` |
|--------|---------|--------|
| Storage | Heap-allocated, growable | Borrows from somewhere (a `String`, a literal, etc.) |
| Ownership | Owned | Borrowed (a reference) |
| Mutability | Mutable if declared `mut` | Always immutable (a `&` reference) |
| Created with | `String::from(...)`, `s.to_string()`, `format!()` | String literals `"..."`, `&s`, `&s[0..5]` |
| Use when | You need to build, modify, or own text | You need to read or pass text around |

```rust
let owned: String = String::from("hello");   // owned, mutable
let borrowed: &str = "world";                // borrowed, immutable
let also_borrowed: &str = &owned;            // borrowed view of the owned String
```

Most function parameters should be `&str` (accepts both `&String` and `&str` via deref coercion). Most return values should be `String` (gives the caller ownership).

---

## 6. Control Flow

### `if` (expression — returns a value)

```rust
let n = if cond { 5 } else { 10 };   // both branches must return the same type
if x > 0 {
    println!("positive");
} else if x == 0 {
    println!("zero");
} else {
    println!("negative");
}
```

### `loop` (infinite loop, `break` exits — and can return a value)

```rust
let result = loop {
    if done { break 42; }   // break carries a value out
};
```

### `while`

```rust
while n > 0 {
    n -= 1;
}
```

### `for` (with range or iterator)

```rust
for i in 0..5 { /* 0,1,2,3,4 */ }     // exclusive
for i in 0..=5 { /* 0,1,2,3,4,5 */ }  // inclusive
for x in arr.iter() { /* each element */ }
for (i, x) in arr.iter().enumerate() { /* (index, value) pairs */ }
```

### `match` (exhaustive — must cover all cases or use `_`)

```rust
let s = match x {
    1 => "one",
    2 | 3 => "small",
    4..=10 => "medium",
    _ => "large",
};
```

### `if let` (concise form for matching one case)

```rust
if let Some(v) = opt {
    println!("got {}", v);
} else {
    println!("none");
}
```

---

## 7. Functions

```rust
fn add(a: i32, b: i32) -> i32 {   // params need explicit types; -> Type for return
    a + b                          // trailing expression, no semicolon — this is the return value
}

fn early_return(x: i32) -> i32 {
    if x < 0 {
        return -x;                // `return` keyword for early exit
    }
    x                             // trailing expression for normal return
}

fn no_return() {                  // no `->`, implicitly returns ()
    println!("hi");
}
```

- Parameters **always** need explicit type annotations.
- The **last expression** in the body (with no semicolon) is the return value.
- A **statement** (with semicolon) returns `()`. An **expression** (no semicolon) returns its value.
- Adding a semicolon to the last expression accidentally returns `()` — the most common Rust beginner mistake.

---

## 8. Ownership Cheat Card

| Situation | What happens | Code |
|-----------|-------------|------|
| `let s2 = s1;` for `String` | **Move** — `s1` is invalidated | `let s1 = String::from("hi"); let s2 = s1;` |
| `let y = x;` for `i32` | **Copy** — both still valid | `let x = 5; let y = x;` |
| `s1.clone()` | **Deep copy** — both still valid | `let s2 = s1.clone();` |
| `fn f(s: String)` | **Move into** the function | caller loses `s` |
| `fn f(s: &String)` | **Borrow immutably** — caller keeps `s` | can have many `&` at once |
| `fn f(s: &mut String)` | **Borrow mutably** — caller keeps `s` | only ONE `&mut` at a time |
| `fn f() -> &String` | **Dangling ref** — usually an error | return owned `String` instead |
| `&s[0..5]` | **Slice** — borrows a chunk of `s` | works on `String`, `&str`, `Vec`, arrays |

**Golden rule:** at any moment, you can have EITHER one `&mut T` OR any number of `&T` — never both. The borrow checker enforces this at compile time.

---

## 9. Common Idioms

These are the standard library types and patterns every Rust developer uses daily.

### `Option<T>` — use instead of `null`

```rust
let maybe: Option<i32> = Some(42);
match maybe {
    Some(v) => println!("got {}", v),
    None => println!("nothing"),
}

// or with if let:
if let Some(v) = maybe {
    println!("got {}", v);
}

// or with unwrap (panics on None — use sparingly):
let v = maybe.unwrap();
```

Rust has no `null`. Functions that might not have a value return `Option<T>` — either `Some(value)` or `None`.

### `Result<T, E>` — use instead of exceptions

```rust
use std::fs;

let content: Result<String, std::io::Error> = fs::read_to_string("file.txt");

match content {
    Ok(s) => println!("file content: {}", s),
    Err(e) => eprintln!("error: {}", e),
}
```

Functions that can fail return `Result<T, E>` — either `Ok(value)` or `Err(error)`. Rust has no `try`/`catch` — error handling is done through `Result`.

### The `?` operator — short-circuit error propagation

```rust
use std::fs;

fn read_config() -> Result<String, std::io::Error> {
    let s = fs::read_to_string("config.txt")?;   // returns Err automatically on failure
    Ok(s)
}
```

The `?` after an expression says: "if this is `Ok`, unwrap it; if it is `Err`, return the error from the current function immediately." This is Rust's idiomatic way to propagate errors without writing boilerplate `match` blocks.

### `Vec<T>` — growable heap array

```rust
let mut v: Vec<i32> = Vec::new();
v.push(1);
v.push(2);
v.push(3);
println!("{}", v[0]);         // 1
println!("{}", v.len());      // 3

// or use the vec! macro:
let nums = vec![1, 2, 3, 4, 5];
```

### `HashMap<K, V>` — key-value map

```rust
use std::collections::HashMap;

let mut scores: HashMap<&str, i32> = HashMap::new();
scores.insert("Alice", 10);
scores.insert("Bob", 7);

if let Some(score) = scores.get("Alice") {
    println!("Alice: {}", score);   // 10
}
```

### Other useful macros

```rust
let s = format!("{}/{}", "usr", "bin");   // "usr/bin" — String
let v = vec![1, 2, 3];                    // Vec<i32>
assert!(1 + 1 == 2);                      // panics if false
assert_eq!(2 + 2, 4);                     // panics if not equal
panic!("something went wrong");           // immediate crash with message
```

---

## 10. Common Pitfalls

Every Rust beginner hits these. Memorize them so you recognize the error messages.

### Pitfall 1: Moving then using

```rust
// WRONG
let s1 = String::from("hi");
let s2 = s1;
println!("{}", s1);   // error: borrow of moved value `s1`

// RIGHT
let s1 = String::from("hi");
let s2 = s1.clone();  // or use s2 only
println!("{}", s1);
```

### Pitfall 2: Mutable + immutable borrows at the same time

```rust
// WRONG
let mut s = String::from("hi");
let r1 = &s;
let r2 = &mut s;          // error: cannot borrow as mutable while immutably borrowed
println!("{} {}", r1, r2);

// RIGHT — finish using &s before creating &mut s
let mut s = String::from("hi");
let r1 = &s;
println!("{}", r1);       // last use of r1
let r2 = &mut s;          // now safe
println!("{}", r2);
```

### Pitfall 3: Integer overflow panics in debug mode

```rust
// Panics in debug mode at runtime
let x: u8 = 200;
let y: u8 = 100;
let z = x + y;            // 300 > 255, panic: attempt to add with overflow

// Use a larger type, or use wrapping_* methods if you really want wraparound:
let z = x.wrapping_add(y);
```

### Pitfall 4: `String` vs `&str` confusion

```rust
// WRONG — function wants &str, you passed String (no automatic conversion at call boundary)
fn greet(name: &str) { println!("hi {}", name); }

let owned: String = String::from("Ada");
greet(owned);             // ERROR: expected &str, found String

// RIGHT — pass &owned (a reference to the String), which coerces to &str
greet(&owned);

// Or pass a literal directly (it is already &str):
greet("Ada");
```

Actually, in modern Rust the borrow checker can do this conversion automatically in many contexts via "deref coercion" — but it never hurts to be explicit with `&`.

---

## 11. The 10 Most-Used Crates

Rust's package registry [crates.io](https://crates.io/) hosts over 100,000 third-party libraries. Here are the ten you will encounter in almost every real Rust project. Add them to your `Cargo.toml` under `[dependencies]` to use them.

| Crate | What it does |
|-------|--------------|
| [`serde`](https://crates.io/crates/serde) | Serialization and deserialization — convert between Rust structs and JSON, YAML, TOML, etc. The de facto standard. |
| [`tokio`](https://crates.io/crates/tokio) | Async runtime — the engine that powers almost all async Rust code, including HTTP servers and database drivers. |
| [`rand`](https://crates.io/crates/rand) | Random number generation — `rand::random()`, `rand::thread_rng()`, and more. |
| [`clap`](https://crates.io/crates/clap) | Command-line argument parser — build CLI tools with subcommands, flags, and help text. |
| [`anyhow`](https://crates.io/crates/anyhow) | Error-handling library — makes `Result<T, anyhow::Error>` easy by auto-converting any error type. |
| [`reqwest`](https://crates.io/crates/reqwest) | HTTP client — `reqwest::get(url).await?.text().await?`. Built on top of `tokio` and `hyper`. |
| [`regex`](https://crates.io/crates/regex) | Regular expressions — fast, Unicode-aware regex engine. |
| [`chrono`](https://crates.io/crates/chrono) | Date and time — parsing, formatting, arithmetic on dates and times. |
| [`log`](https://crates.io/crates/log) | Logging facade — `info!`, `warn!`, `error!`, `debug!` macros that work with any logging backend. |
| [`env_logger`](https://crates.io/crates/env_logger) | Logging backend for `log` — configure log levels via the `RUST_LOG` environment variable. |

To use any of these, add them to your `Cargo.toml`:

```toml
[dependencies]
serde = { version = "1.0", features = ["derive"] }
rand = "0.8"
```

Then run `cargo build` — Cargo downloads the crates, compiles them, and links them into your project automatically. You can browse all crates and read their documentation at [docs.rs](https://docs.rs/) (auto-generated docs for every published crate) and [lib.rs](https://lib.rs/) (a curated, opinionated index of high-quality crates).

---

## 12. What's Next?

You have finished the Rust module. You now know enough to:

- Set up a Rust project with `cargo new`.
- Write functions, control flow, and basic data structures.
- Understand the ownership and borrowing model — Rust's defining feature.
- Read most beginner-level Rust code without confusion.
- Recognize the most popular crates and idioms in real Rust projects.

The natural next step is to **build something real**. Try these projects to solidify your knowledge:

1. A **command-line tool** that reads a file, does some processing, and writes the result. Use `clap` for argument parsing and `anyhow` for error handling. You will meet `Result`, `?`, and `String` vs `&str` in real contexts.
2. A **simple HTTP server** using `axum` or `actix-web`. You will see async Rust and learn about `tokio`, futures, and how ownership plays out across network boundaries.
3. A **small game** like Conway's Game of Life or Tetris using `crossterm` for terminal rendering. This will exercise your `Vec`, loops, and `match` skills.

If you want a structured next step, the free official book [The Rust Programming Language](https://doc.rust-lang.org/book/) covers everything in this module and far more — including structs, enums, traits, generics, lifetimes, concurrency, and unsafe Rust. Read it after this module to take your Rust from beginner to intermediate.

For the curriculum, your next stop is Module 15, where you will meet **Go** — another modern systems language with a different philosophy. Go also compiles to native binaries, also has no garbage collector (well, it has a simpler one), and is famously used at Google, Uber, Twitch, and Docker. Comparing Rust and Go is one of the best ways to deepen your understanding of both, because they make opposite trade-offs: Rust optimizes for safety and zero-cost abstractions, Go optimizes for simplicity and fast compilation.

You should be proud of getting this far. Rust is not easy, and completing a module on ownership and borrowing puts you ahead of most people who ever try the language. See you in Module 15.

---

**Next:** [Module 15: Go →](/home/z/my-project/learn-dev/15-go/)
