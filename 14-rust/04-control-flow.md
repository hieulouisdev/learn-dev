# Lesson 04: Control Flow

> Module: Rust · Lesson 4 of 7
> Estimated time: 30–45 minutes

Welcome to Lesson 04. So far you can declare variables and print them. In this lesson you will learn how to make your programs **make decisions** and **repeat work** — the two capabilities that turn a list of statements into a real program. Rust has the usual suspects (`if`, `while`, `for`) but also a few surprises: `if` is an **expression** that returns a value, `loop` is an infinite loop you exit with `break` (and `break` can return a value too), and `match` is Rust's answer to `switch` on steroids.

If you know `if`, `while`, and `for` from Python, the basics will feel familiar. The Rust-specific features — `if` as an expression, `match`'s exhaustiveness checking, and `if let` — are worth slowing down for. They are the foundation for the ownership lesson that comes next.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use `if` as an expression that returns a value, not just as a statement.
2. Use `loop`, `while`, and `for` (including range syntax `0..5` and `0..=5`).
3. Use `match` for exhaustive pattern matching, including the `_` wildcard and `|` "or" patterns.
4. Use `if let` as a concise form for the common "match one variant" case.

---

## 1. `if` Expressions

The simplest form of `if` looks familiar:

```rust
fn main() {
    let number = 7;

    if number > 5 {
        println!("number is greater than 5");
    } else if number == 5 {
        println!("number is exactly 5");
    } else {
        println!("number is less than 5");
    }
}
```

Let's break this down.

- `let number = 7;` — declares an `i32` variable bound to `7`.
- `if number > 5 { ... }` — the `if` keyword, followed by a condition. **No parentheses** around the condition — Rust does not require them, and the idiomatic style is to omit them. (Adding them is allowed but clutters the code.)
- `else if number == 5 { ... }` — an alternative branch tested only if the first condition was false. You can chain as many `else if`s as you want.
- `else { ... }` — the fallback branch, taken if no previous condition matched. The `else` branch is optional.
- Each block is wrapped in curly braces `{ }`. Unlike Python (which uses indentation), Rust uses braces.

When you run this, the output is:

```text
number is greater than 5
```

### `if` returns a value

Here is the Rust surprise: **`if` is an expression**, not a statement. It evaluates to a value — the value of whichever branch runs. This means you can write:

```rust
fn main() {
    let condition = true;
    let n = if condition { 5 } else { 10 };
    println!("n is {}", n);
}
```

- `let condition = true;` — declares a boolean.
- `let n = if condition { 5 } else { 10 };` — the `if` expression evaluates to `5` (because `condition` is `true`), and that value is bound to `n`. So `n` becomes `5`.
- `println!("n is {}", n);` — prints `n is 5`.

This is impossible in Python — `if` is a statement there. In Rust, `if`, `match`, blocks (`{ }`), and loops can all be used as expressions. This is one of the most distinctive features of the language.

**Important constraint**: both branches of an `if` expression must have the **same type**. If you write `let n = if cond { 5 } else { "hi" };`, the compiler refuses with `if and else have incompatible types`. This is so that the type of `n` is unambiguous — the compiler knows it at compile time without knowing which branch will run.

### Conditions must be `bool`

Unlike C and JavaScript (where `0`, `""`, `null`, `undefined` are all "falsy"), Rust **does not** implicitly convert non-booleans to booleans. The condition of an `if` must be exactly `bool`. Writing `if number { ... }` where `number` is an `i32` is a compile error, not a test for non-zero. You must write `if number != 0`.

---

## 2. `loop`

The `loop` keyword creates an **infinite loop**. It runs forever, or until you `break` out of it:

```rust
fn main() {
    let mut count = 0;

    loop {
        count += 1;
        if count >= 5 {
            break;
        }
    }

    println!("Final count: {}", count);
}
```

- `let mut count = 0;` — declares a mutable counter starting at 0.
- `loop { ... }` — begins an infinite loop.
- `count += 1;` — increments the counter. (Rust also supports `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`.)
- `if count >= 5 { break; }` — once `count` reaches 5, the `break` statement exits the loop.
- `println!("Final count: {}", count);` — prints `Final count: 5`.

### `break` returns a value

Because `loop` is an expression, `break` can carry a value out of the loop:

```rust
fn main() {
    let mut count = 0;
    let result = loop {
        count += 1;
        if count == 10 {
            break count * 2;
        }
    };
    println!("result = {}", result);
}
```

- `let result = loop { ... };` — the loop expression evaluates to whatever value the `break` carries.
- `break count * 2;` — when `count` reaches 10, the loop exits and `result` becomes `10 * 2 = 20`.

When you run this, the output is:

```text
result = 20
```

This is useful when you want to compute a value inside a loop and return it — for example, searching for something and breaking with the found value.

### Loop labels

If you have nested loops, you can label them and `break` the outer one from inside the inner one:

```rust
fn main() {
    let mut found = false;

    'outer: loop {
        let mut i = 0;
        loop {
            if i == 5 {
                break 'outer;   // exits the OUTER loop
            }
            i += 1;
        }
        // This line is never reached, because we break 'outer above
        found = true;
    }

    println!("Exited outer loop");
}
```

- `'outer: loop { ... }` — labels the outer loop with the lifetime-name `'outer`. (The single quote in front of the label is required syntax.)
- `break 'outer;` — exits the labeled outer loop, not just the inner loop.

Loop labels are rare in everyday Rust, but they are the cleanest way to break out of multiple nested loops.

---

## 3. `while`

The `while` loop runs as long as a condition is true:

```rust
fn main() {
    let mut n = 3;

    while n > 0 {
        println!("{}!", n);
        n -= 1;
    }

    println!("LIFTOFF!");
}
```

- `let mut n = 3;` — declares a mutable counter.
- `while n > 0 { ... }` — runs the body as long as `n > 0`.
- `println!("{}!", n);` — prints `3!`, then `2!`, then `1!` (on separate lines).
- `n -= 1;` — decrements `n`.
- `println!("LIFTOFF!");` — printed after the loop exits.

Output:

```text
3!
2!
1!
LIFTOFF!
```

`while` is just sugar for `loop` with a condition check at the top and a `break` when the condition fails. Use `while` when you do not know how many iterations you need ahead of time. Use `for` (next section) when you do.

---

## 4. `for`

The `for` loop iterates over anything that can be iterated — including ranges, arrays, and vectors:

```rust
fn main() {
    for i in 0..5 {
        println!("i = {}", i);
    }
}
```

- `for i in 0..5 { ... }` — `i` takes each value in the range `0..5` in turn.
- `0..5` — a **range** that produces `0, 1, 2, 3, 4` (the end is **exclusive**).

Output:

```text
i = 0
i = 1
i = 2
i = 3
i = 4
```

If you want to include the end, use `..=`:

```rust
fn main() {
    for i in 0..=5 {
        println!("i = {}", i);
    }
}
```

- `0..=5` — an inclusive range that produces `0, 1, 2, 3, 4, 5`.

Output:

```text
i = 0
i = 1
i = 2
i = 3
i = 4
i = 5
```

### Iterating over arrays

```rust
fn main() {
    let fruits = ["apple", "banana", "cherry"];

    for fruit in fruits {
        println!("I like {}", fruit);
    }
}
```

- `let fruits = ["apple", "banana", "cherry"];` — declares an array of three `&str` values.
- `for fruit in fruits { ... }` — iterates over the array by value. (In this case, the values are `&str`, which are references, so the original array is not consumed — but for owned types you might need `for fruit in &fruits` to borrow instead. You will see this in Lesson 06.)

### Iterating with index

If you need both the index and the value, use `.iter().enumerate()`:

```rust
fn main() {
    let fruits = ["apple", "banana", "cherry"];

    for (index, fruit) in fruits.iter().enumerate() {
        println!("{}: {}", index, fruit);
    }
}
```

- `fruits.iter()` — creates an iterator over the array.
- `.enumerate()` — turns it into an iterator of `(index, value)` pairs.
- `for (index, fruit) in ...` — destructures each pair into `index` and `fruit`.

Output:

```text
0: apple
1: banana
2: cherry
```

For loops are the most common kind of loop in idiomatic Rust. They are safer than `while` (no risk of an infinite loop if you forget to decrement) and more concise than manual indexing.

---

## 5. `match`

`match` is Rust's pattern-matching construct — think of it as a `switch` statement on steroids. It compares a value against a series of **patterns** and runs the arm that matches:

```rust
fn main() {
    let number = 3;

    match number {
        1 => println!("one"),
        2 => println!("two"),
        3 => println!("three"),
        4 | 5 => println!("four or five"),
        _ => println!("something else"),
    }
}
```

Let's break this down.

- `match number { ... }` — the `match` keyword, followed by the value to match on (`number`), then a block of arms.
- `1 => println!("one"),` — an arm with pattern `1` and body `println!("one")`. If `number` equals `1`, this arm runs.
- `4 | 5 => println!("four or five"),` — the `|` operator combines patterns: this arm matches either `4` or `5`.
- `_ => println!("something else"),` — the `_` is the **wildcard pattern**, which matches any value. It acts as the "default" or "else" case.
- Each arm ends with a comma (the last comma is optional).

When you run this, the output is:

```text
three
```

### Exhaustiveness

Rust `match` is **exhaustive** — you must handle every possible value. If you forget a case (and do not have a `_` wildcard), the compiler refuses to build your program. Try this:

```rust
fn main() {
    let number = 7;
    match number {
        1 => println!("one"),
        2 => println!("two"),
        // no _ wildcard, no arm for 7
    }
}
```

The compiler will refuse:

```text
error[E0004]: non-exhaustive patterns: `3i32` and `i32::MIN..=0i32` not covered
```

This is a safety feature: Rust forces you to think about every case, so you never accidentally forget to handle a value. The `_` wildcard is the escape hatch when you do not care about the other cases — put it last and it catches everything not matched above.

### `match` returns a value

Like `if`, `match` is an expression. It evaluates to the value of whichever arm runs:

```rust
fn main() {
    let x = 4;
    let description = match x {
        1 => "one",
        2 | 3 => "two or three",
        _ => "many",
    };
    println!("x is {}", description);
}
```

- `let description = match x { ... };` — `match` evaluates to the value of whichever arm runs, and that value is bound to `description`.
- Each arm's body is just a string literal, which evaluates to a `&str`.
- All arms must return the same type (`&str` here).

When you run this, the output is:

```text
x is many
```

Pattern matching in Rust is much more powerful than this — you can match on enums, structs, ranges (`1..=10 =>`), bind parts of the matched value, add guards with `if`, and more. You will see more of this when you learn about enums in a later module. For now, remember: `match` is exhaustive, returns a value, and is one of Rust's most loved features.

---

## 6. `if let`

Sometimes you only care about one specific case and want to ignore everything else. Writing a full `match` with `_ =>` for the catch-all is verbose:

```rust
match some_value {
    Some(v) => do_something_with(v),
    _ => (),
}
```

For this common pattern, Rust provides `if let` — a concise form that does the same thing:

```rust
fn main() {
    let maybe_name: Option<&str> = Some("Ada");

    if let Some(name) = maybe_name {
        println!("Hello, {}!", name);
    } else {
        println!("No name provided");
    }
}
```

Let's break this down.

- `let maybe_name: Option<&str> = Some("Ada");` — declares an `Option<&str>`. `Option<T>` is Rust's way of saying "this might be `Some(T)` or `None`" — it replaces `null` in other languages. You will meet it formally later in the module, but for now just understand that `Some("Ada")` is one of its two possible variants.
- `if let Some(name) = maybe_name { ... }` — if `maybe_name` is `Some(...)`, this matches and binds the inner value to `name`. Otherwise the `else` branch runs.
- `println!("Hello, {}!", name);` — runs only if the pattern matched.
- `else { ... }` — the optional fallback when the pattern does not match.

When you run this, the output is:

```text
Hello, Ada!
```

`if let` is shorthand for `match` with one arm. Use it whenever you only care about one variant of an enum (typically `Some` vs `None`, or `Ok` vs `Err`). If you care about multiple variants, use a full `match` instead.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: FizzBuzz

Write a Rust program that prints the numbers from 1 to 20, but for multiples of 3 print `Fizz` instead of the number, for multiples of 5 print `Buzz`, and for multiples of both 3 and 5 print `FizzBuzz`. Use a `for` loop with `1..=20`, an `if`/`else if`/`else` chain inside it, and `print!` (without the `ln`) to print without a newline, followed by `println!("")` at the end of each iteration. Run it with `cargo run` and verify the output starts with `1, 2, Fizz, 4, Buzz, Fizz, 7, ...`.

### Exercise 2: Loop with break value

Write a `loop` that starts with `let mut n = 1` and doubles `n` each iteration (`n *= 2`). Break out of the loop when `n` would exceed 1000, and use `break n;` to return the final value. Bind the loop expression to a `let result = loop { ... };` variable. Print the result. Confirm you get `1024` (the first power of 2 above 1000).

### Exercise 3: Match on a Number

Declare `let day = 3;` and use a `match` expression to bind a string: `1 => "Monday"`, `2 => "Tuesday"`, `3 => "Wednesday"`, `4 => "Thursday"`, `5 => "Friday"`, `_ => "Weekend"`. Print the result. Then change `day` to `7` and confirm you get `Weekend`. Try removing the `_` arm and confirm the compiler complains the match is non-exhaustive.

---

## Common Mistakes

### Mistake 1: Forgetting the `_` wildcard and getting a non-exhaustive match error

```rust
// WRONG — match is not exhaustive (what if x is 3, 4, 5, etc?)
fn main() {
    let x = 5;
    let s = match x {
        1 => "one",
        2 => "two",
    };
    println!("{}", s);
}
```

```rust
// RIGHT — add a _ wildcard to catch all other values
fn main() {
    let x = 5;
    let s = match x {
        1 => "one",
        2 => "two",
        _ => "many",
    };
    println!("{}", s);
}
```

Rust `match` is **exhaustive** — you must cover every possible value. If you forget a case, the compiler refuses with `non-exhaustive patterns`. The fix is always either to add the missing cases explicitly, or to add a `_ => ...` arm at the end to catch everything else. The `_` is the universal "default" case.

### Mistake 2: Mixing types in `if` branches

```rust
// WRONG — if branches must return the same type
fn main() {
    let cond = true;
    let n = if cond { 5 } else { "ten" };
    println!("{}", n);
}
```

```rust
// RIGHT — both branches return the same type
fn main() {
    let cond = true;
    let n = if cond { 5 } else { 10 };
    println!("{}", n);
}
```

Because `if` is an expression that returns a value, both branches must return the **same type**. If you try to return `5` from one branch and `"ten"` from the other, the compiler will say `if and else have incompatible types`. This is so that the type of `n` is unambiguous at compile time. If you genuinely need different types, use an enum (a topic for a later module) or rethink your design.

### Mistake 3: Off-by-one with `..` vs `..=`

```rust
// WRONG — meant to include 5, used exclusive range
fn main() {
    for i in 0..5 {
        println!("{}", i);   // prints 0, 1, 2, 3, 4 — missing 5
    }
}
```

```rust
// RIGHT — use ..= for inclusive range
fn main() {
    for i in 0..=5 {
        println!("{}", i);   // prints 0, 1, 2, 3, 4, 5
    }
}
```

The two range operators look almost identical but behave differently. `a..b` is exclusive of `b`; `a..=b` is inclusive of `b`. Off-by-one errors from choosing the wrong one are extremely common. The mnemonic: `..=` has an `=` sign next to the second number, meaning "up to and including". Plain `..` has no `=`, meaning "up to but not including".

### Mistake 4: Forgetting that `if` returns `()` when no value is given

```rust
// WRONG — the if branches contain statements (println!), so they evaluate to ()
fn main() {
    let cond = true;
    let n = if cond {
        println!("yes");
    } else {
        println!("no");
    };
    println!("n is {:?}", n);
}
```

```rust
// RIGHT — branches contain values, not statements
fn main() {
    let cond = true;
    let n = if cond { 5 } else { 10 };
    println!("n is {}", n);
}
```

If you write `if cond { println!("yes"); }` and try to bind it to a variable, you might expect the value to be the string. But `println!` is a statement that returns `()` (the unit type — "nothing"), so both branches return `()` and `n` is bound to `()`. The compiler will accept this (because `()` is a valid type), but `n` will be useless. The fix is to put an actual value as the last expression in each branch (with no semicolon), as in the corrected version above.

---

## Summary

- `if` in Rust is an **expression** — it returns a value, not just runs a branch.
- Conditions must be exactly `bool`; Rust does not implicitly convert other types to booleans.
- `loop` runs forever until `break`; `break` can carry a value out: `let n = loop { break 42; };`.
- `while cond { ... }` runs as long as `cond` is true.
- `for i in 0..5` is exclusive (0,1,2,3,4); `for i in 0..=5` is inclusive (0,1,2,3,4,5).
- `.iter().enumerate()` gives you `(index, value)` pairs when iterating.
- `match` is exhaustive — you must cover every case, or add a `_ =>` wildcard.
- `match` arms can use `|` to combine patterns (`1 | 2 =>`) and returns the value of whichever arm runs.
- `if let PATTERN = VALUE { ... }` is shorthand for `match` with one arm — used when you only care about one case.
- All branches of an `if` expression (or arms of a `match`) must return the same type.

You can now write Rust programs that make decisions and repeat work. In Lesson 05 you will learn how to factor repeated code into reusable **functions**, and meet a deep Rust concept — the difference between **statements** and **expressions** — that explains everything you have seen so far.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
