# Lesson 04: Control Flow

> Module: C++ · Lesson 4 of 7
> Estimated time: 30–45 minutes

In Lesson 03 you learned C++'s primitive types, declaration forms, `auto`, constants, strings, arrays, and references. Your variables now have somewhere to live — but a program with only straight-line code is not much of a program. Real programs make decisions, repeat work, and skip code based on conditions. In this lesson we cover every form of control flow C++ offers: `if`/`else if`/`else`, `switch`, the classic C-style `for` loop, the modern range-based `for`, `while`, `do-while`, `break`, `continue`, the ternary operator, and yes, even `goto`.

If you have completed the Java, Go, or Rust module, this lesson will feel like a refresher — C++ uses the exact same C-style syntax as those languages. The main thing to watch out for is a subtle truthiness rule: in C++, any non-zero number is "true," which is different from Java's strict boolean requirement. Take it slowly and you will be done in thirty minutes.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write `if`/`else if`/`else` chains and explain why a non-zero `int` is treated as `true` in C++.
2. Use `switch` with `case`/`break`/`default`, and explain why forgetting `break` causes fall-through.
3. Write both the classic C-style `for` loop and the modern range-based `for` (C++11+), and know when to use `const auto&` in the range-for.
4. Use `while`, `do-while`, `break`, `continue`, and the ternary operator, and explain why `goto` is discouraged.

---

## 1. `if` / `else if` / `else`

The `if` statement is the simplest form of control flow: it runs a block of code only if a condition is true. C++ inherits C's syntax exactly, so the structure should look familiar from Java, Go, and TypeScript:

```cpp
#include <iostream>

int main() {
    int score = 85;

    if (score >= 90) {
        std::cout << "A\n";
    } else if (score >= 80) {
        std::cout << "B\n";
    } else if (score >= 70) {
        std::cout << "C\n";
    } else {
        std::cout << "F\n";
    }
    return 0;
}
```

- `if` is followed by a condition in parentheses `(score >= 90)`.
- The condition can be any expression that converts to `bool`.
- The body is a block `{ ... }` or a single statement (without braces, only the next statement is conditional — but always use braces for readability).
- `else if` chains additional conditions; `else` runs if none of the previous conditions were true.

The condition does not have to be a strict boolean — C++ accepts any type that is **convertible to bool**. The conversion rule is borrowed from C: **zero is false, anything else is true**. So:

```cpp
int x = 5;
if (x) {            // true — x is non-zero
    std::cout << "x is non-zero\n";
}

int* p = nullptr;
if (p) {            // false — p is null
    std::cout << "p is not null\n";
} else {
    std::cout << "p is null\n";
}

std::string s = "hello";
if (!s.empty()) {   // .empty() returns bool — note the ! (not) operator
    std::cout << "s has content\n";
}
```

This implicit truthiness is **different from Java**: Java requires the condition to be strictly `boolean`, and `if (x)` where `x` is an `int` is a compile error in Java. In C++ it is legal and idiomatic — but be careful, because `if (x = 5)` (assignment, see Common Mistakes) compiles and is always true. Modern style guides recommend writing the explicit form: `if (x != 0)` instead of `if (x)`, especially when reading code that may be confusing.

C++17 added one small ergonomic improvement: **`if` with initializer**, which lets you declare a variable inside the `if` statement itself:

```cpp
if (auto it = map.find(key); it != map.end()) {
    // 'it' is available here, scoped to the if-block
    use(it->second);
}
// 'it' is not in scope here — clean
```

This is the equivalent of Go's `if x, ok := m[k]; ok { ... }`. It keeps the temporary variable scoped to the `if`, preventing it from leaking into the surrounding code. You will see this in real codebases; it is optional but useful for keeping scope tight.

---

## 2. `switch`

A `switch` statement jumps to one of several `case` labels based on the value of an integer expression. It is a more readable alternative to a long `if`/`else if` chain when comparing a single variable against many constants.

```cpp
#include <iostream>

int main() {
    int day = 3;

    switch (day) {
        case 1:
            std::cout << "Monday\n";
            break;
        case 2:
            std::cout << "Tuesday\n";
            break;
        case 3:
            std::cout << "Wednesday\n";
            break;
        case 4:
            std::cout << "Thursday\n";
            break;
        case 5:
            std::cout << "Friday\n";
            break;
        default:
            std::cout << "Weekend\n";
            break;
    }
    return 0;
}
```

- `switch (day)` — `day` must be an integer or enum type. You cannot `switch` on a `std::string` or a `float` (use `if`/`else` instead).
- `case 1:` — a label. Cases must be **constant integer expressions** — they cannot be variables.
- `break;` — exits the switch. **Without `break`, execution falls through to the next case.** This is intentional (called "fall-through") and is sometimes useful, but it is also a frequent source of bugs.
- `default:` — runs if no case matches. Optional but recommended.

**Fall-through is the classic `switch` bug.** If you forget `break;` in a case, execution continues into the next case's body. For example:

```cpp
switch (day) {
    case 1:
        std::cout << "Monday\n";
        // forgot break! falls through to case 2
    case 2:
        std::cout << "Tuesday\n";
        break;
    // ...
}
// If day == 1, prints BOTH "Monday" and "Tuesday"
```

Sometimes fall-through is intentional — for example, when several cases should run the same code:

```cpp
switch (day) {
    case 1: case 2: case 3: case 4: case 5:
        std::cout << "Weekday\n";
        break;
    case 6: case 7:
        std::cout << "Weekend\n";
        break;
}
```

Here several labels are stacked on the same block of code — this is intentional and idiomatic. C++17 added the `[[fallthrough]];` attribute to mark intentional fall-through and silence compiler warnings:

```cpp
switch (x) {
    case 1:
        do_first_thing();
        [[fallthrough]];   // tells the compiler "I meant to fall through"
    case 2:
        do_second_thing();
        break;
}
```

Without `[[fallthrough]];`, modern compilers with `-Wimplicit-fallthrough` warn you about every fall-through, which catches the common mistake of forgetting `break`. Turn on `-Wall -Wextra` and you will get these warnings automatically.

---

## 3. `for` Loop

C++ has **two kinds of `for` loop**: the classic C-style `for` (inherited from C, also in Java and JavaScript) and the modern range-based `for` (added in C++11, similar to Python's `for x in things` and Java's enhanced for-loop).

### Classic C-style `for`

```cpp
#include <iostream>

int main() {
    for (int i = 0; i < 5; i++) {
        std::cout << "i = " << i << '\n';
    }
    return 0;
}
```

The classic `for` has three parts separated by semicolons:

- **Init**: `int i = 0;` — runs once before the loop starts. Usually declares and initializes a counter.
- **Condition**: `i < 5` — checked before each iteration. If true, the body runs; if false, the loop ends.
- **Update**: `i++` — runs after each iteration. Usually increments the counter.

All three parts are optional — `for (;;) { ... }` is an infinite loop (equivalent to `while (true)`). You can declare multiple variables in the init: `for (int i = 0, j = 10; i < j; i++, j--) { ... }`.

### Range-based `for` (C++11+)

The range-based `for` iterates over every element of a container — `std::vector`, `std::array`, `std::string`, C-style arrays, or any type that has `begin()` and `end()` methods. It is the C++ equivalent of Python's `for x in things` and Java's `for (int x : nums)`.

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> nums = {10, 20, 30, 40, 50};

    // (1) Iterate by value — copies each element (fine for small types like int)
    for (int n : nums) {
        std::cout << n << ' ';
    }
    std::cout << '\n';

    // (2) Iterate by reference — modify elements in place
    for (int& n : nums) {
        n *= 2;       // doubles each element of nums
    }

    // (3) Iterate by const reference — no copy, no modification (preferred for big types)
    for (const int& n : nums) {
        std::cout << n << ' ';
    }
    std::cout << '\n';

    // (4) Use auto for brevity (most common in production code)
    for (const auto& n : nums) {
        std::cout << n << ' ';
    }
    std::cout << '\n';
    return 0;
}
```

The three forms — by value, by reference, by `const` reference — correspond to three different needs:

- **By value (`for (int n : nums)`)** copies each element. Use for small types like `int` or `char`, where the copy is cheap.
- **By reference (`for (int& n : nums)`)** gives you a mutable reference. Use when you want to modify the elements in place.
- **By const reference (`for (const int& n : nums)`)** gives you a read-only reference, no copy. Use for big types (`std::string`, `std::vector`, custom classes) where copying would be expensive.

The most common form in production code is `for (const auto& x : things)` — works for any container, never copies, never modifies. Get in the habit of writing it.

---

## 4. `while`

A `while` loop runs its body repeatedly as long as its condition is true. Use it when you do not know in advance how many iterations you need.

```cpp
#include <iostream>

int main() {
    int n = 1024;
    int steps = 0;
    while (n > 1) {
        if (n % 2 == 0) {
            n /= 2;
        } else {
            n = 3 * n + 1;
        }
        steps++;
    }
    std::cout << "Steps: " << steps << '\n';
    return 0;
}
```

- `while (n > 1)` checks the condition **before** each iteration.
- If the condition is false at the start, the body never runs (zero iterations).
- If the condition never becomes false, you have an infinite loop — be careful.

`while` is the natural choice when the loop condition is checked at the top and the number of iterations is unknown. Common use cases: reading from a stream until EOF (`while (std::cin >> x)`), polling a condition (`while (!is_ready())`), or processing a queue (`while (!queue.empty())`).

---

## 5. `do-while`

A `do-while` loop is like a `while` loop but checks the condition **after** the body runs. This guarantees the body executes at least once.

```cpp
#include <iostream>

int main() {
    int n = 1;
    do {
        std::cout << n << '\n';
        n *= 2;
    } while (n < 100);
    return 0;
}
```

- The body runs first, then `while (n < 100)` is checked.
- If true, the body runs again; if false, the loop ends.
- The body **always** runs at least once, even if the condition is false at the start.

`do-while` is less common than `while` because most loops check their condition at the top. Its main use case is when the body produces the value the condition depends on — for example, reading input until the user types "quit", or computing the next approximation in a numerical algorithm. C++ also uses `do-while` for macros occasionally, but that is a niche case.

---

## 6. `break`, `continue`, and `goto`

C++ inherits C's three "loop control" statements. The first two you will use every day; the third you should generally avoid.

**`break`** immediately exits the enclosing loop (or `switch`). Use it when you want to stop the loop early — for example, when you have found what you were looking for:

```cpp
for (int n : nums) {
    if (n == target) {
        std::cout << "Found!\n";
        break;          // stop searching
    }
}
```

**`continue`** skips the rest of the current iteration and goes to the next one. Use it to skip elements that do not need processing:

```cpp
for (int n : nums) {
    if (n < 0) {
        continue;       // skip negatives
    }
    std::cout << n << '\n';
}
```

`break` and `continue` only affect the **innermost** loop — if you have nested loops, `break` exits only the inner loop. To break out of multiple loops at once, you can use a flag variable, a labeled break (which C++ does not have — unlike Java), or a `goto` (see below).

**`goto`** jumps to a labeled statement elsewhere in the same function. It is the infamous statement Dijkstra warned us about in 1968, and modern C++ style guides tell you to avoid it. But C++ still has it, and there is one legitimate use case: breaking out of **nested loops**.

```cpp
#include <iostream>

int main() {
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 10; j++) {
            if (i * j > 50) {
                std::cout << "Found at i=" << i << ", j=" << j << '\n';
                goto done;     // breaks out of BOTH loops
            }
        }
    }
    done:
    std::cout << "Out of both loops\n";
    return 0;
}
```

Without `goto`, you would need a flag variable checked at the end of the outer loop. Some style guides (notably the Linux kernel) consider this `goto` use acceptable. Most application code does not need it. **Avoid `goto` unless you have a clear reason** — it makes code hard to follow and is a famous source of "spaghetti code" bugs.

---

## 7. Ternary Operator

The ternary operator `? :` is a one-line `if`/`else` that produces a value. It is the same syntax as in Java, JavaScript, and C#.

```cpp
#include <iostream>

int main() {
    int a = 5, b = 7;
    int max = (a > b) ? a : b;       // if a > b, max = a; else max = b
    std::cout << "Max: " << max << '\n';

    int x = 10;
    std::string label = (x % 2 == 0) ? "even" : "odd";
    std::cout << x << " is " << label << '\n';
    return 0;
}
```

- The syntax is `condition ? value_if_true : value_if_false`.
- The two values must have compatible types (or types with a common conversion).
- The result is a value, so you can use it in expressions: `int y = (x > 0) ? x : -x;` computes the absolute value.

The ternary is concise but can become unreadable if nested. Avoid `(a > b) ? ((c > d) ? a : c) : b` — use a regular `if`/`else` instead. The rule of thumb: one ternary per line, no nesting. Use it for simple cases like picking a label or computing a default.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: FizzBuzz

Write a program that prints the numbers from 1 to 30, but for multiples of 3 print `Fizz` instead of the number, for multiples of 5 print `Buzz`, and for multiples of both print `FizzBuzz`. Use a `for` loop, an `if`/`else if`/`else` chain, and the modulo operator `%`. Compile with `g++ -std=c++17 -Wall -Wextra -o fizz fizz.cpp` and confirm the first few lines look like `1, 2, Fizz, 4, Buzz, Fizz, 7, ...`.

### Exercise 2: Switch on a Char

Write a program that reads a single character from the user (use `char c; std::cin >> c;`), then uses a `switch` to print whether it is a vowel (`a, e, i, o, u` — handle both cases), a digit (`0-9`), or something else. Use stacked case labels for the vowels (`case 'a': case 'e': ...`). Remember the `break;` after each group.

### Exercise 3: Range-for Sum and Product

Write a program that uses a `std::vector<int>` initialized with `{1, 2, 3, 4, 5}`, then uses two separate range-based `for` loops — one to compute the sum, and one to compute the product. Print both results. Use `const auto&` in the range-for. Then modify the vector by doubling each element using a third range-for with `auto&` (mutable reference). Confirm the sum updates correctly.

---

## Common Mistakes

### Mistake 1: Forgetting `break` in `switch`

```cpp
// WRONG — missing break causes fall-through
switch (day) {
    case 1:
        std::cout << "Monday\n";
        // missing break — falls through!
    case 2:
        std::cout << "Tuesday\n";
        break;
}
// If day == 1, prints BOTH "Monday" and "Tuesday"
```

```cpp
// RIGHT — break after each case (unless fall-through is intentional)
switch (day) {
    case 1:
        std::cout << "Monday\n";
        break;
    case 2:
        std::cout << "Tuesday\n";
        break;
}
```

`switch` cases fall through by default — without `break`, execution continues into the next case's body. This was a deliberate design choice in C (to allow stacking cases), but it is the source of countless bugs. The fix is to add `break;` at the end of every case unless you specifically intend to fall through (and in that case, mark it with `[[fallthrough]];` so the compiler does not warn you). Compile with `-Wimplicit-fallthrough` (part of `-Wextra` on modern GCC) and the compiler will catch every missing break.

### Mistake 2: `if (x = 5)` instead of `if (x == 5)`

```cpp
// WRONG — single = is assignment, always returns 5 (truthy)
int x = 0;
if (x = 5) {       // assigns 5 to x, then tests "is 5 truthy" — always true!
    std::cout << "x is 5\n";   // always runs
}
```

```cpp
// RIGHT — double == is comparison
int x = 0;
if (x == 5) {      // compares x to 5 — false, body does not run
    std::cout << "x is 5\n";
}
```

`= ` is **assignment**; `==` is **comparison**. `if (x = 5)` assigns `5` to `x`, returns `5`, and then the `if` tests "is `5` truthy" — which is always true, so the body always runs. This is one of the most famous C and C++ bugs. The compiler will warn you with `-Wall` (`warning: suggest parentheses around assignment used as truth value`). Some style guides recommend "Yoda conditions" — `if (5 == x)` — so that a typo (`if (5 = x)`) becomes a compile error, but modern C++ code rarely does this. Just be careful, turn on warnings, and read them.

### Mistake 3: Infinite loop from wrong update expression

```cpp
// WRONG — i-- instead of i++ makes the condition never become false
for (int i = 0; i < 10; i--) {
    std::cout << i << '\n';
}
// i starts at 0, goes to -1, -2, -3, ... never reaches 10 — infinite loop
```

```cpp
// RIGHT — increment i to count up
for (int i = 0; i < 10; i++) {
    std::cout << i << '\n';
}
// Prints 0, 1, 2, ..., 9
```

A `for` loop continues as long as its condition is true. If the update expression moves the counter in the wrong direction (`i--` when the condition is `i < 10`), the condition never becomes false, and the loop runs forever. This is a common bug when modifying existing loops. The fix is to check the direction of the update against the direction implied by the condition. If your program "hangs" forever, press `Ctrl+C` to stop it, then re-read your loop logic.

### Mistake 4: Modifying the collection you are iterating

```cpp
// WRONG — modifying a vector while iterating it with range-for is undefined behavior
std::vector<int> nums = {1, 2, 3, 4, 5};
for (int n : nums) {
    if (n == 3) {
        nums.push_back(99);     // adds an element — invalidates the iteration
    }
}
```

```cpp
// RIGHT — collect changes, apply after the loop (or use indices carefully)
std::vector<int> nums = {1, 2, 3, 4, 5};
std::vector<int> additions;
for (int n : nums) {
    if (n == 3) {
        additions.push_back(99);
    }
}
for (int n : additions) {
    nums.push_back(n);
}
```

When you iterate a `std::vector` with a range-based `for`, the loop holds an iterator into the vector. If you `push_back`, `pop_back`, or `resize` the vector during the loop, the underlying array may be reallocated — and the iterator becomes invalid, pointing to freed memory. Reading from it is **undefined behavior** (crash, garbage data, or worse). The rule is: never modify a container's structure (size, capacity) while iterating it. If you need to add or remove elements, collect the changes in a separate container and apply them after the loop ends, or use index-based iteration with care. (Some containers like `std::list` and `std::set` have iterator stability guarantees — but `std::vector` does not.)

---

## Summary

- `if`/`else if`/`else` works as in Java and C#. The condition can be any expression convertible to bool — **zero is false, anything else is true** in C++, unlike Java's strict boolean requirement.
- `switch` jumps to a matching `case` label. Cases must be constant integer expressions; `default` runs when nothing matches. **Forgetting `break` causes fall-through** — the most famous `switch` bug.
- The classic C-style `for` has three parts: `for (init; condition; update)`. All three are optional; `for (;;)` is an infinite loop.
- The range-based `for` (C++11+) iterates over every element: `for (int n : nums)` (by value), `for (int& n : nums)` (mutable), `for (const auto& n : nums)` (read-only, preferred).
- `while` checks the condition before each iteration (zero or more iterations); `do-while` checks after (one or more iterations).
- `break` exits the innermost loop or switch; `continue` skips to the next iteration of the innermost loop. Both affect only the innermost loop in nested cases.
- The ternary `? :` is a one-line `if`/`else` that produces a value. Use for simple cases; avoid nesting.
- `goto` exists in C++ but is discouraged — the one acceptable use is breaking out of nested loops.

You now have all the tools you need to write structured, branching, looping C++ code. In Lesson 05 we cover functions — how to declare them, pass arguments by value vs reference vs pointer, give them default arguments, overload them, and write modern lambda expressions.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
