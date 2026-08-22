# Lesson 06: Ownership and Borrowing

> Module: Rust · Lesson 6 of 7
> Estimated time: 45–60 minutes

Welcome to Lesson 06 — the defining lesson of this entire module. Everything you have learned so far (variables, types, control flow, functions) exists in every programming language. What you will learn in this lesson exists in only one: Rust. **Ownership and borrowing** are how Rust delivers on its central promise — memory safety without a garbage collector — and they are the reason Rust feels different from every other language you have ever used.

This lesson is harder than the others. Read it slowly. Type every example. When the compiler rejects your code with a confusing error, do not get frustrated — read the error message carefully, because Rust's errors are famously helpful and they will teach you the rules. By the end you will understand memory in a way that makes every other language you ever learn feel simpler.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain why ownership exists and what problem it solves (no GC, no manual `free`).
2. State the three rules of ownership and predict when a value is "dropped."
3. Explain move semantics: why `let s2 = s1;` invalidates `s1` for `String` but not for `i32`.
4. Use references (`&T` and `&mut T`) to lend data without taking ownership, and respect the borrow checker's rules.
5. Use slices (`&str`, `&[T]`) to view contiguous data without owning it.

---

## 1. Why Ownership?

Every program needs memory to do its work. Variables, strings, lists, images, network buffers — all of them consume RAM. Memory must be **allocated** (reserved for your use) and eventually **freed** (returned to the operating system so other programs can use it). If you free memory too early, your program reads garbage and crashes. If you free it twice, the operating system panics. If you never free it, your program "leaks" and eventually eats all available RAM.

Languages solve this problem in two main ways. **Manual memory management** (C, C++) makes the programmer call `malloc` and `free` by hand — fast and predictable, but the source of 70% of all security vulnerabilities in large C/C++ codebases (use-after-free, double-free, buffer overflows). **Garbage collection** (Java, Python, JavaScript, Go, C#) has a runtime that periodically scans memory and frees what is no longer reachable — safe and convenient, but it adds a runtime cost, pauses your program unpredictably, and consumes extra memory.

Rust proposes a third option: **ownership**. There is no garbage collector, and the programmer never calls `free` explicitly. Instead, the **compiler** tracks which piece of code "owns" each value, and at compile time decides when each value should be freed. The rule is simple: when the owner goes out of scope, the value is automatically dropped (freed). Because this all happens at compile time, the runtime cost is **zero** — your program runs as fast as hand-written C, but it cannot have a use-after-free or double-free, because the compiler refuses to build such a program.

The cost of this approach is that you must think about ownership when you write code. In Python, you can pass a list to a function and forget about it; the garbage collector handles cleanup. In Rust, passing a value to a function might **move** ownership into that function — and then you cannot use the original variable anymore. This sounds annoying at first, but it is what gives Rust its safety and speed. Ownership is the trade-off that buys you the rest of Rust's benefits.

---

## 2. The Rules of Ownership

Rust's ownership system has three rules. Memorize them, because every error message from the borrow checker ultimately traces back to one of these rules being violated.

1. **Each value has an owner.** At any moment, exactly one variable or function parameter is the "owner" of a given piece of data.
2. **There is only one owner at a time.** When you assign a value to a new variable or pass it to a function, ownership moves (the old owner is invalidated) — unless the type implements the special `Copy` trait (see Section 5).
3. **When the owner goes out of scope, the value is dropped.** At the closing `}` of the block where the owner was declared, Rust automatically calls the value's "destructor" (which frees any heap memory it holds).

Let's see rule 3 in action with a `String` — Rust's growable, heap-allocated string type:

```rust
fn main() {
    {
        // s is not yet declared, so no String exists yet
        let s = String::from("hello");
        // s is in scope — the String is valid here
        println!("{}", s);
    }   // <-- s goes out of scope here. The String is dropped (its heap memory is freed).

    // If we tried to use s here, it would be a compile error.
    // println!("{}", s);   // uncommenting this line fails to compile
}
```

Let's break this down.

- `let s = String::from("hello");` — declares `s` as a `String`. `String::from("hello")` allocates heap memory to store the text `"hello"` and returns a `String` that owns that heap memory. The variable `s` is the owner.
- `println!("{}", s);` — uses `s` while it is in scope. Prints `hello`.
- The inner `{ }` block ends. At the closing `}`, `s` goes out of scope. Rust automatically calls `String`'s "drop" function, which frees the heap memory that `s` owned. After this point, `s` no longer exists and the memory it held has been returned to the operating system.

This automatic drop happens for every owned type — `String`, `Vec<T>`, `HashMap<K,V>`, and any struct you define that owns data. You never call `free` or `delete` yourself; the compiler inserts the calls for you at the right point in the program. This is rule 3 in action.

---

## 3. Move Semantics

Now for the second big Rust surprise (after default immutability). When you assign a value to a new variable, or pass it to a function, **ownership moves** — the original variable is invalidated:

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;            // ownership moves from s1 to s2

    println!("{}", s1);     // ERROR: s1 was moved
}
```

Save this and run `cargo build`. The compiler refuses:

```text
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:4:20
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`,
  |            which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |     println!("{}", s1);
  |                    ^^ value borrowed here after move
```

This is **move semantics**. After `let s2 = s1;`, the variable `s1` no longer holds a valid value. The compiler literally refuses to let you use `s1` after this point. The new owner is `s2`.

### Why does ownership move? The double-free problem

You might be wondering: why does Rust invalidate `s1`? Why not just copy the data, like in Python? The answer lies in how `String` is represented in memory. A `String` is not just the text — it is a small struct on the stack containing three things: a **pointer** to the heap-allocated text data, a **length**, and a **capacity**. The actual text (`"hello"`) lives on the heap, and the `String` struct on the stack just points to it.

```text
   String (stack)                heap
   ┌─────────────┐
   │ ptr  ───────────────────►  "hello"
   │ len  = 5     │             (5 bytes)
   │ cap  = 5     │
   └─────────────┘
```

When you write `let s2 = s1;`, Rust does a **shallow copy** of the stack struct: it copies the pointer, length, and capacity into `s2`. It does **not** copy the heap text. So now both `s1` and `s2` point to the same heap memory:

```text
   s1 (stack)                  heap
   ┌─────────────┐
   │ ptr  ───────────────────►  "hello"
   │ len  = 5     │             ▲
   └─────────────┘             │
                               │
   s2 (stack)                  │
   ┌─────────────┐             │
   │ ptr  ───────────────────►  │
   │ len  = 5     │
   │ cap  = 5     │
   └─────────────┘
```

If Rust left both `s1` and `s2` valid, then when `s1` goes out of scope, Rust would free the heap memory — and then when `s2` goes out of scope, Rust would free the **same** heap memory again. This is the classic **double-free** bug, which can corrupt the heap and crash the program (or worse, create a security vulnerability). In C, this is a constant source of bugs. In Rust, the compiler prevents it by **moving** ownership: after `let s2 = s1;`, only `s2` is valid, and only `s2`'s drop will free the heap.

So Rust refuses to compile the program. The fix is one of three things:

1. Use `s2` instead of `s1` after the move (most common — you usually wanted the new name anyway).
2. Clone the data with `s1.clone()` (Section 4).
3. Borrow with references instead of moving (Section 6).

### Moves happen with function calls too

Passing a value to a function also moves ownership:

```rust
fn takes_ownership(s: String) {
    println!("I now own: {}", s);
}   // s goes out of scope here and is dropped

fn main() {
    let s1 = String::from("hello");
    takes_ownership(s1);
    // println!("{}", s1);   // ERROR: s1 was moved into the function
}
```

Let's break this down.

- `fn takes_ownership(s: String) { ... }` — declares a function that takes a `String` **by value**. The parameter `s` becomes the new owner of the data passed in.
- `let s1 = String::from("hello");` — `s1` is the original owner of the string.
- `takes_ownership(s1);` — passing `s1` to the function moves ownership into the parameter `s`. After this call, `s1` is no longer valid.
- Inside `takes_ownership`, `s` is in scope and the string is usable.
- When `takes_ownership` returns, `s` goes out of scope and the string is dropped. The heap memory is freed.
- Back in `main`, `s1` cannot be used anymore — it was moved.

This is why passing a `String` to a function is a big deal in Rust: you lose the string after the call. If you want to keep using it, you need to use references (Section 6) or to clone it before passing.

---

## 4. Clone for Deep Copy

If you genuinely want a deep copy of a `String` — that is, a new `String` with its own heap allocation containing the same text — use the `.clone()` method:

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();    // deep copy — s1 is still valid

    println!("s1 = {}, s2 = {}", s1, s2);
}
```

Let's break this down.

- `let s1 = String::from("hello");` — `s1` is the owner of a heap-allocated string `"hello"`.
- `let s2 = s1.clone();` — calls the `clone()` method on `s1`, which allocates a new block of heap memory, copies the text `"hello"` into it, and returns a new `String` that owns that new heap memory. The result is bound to `s2`. Both `s1` and `s2` are now valid, and they point to two independent heap allocations, each containing `"hello"`.
- `println!("s1 = {}, s2 = {}", s1, s2);` — works, because `s1` was never moved.

When you run this, the output is:

```text
s1 = hello, s2 = hello
```

`.clone()` is expensive — it allocates heap memory and copies bytes. That is exactly the point: by making the deep copy **explicit** (you have to type `.clone()`), Rust makes it impossible to accidentally trigger an expensive copy. In languages with implicit copying (like C++ when copy constructors run silently), it is easy to write code that copies huge data structures on every function call without realizing it, killing performance. In Rust, if you see `.clone()` in code, you know someone decided to pay for a copy.

Use `.clone()` when:
- You need to pass a value to a function that takes ownership, but you also need to keep using the original.
- You need two independent copies that you will modify differently.
- The data is small (a short string, a few numbers) so the cost is negligible.

Avoid `.clone()` in tight loops on large data — that is when the cost actually matters.

---

## 5. The Copy Trait

Now you might be wondering: when I write `let x = 5; let y = x;`, does `x` get moved too? The answer is **no** — `x` is still valid afterward. Why is `i32` different from `String`?

```rust
fn main() {
    let x = 5;
    let y = x;              // x is NOT moved — it is copied

    println!("x = {}, y = {}", x, y);   // both work fine
}
```

- `let x = 5;` — `x` is an `i32`.
- `let y = x;` — `y` gets a **copy** of `x`. `x` is still valid.
- `println!("x = {}, y = {}", x, y);` — prints `x = 5, y = 5`. Both variables are usable.

This works because `i32` implements a special trait called `Copy`. Types that implement `Copy` are **cheap to copy bitwise** — they fit entirely on the stack, with no heap pointers, no destructors, and no need to track ownership. When you assign or pass them, Rust just copies the bytes, and both copies are valid. The `Copy` trait is implemented for all primitive types: `i8`, `i16`, `i32`, `i64`, `i128`, `isize`, `u8`-`u128`, `usize`, `f32`, `f64`, `bool`, and `char`. It is also implemented for tuples and arrays **if all their elements are `Copy`** (so `(i32, i32)` is `Copy`, but `(String, i32)` is not).

Types that own heap memory — `String`, `Vec<T>`, `HashMap<K,V>`, `Box<T>` — do **not** implement `Copy`. They implement only `Clone` (which is the more expensive, explicit deep-copy version). The compiler enforces a rule: a type can only be `Copy` if all of its components are `Copy`. This is why `String` cannot be `Copy`: it contains a heap pointer, and copying that pointer without copying the heap data would lead to the double-free we discussed earlier.

The practical takeaway: **primitive types copy on assignment, owning types move on assignment**. You never need to write `.clone()` for `i32`, `f64`, `bool`, or `char`. You do need to write it for `String`, `Vec`, and similar.

---

## 6. References and Borrowing

If passing a `String` to a function moves ownership (and you lose the string), how do you write a function that *uses* a string without taking it away from the caller? The answer is **references**. A reference is a pointer that lets you read or modify data **without taking ownership**. The act of creating a reference is called **borrowing** — you are borrowing the value from its owner, with a promise to give it back.

```rust
fn print_str(s: &String) {
    println!("I borrowed: {}", s);
}

fn main() {
    let s1 = String::from("hello");
    print_str(&s1);                       // borrow s1, do not move it
    println!("s1 is still valid: {}", s1); // s1 is still owned here
}
```

Let's break this down.

- `fn print_str(s: &String) { ... }` — declares a function whose parameter is `&String`. The `&` means "a reference to a `String`" — `print_str` does **not** own the string; it just borrows it.
- `let s1 = String::from("hello");` — `s1` is the owner of a `String` containing `"hello"`.
- `print_str(&s1);` — passing `&s1` creates a reference to `s1` and passes that reference to `print_str`. Ownership does **not** move — `s1` is still the owner.
- Inside `print_str`, `s` is a reference. The function can read the string but does not own it. When `print_str` returns, nothing is dropped — the reference just goes away, and the original `String` is still owned by `s1`.
- `println!("s1 is still valid: {}", s1);` — works, because `s1` was never moved. The string was only borrowed.

When you run this, the output is:

```text
I borrowed: hello
s1 is still valid: hello
```

References are the everyday way to pass data around in Rust. Most function parameters are references, not owned values.

### Immutable References: `&T`

A plain reference `&T` is **immutable** — you can read the data, but you cannot modify it through the reference. Even if the original variable is `mut`, an immutable reference forbids mutation:

```rust
fn main() {
    let mut s = String::from("hello");
    let r: &String = &s;       // r is an immutable reference
    // r.push_str("!");       // ERROR: cannot mutate through an immutable reference
    println!("{}", r);
}
```

- `let mut s = String::from("hello");` — declares a mutable `String`.
- `let r: &String = &s;` — borrows `s` immutably. `r` is read-only.
- `r.push_str("!");` — would not compile, because `r` is an immutable reference. Even though `s` is `mut`, you cannot mutate through an immutable reference.
- `println!("{}", r);` — reads the value through the reference. Works fine.

### Mutable References: `&mut T`

If you want a function to modify the data it borrows, use a **mutable reference** `&mut T`:

```rust
fn push_exclamation(s: &mut String) {
    s.push_str("!");
}

fn main() {
    let mut s = String::from("hello");
    push_exclamation(&mut s);
    println!("{}", s);
}
```

Let's break this down.

- `fn push_exclamation(s: &mut String) { ... }` — declares a function that takes a mutable reference to a `String`. The `&mut` keyword means the function can modify the borrowed data.
- `let mut s = String::from("hello");` — the caller must declare `s` as `mut`, otherwise you cannot create a `&mut` reference to it.
- `push_exclamation(&mut s);` — passes a mutable reference to `s` into the function. Inside the function, `s.push_str("!")` appends `"!"` to the string — modifying the original data in place. No ownership moves.
- `println!("{}", s);` — prints `hello!`, the modified string.

When you run this, the output is:

```text
hello!
```

The original `s` is mutated, even though it was not moved into the function. This is how Rust lets functions modify caller data without giving up the safety of ownership tracking.

---

## 7. The Borrow Checker Rules

The **borrow checker** is the part of the Rust compiler that enforces the rules of references. It is famous — both for catching real bugs and for being frustrating to beginners. The rules are:

1. **At any given time, you can have EITHER one mutable reference OR any number of immutable references.** You cannot have both, and you cannot have two mutable references to the same data at the same time.
2. **References must always be valid.** You cannot have a reference to data that has been freed (no "dangling references").
3. **References that are no longer used become inactive.** A reference stops counting against rule 1 once you stop using it.

Here is rule 1 in action — trying to have two mutable references:

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;          // ERROR: cannot borrow `s` as mutable more than once

    println!("{} {}", r1, r2);
}
```

Save this and run `cargo build`. The compiler refuses:

```text
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> src/main.rs:5:18
  |
4 |     let r1 = &mut s;
  |              ------ first mutable borrow occurs here
5 |     let r2 = &mut s;
  |                   ^ second mutable borrow occurs here
6 |     println!("{} {}", r1, r2);
  |                            -- first borrow later used here
```

The compiler is telling you: while `r1` is alive (still being used), you cannot create a second mutable reference `r2` to the same data. This prevents data races: if two parts of your code could mutate the same data at the same time, you would get inconsistent results.

Here is the other half of rule 1 — trying to have a mutable reference while an immutable one is alive:

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;              // immutable borrow
    let r2 = &s;              // another immutable borrow — fine
    let r3 = &mut s;          // ERROR: cannot borrow as mutable while immutably borrowed

    println!("{} {} {}", r1, r2, r3);
}
```

The compiler refuses, because `r1` and `r2` (immutable borrows) are still in use when `r3` (a mutable borrow) is created. The fix is to make sure the immutable borrows are done being used before the mutable one starts. In Rust, a reference "ends" at its last use, not at the end of the scope — this is called "non-lexical lifetimes" and the compiler handles it automatically:

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;
    let r2 = &s;
    println!("{} {}", r1, r2);   // last use of r1 and r2 — they are now "dead"

    let r3 = &mut s;             // this is fine — r1 and r2 are no longer in use
    println!("{}", r3);
}
```

- `let r1 = &s;` and `let r2 = &s;` — two immutable borrows, both used on the next line.
- `println!("{} {}", r1, r2);` — the last use of `r1` and `r2`. After this line, they are considered "dead" by the compiler.
- `let r3 = &mut s;` — now safe to create a mutable borrow, because no immutable references are still in use.
- `println!("{}", r3);` — uses `r3`.

This compiles and runs fine. The borrow checker is smart about when references stop being used.

### Why these rules?

The borrow checker rules exist to prevent **data races** and **iterator invalidation**, two of the most common and dangerous concurrency and memory bugs. If two threads could mutate the same data at the same time, you would get unpredictable results. If you could mutate a list while iterating over it, you could corrupt the list and crash. By forbidding multiple mutable references (or mixing mutable and immutable), Rust eliminates these bugs at compile time — your concurrent code cannot have data races, by construction. This is the "fearless concurrency" promise from Lesson 01.

---

## 8. Slices

A **slice** is a reference to a contiguous sequence of elements in a collection — like a view into an array or a string, without owning it. Slices are written `&[T]` (a slice of some type `T`) or `&str` (a string slice). They let you pass around "a chunk of data" without specifying exactly which collection it comes from.

### String Slices: `&str`

A `&str` is a slice of a string — a reference to some UTF-8 text in memory, with a length, but without owning the underlying bytes:

```rust
fn main() {
    let s = String::from("hello world");

    let hello: &str = &s[0..5];      // a slice of the first 5 bytes
    let world: &str = &s[6..11];     // a slice of bytes 6 through 10

    println!("{} {}", hello, world);
}
```

Let's break this down.

- `let s = String::from("hello world");` — `s` is a `String` (owned, on the heap) containing `"hello world"`.
- `&s[0..5]` — a slice of `s` covering bytes 0 through 4 (the range `0..5` is exclusive of the end). This is a `&str` — it points at the bytes inside `s` and remembers that the slice is 5 bytes long.
- `&s[6..11]` — a slice covering bytes 6 through 10 (`"world"`).
- `hello` and `world` are `&str` values. They do not own the text — they just borrow a piece of `s`.

When you run this, the output is:

```text
hello world
```

A `&str` can also refer to a string literal:

```rust
fn main() {
    let literal: &str = "I am a string literal";  // &str, stored in the program binary
    println!("{}", literal);
}
```

String literals like `"I am a string literal"` are stored in the read-only data section of the compiled binary, and the type of a string literal is `&str`. So `&str` is the universal "borrowed string" type — it can point at a `String`, a string literal, or a slice of either.

### Array Slices: `&[T]`

Similarly, `&[T]` is a slice of an array or vector of type `T`:

```rust
fn sum_slice(nums: &[i32]) -> i32 {
    let mut total = 0;
    for n in nums {
        total += *n;
    }
    total
}

fn main() {
    let arr = [1, 2, 3, 4, 5];
    let vec = vec![10, 20, 30];

    println!("sum of arr = {}", sum_slice(&arr));
    println!("sum of vec = {}", sum_slice(&vec));
    println!("sum of slice = {}", sum_slice(&arr[1..4]));
}
```

Let's break this down.

- `fn sum_slice(nums: &[i32]) -> i32 { ... }` — declares a function that takes a slice of `i32` (`&[i32]`) and returns an `i32`. Because slices are references, this function does not take ownership of any data — it just borrows whatever is passed in.
- `let arr = [1, 2, 3, 4, 5];` — declares an array on the stack.
- `let vec = vec![10, 20, 30];` — declares a `Vec<i32>` (a heap-allocated, growable vector).
- `sum_slice(&arr)` — passes a slice of the array. `&arr` is automatically coerced to `&[i32]` because an array can be borrowed as a slice.
- `sum_slice(&vec)` — passes a slice of the vector. `&vec` is similarly coerced to `&[i32]`.
- `sum_slice(&arr[1..4])` — passes a slice of just the middle elements `[2, 3, 4]`. The result is `9`.

When you run this, the output is:

```text
sum of arr = 15
sum of vec = 60
sum of slice = 9
```

Slices are how you write functions that work on **any** collection of the right element type — arrays, vectors, or sub-slices of either. They are one of the most useful patterns in Rust.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 10–15 minutes each — this lesson deserves the extra time.

### Exercise 1: Predict What Will Happen

For each of the following snippets, predict whether it will compile and what it will print. Then type it into a `cargo new` project and run `cargo build` to check. **Snippet A:** `let s = String::from("hi"); let t = s; println!("{}", s);` **Snippet B:** `let s = String::from("hi"); let t = s.clone(); println!("{}", s);` **Snippet C:** `let x = 5; let y = x; println!("{}", x);` Write a one-sentence note explaining why each snippet compiles or fails.

### Exercise 2: Convert a Function to Use References

Write a function `fn word_count(s: &String) -> usize` that takes a reference to a `String` and returns the number of words (split by spaces). Call it from `main` with a `String` you create, then continue using that `String` after the call (for example, print it again). Confirm the program compiles and runs — proving that the function borrowed rather than moved.

### Exercise 3: Slices

Write a function `fn first_word(s: &str) -> &str` that returns a slice containing the first word of a string (up to but not including the first space, or the whole string if no space). Hint: use `s.find(' ')` to locate the space; if found, return `&s[..idx]`, else return `s`. Call it from `main` with a `String` (passing `&s` to convert from `&String` to `&str` automatically — this is called "deref coercion") and a string literal. Print the first word in each case.

---

## Common Mistakes

### Mistake 1: Using a variable after it has been moved

```rust
// WRONG — s1 was moved into s2
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;
    println!("{}", s1);
}
```

```rust
// RIGHT — use s2, or borrow with &, or clone
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();
    println!("{} {}", s1, s2);
}
```

The single most common borrow checker error. After `let s2 = s1;`, the variable `s1` is no longer valid — ownership moved. Using `s1` after this point produces `error[E0382]: borrow of moved value: \`s1\``. The fix depends on your intent: if you wanted the new owner, just use `s2`. If you need both copies, call `.clone()` on the value before assigning. If you wanted to let the function *read* the data without taking it, pass a reference (`&s1`).

### Mistake 2: Having both mutable and immutable borrows alive at the same time

```rust
// WRONG — r1 and r2 (immutable) are still in use when r3 (mutable) is created
fn main() {
    let mut s = String::from("hello");
    let r1 = &s;
    let r2 = &s;
    let r3 = &mut s;
    println!("{} {} {}", r1, r2, r3);
}
```

```rust
// RIGHT — finish using immutable refs before creating the mutable ref
fn main() {
    let mut s = String::from("hello");
    let r1 = &s;
    let r2 = &s;
    println!("{} {}", r1, r2);   // last use of r1 and r2

    let r3 = &mut s;             // now safe
    println!("{}", r3);
}
```

The borrow checker refuses to let you have a mutable reference while immutable references to the same data are still in use. The error is `cannot borrow \`s\` as mutable because it is also borrowed as immutable`. The fix is to make sure all uses of the immutable references come **before** you create the mutable one. The compiler tracks "last use" of references automatically (a feature called non-lexical lifetimes), so you do not need to manually drop them.

### Mistake 3: Returning a reference to a local variable (dangling reference)

```rust
// WRONG — s is dropped at the end of the function, the reference dangles
fn bad() -> &String {
    let s = String::from("hi");
    &s
}
```

```rust
// RIGHT — return an owned String, not a reference
fn good() -> String {
    let s = String::from("hi");
    s
}
```

If a function creates a local variable and tries to return a reference to it, the local is dropped at the end of the function — the reference would point to freed memory. Rust's borrow checker catches this at compile time: `missing lifetime specifier` or `borrowed value does not live long enough`. The fix is to return an **owned** value (move it out of the function) instead of a reference. The owner is then the caller, and the value lives as long as the caller keeps it.

### Mistake 4: Using `&` where `&mut` is needed

```rust
// WRONG — an immutable reference cannot mutate the data
fn add_bang(s: &String) {
    s.push_str("!");
}
```

```rust
// RIGHT — use &mut to allow mutation through the reference
fn add_bang(s: &mut String) {
    s.push_str("!");
}

fn main() {
    let mut s = String::from("hello");
    add_bang(&mut s);
    println!("{}", s);
}
```

If a function tries to modify the data it borrows, it must take `&mut T`, not `&T`. The error from using `&` is `cannot borrow \`*s\` as mutable, as it is behind a \`&\` reference`. The fix is twofold: change the parameter to `&mut String`, and at the call site, pass `&mut s` (and make sure the original is declared `let mut`). Forgetting either piece will give you a compiler error.

---

## Summary

- **Ownership** is Rust's system for memory safety without a garbage collector. Every value has exactly one owner; when that owner goes out of scope, the value is dropped.
- The three rules: (1) each value has an owner; (2) only one owner at a time; (3) when the owner goes out of scope, the value is dropped.
- For types that own heap memory (like `String`), assignment **moves** ownership — `let s2 = s1;` invalidates `s1`. The same happens when passing to a function or returning.
- **`.clone()`** makes a deep copy — use it when you need to keep the original valid after a would-be move.
- Primitive types (`i32`, `f64`, `bool`, `char`) implement the `Copy` trait and are copied on assignment, not moved. Use them freely without cloning.
- **References** (`&T`) let you lend data without taking ownership — this is called **borrowing**. The caller keeps ownership; the reference just gives access.
- **Mutable references** (`&mut T`) let you modify the borrowed data; the original must be declared `mut`. Use `&mut` when a function needs to write to the borrowed value.
- The borrow checker rule: at any time, EITHER one `&mut T` OR any number of `&T` — never both. References also "die" at their last use, after which conflicting borrows become legal (non-lexical lifetimes).
- **Slices** (`&str`, `&[T]`) are references to a contiguous chunk of data — they let you write functions that work on any collection (arrays, vectors, substrings) without taking ownership.
- Never return a reference to a local variable — the borrow checker will catch you with "borrowed value does not live long enough." Return an owned value instead.

You have now survived the single hardest concept in Rust. The rest of the module is downhill from here — the ownership model is what makes Rust Rust, and once it clicks, you will see the whole language differently. In Lesson 07 you will find a printable cheatsheet that summarizes everything from this module in one place, plus a guide to the most popular crates you will use in real Rust projects.

---

**Next:** [Lesson 07: Rust Cheatsheet →](./07-rust-cheatsheet.md)
