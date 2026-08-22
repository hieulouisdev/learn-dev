# Lesson 05: Functions

> Module: C++ · Lesson 5 of 7
> Estimated time: 30–45 minutes

In Lesson 04 you learned every form of control flow C++ offers. With `if`, `for`, and `while` you can write useful programs — but as soon as your code grows past a few dozen lines, you need a way to break it into reusable pieces. That is what functions are for. In this lesson you will learn how to define functions, how to separate their declarations from their definitions (the `.h`/`.cpp` split from Lesson 02 in more detail), the three ways to pass arguments (by value, by reference, by pointer), default arguments, function overloading, inline functions, the modern lambda expressions from C++11, and `constexpr` functions.

If you have completed the Java or Rust module, the basics will feel familiar. The big C++-specific wrinkles are: pass-by-reference is explicit (you write `int&` in the signature), pointers are a third option that does not exist in Java, default arguments are written in the declaration not the definition, and lambdas have an unusual `[...]` capture syntax that takes getting used to. Take it slowly.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define functions with `return_type name(parameters) { body }`, and explain the difference between a function **declaration** (prototype, in a `.h` file) and a function **definition** (in a `.cpp` file).
2. Pass arguments by value, by reference (`int&`), by `const` reference (`const int&`), and by pointer (`int*`) — and know when to use each.
3. Use default arguments, function overloading, and `inline` functions correctly.
4. Write lambda expressions with `[=]`, `[&]`, and explicit captures, and explain what `constexpr` functions are.

---

## 1. Defining Functions

A C++ function definition has the same structure as in C, Java, and C#: a return type, a name, a parameter list, and a body.

```cpp
#include <iostream>

// Define a function that adds two integers
int add(int a, int b) {
    return a + b;
}

int main() {
    int result = add(3, 4);
    std::cout << "3 + 4 = " << result << '\n';
    return 0;
}
```

Let's dissect the `add` function:

- `int` — the **return type**. The function returns an `int`. If a function returns nothing, use `void` (e.g., `void print_greeting() { ... }`).
- `add` — the **function name**. Same naming rules as variables: letters, digits, underscores; cannot start with a digit; case-sensitive.
- `(int a, int b)` — the **parameter list**. Each parameter has a type and a name. Multiple parameters are separated by commas. An empty `()` means "no parameters."
- `{ return a + b; }` — the **function body**. The code that runs when the function is called. Must contain a `return` statement returning a value compatible with the return type (unless the return type is `void`).

When you call `add(3, 4)`, the program jumps to the `add` function's body with `a` set to `3` and `b` set to `4`. The body computes `a + b` (`7`), returns it, and the caller receives `7`. This is the same as in Java or Python — function calls work the same way in every C-family language.

A function must be **declared before it is used**. C++ reads source files top to bottom; if you call `add` from `main` but define `add` *below* `main`, the compiler does not know `add` exists when it compiles `main`, and you get a compile error. You have two options: define the function above the caller, or write a **declaration** (also called a **prototype**) above the caller and put the definition below. We cover that next.

---

## 2. Function Declarations vs Definitions

In real programs, you split function signatures (declarations) from their bodies (definitions). The declaration goes in a **header file** (`.h`), and the definition goes in a **source file** (`.cpp`). This is the same multi-file structure we used in Lesson 02 — now we explain why.

`math_utils.h`:

```cpp
// Declaration (prototype) — tells the compiler the function exists
int add(int a, int b);
int subtract(int a, int b);
```

`math_utils.cpp`:

```cpp
// Definition — the actual body of the function
#include "math_utils.h"

int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}
```

`main.cpp`:

```cpp
#include <iostream>
#include "math_utils.h"   // brings in the declarations of add and subtract

int main() {
    std::cout << "2 + 3 = " << add(2, 3) << '\n';
    std::cout << "5 - 2 = " << subtract(5, 2) << '\n';
    return 0;
}
```

Compile both `.cpp` files together: `g++ -std=c++17 -o prog main.cpp math_utils.cpp`. The compiler sees the declarations in `math_utils.h` (via the `#include`), so it knows `add` and `subtract` exist when it compiles `main.cpp`. The actual definitions in `math_utils.cpp` get linked in at the final linking step.

Why this split? Two reasons:

1. **Reusability.** Once `math_utils.h` exists, any `.cpp` file in your project can `#include` it and use `add`/`subtract`. The implementation lives in one place; many files can call it.
2. **Compilation speed.** If you change `add`'s body in `math_utils.cpp`, only `math_utils.cpp` needs to be recompiled. Files that `#include`d `math_utils.h` do not need to be recompiled because the *declaration* (which is what they depend on) did not change. In a large project this saves enormous amounts of time.

A note on the **One Definition Rule (ODR)**: every function must have **exactly one definition** in the entire program. You can have as many declarations as you like (they are identical and harmless), but only one definition. If you accidentally define the same function twice — say, by `#include`-ing a `.cpp` file (do not do this!) — you get a linker error like "multiple definition of `add`". The `.h`/`.cpp` split exists precisely to enforce the ODR: declarations live in headers and can be included many times; definitions live in `.cpp` files and are compiled once.

---

## 3. Pass by Value vs Reference

The most important C++ function concept is the choice between passing arguments **by value**, **by reference**, or **by const reference**. The choice affects performance (does the argument get copied?) and behavior (can the function modify the caller's variable?).

```cpp
#include <iostream>

// Pass by VALUE — function gets a copy; caller's variable unchanged
void increment_by_value(int x) {
    x++;
}

// Pass by REFERENCE — function gets an alias; caller's variable IS modified
void increment_by_reference(int& x) {
    x++;
}

// Pass by CONST REFERENCE — function gets an alias, but cannot modify it
// (avoids the copy; useful for large types like std::string or std::vector)
void print(const std::string& s) {
    std::cout << s << '\n';
}

int main() {
    int n = 5;
    increment_by_value(n);
    std::cout << "After by_value: " << n << '\n';        // prints 5

    increment_by_reference(n);
    std::cout << "After by_reference: " << n << '\n';    // prints 6

    print("Hello, World!");     // no copy of the string literal
    return 0;
}
```

- **Pass by value** (`void f(int x)`): the function receives a **copy** of the argument. Changes to `x` inside the function do not affect the caller's variable. Use this for small types (`int`, `double`, `char`, pointer) where copying is cheap.
- **Pass by reference** (`void f(int& x)`): the function receives an **alias** for the caller's variable. Changes to `x` inside the function modify the caller's variable. Use this when the function needs to modify the caller's variable (like `increment_by_reference`), or when the argument is large and you want to avoid the copy but need to modify it.
- **Pass by const reference** (`void f(const int& x)` or `void f(const std::string& s)`): the function receives an alias but **cannot modify it**. No copy is made. Use this as the default for large types (`std::string`, `std::vector`, custom classes) that you only need to read.

The rule of thumb for choosing the parameter type:

```text
┌──────────────────────────────────────────────────────────────┐
│ Small type (int, double, char, pointer)?                     │
│   └─ Pass by value: void f(int x)                            │
├──────────────────────────────────────────────────────────────┤
│ Big type (std::string, std::vector, custom class)?           │
│   └─ Need to modify?  Pass by reference: void f(std::string&)│
│   └─ Read only?        Pass by const ref: void f(const std::string&)│
└──────────────────────────────────────────────────────────────┘
```

A common idiom is **"in-out parameters by reference"**: when a function needs to return multiple values, you pass them in by reference and have the function fill them in. For example, `bool parse_int(const std::string& s, int& out);` returns `true` if it parsed successfully and writes the result to `out`. We will see this pattern often.

---

## 4. Pass by Pointer

The third option is to pass a **pointer** to the variable. This is lower-level than a reference, and is inherited from C (which has no references). We cover pointers in depth in Lesson 06, but here is how they look as function parameters:

```cpp
#include <iostream>

// Pass by POINTER — function receives the address; can modify via dereference
void increment_by_pointer(int* p) {
    if (p != nullptr) {     // pointers can be null, so always check
        (*p)++;              // *p dereferences the pointer; (*p)++ increments the value
    }
}

int main() {
    int n = 5;
    increment_by_pointer(&n);   // & takes the address of n
    std::cout << "After: " << n << '\n';     // prints 6
    return 0;
}
```

- `void f(int* p)`: the parameter `p` is a pointer to an `int`.
- The caller passes `&n` (the address of `n`).
- Inside the function, `*p` dereferences the pointer to access the `int`. `(*p)++` increments the pointed-to `int`.
- Pointers can be `nullptr`, so the function should check before dereferencing.

When to use a pointer vs a reference?

- Use a **reference** when "no value" is not a valid state and you do not need to rebind.
- Use a **pointer** when "no value" (null) is meaningful, when you need to do pointer arithmetic, when you need to rebind to a different variable, or when calling C APIs that take pointers.

In modern C++, references are preferred whenever the choice is available. We will see in Lesson 06 that smart pointers (`std::unique_ptr`, `std::shared_ptr`) are preferred over raw pointers for ownership.

---

## 5. Default Arguments

C++ lets you give a parameter a **default value** — if the caller does not supply that argument, the default is used. This is similar to Python's default arguments and to Java's method overloading (Java does not have defaults directly).

```cpp
#include <iostream>
#include <string>

// Default arguments: greeting has a default of "Hello"
void greet(const std::string& name, const std::string& greeting = "Hello") {
    std::cout << greeting << ", " << name << "!\n";
}

int main() {
    greet("Alice");                // uses default: "Hello, Alice!"
    greet("Bob", "Hi");            // overrides: "Hi, Bob!"
    greet("Carol", "Good morning"); // "Good morning, Carol!"
    return 0;
}
```

Rules for default arguments:

1. **Defaults must be in the declaration, not the definition.** If the declaration is in a `.h` file and the definition is in a `.cpp` file, put `= "Hello"` in the `.h` and not in the `.cpp`. (If you put it in both, you get a compile error.)
2. **Defaults must be the rightmost parameters.** You cannot have `void f(int a = 1, int b);` — once a parameter has a default, every parameter to its right must also have one. This is because C++ resolves arguments positionally; it cannot skip a middle argument.
3. **Defaults are evaluated at the call site, not at the definition.** This matters when the default is a function call: `void f(int x = compute_default());` calls `compute_default()` every time `f` is called without arguments.
4. **You can specify only some defaults at the call site.** `greet("Bob", "Hi")` overrides only `greeting`; `name` is positional. You cannot skip `name` and supply only `greeting`.

Default arguments are a C++ feature with no direct equivalent in Java (Java uses method overloading instead — five different `greet` methods with different numbers of parameters). In C++, one function with defaults replaces five overloads.

---

## 6. Function Overloading

C++ supports **function overloading**: multiple functions can have the same name as long as their parameter lists differ. This is the same feature Java has.

```cpp
#include <iostream>
#include <string>

int    square(int x)    { return x * x; }
double square(double x) { return x * x; }
long   square(long x)   { return x * x; }

int main() {
    std::cout << square(5) << '\n';        // calls int version
    std::cout << square(5.5) << '\n';      // calls double version
    std::cout << square(5L) << '\n';      // calls long version
    return 0;
}
```

When you call `square(5)`, the compiler looks at the argument types (`int`) and picks the matching overload (`int square(int)`). When you call `square(5.5)`, it picks `double square(double)`. This is called **overload resolution**, and the rules are complex (involving exact matches, promotions, standard conversions, and user-defined conversions). If no overload matches uniquely, you get a compile error like `no matching function for call to 'square(...)'`.

Overloading is most useful when you want to provide the same operation for different types. The alternative in C++ is a **template** (generic function), which we will cover briefly in this lesson and in more depth in future modules. Templates let you write `template<typename T> T square(T x) { return x * x; }` once and have it work for every type `T` that supports `*`.

A few gotchas with overloading:

- Overloads must differ in **parameter types or count**, not in return type alone. `int f();` and `double f();` is a compile error.
- Overloads can be hard to read in error messages — the compiler prints all candidates and you have to figure out which one you meant.
- Default arguments can interact badly with overloading. If you have `void f(int)` and `void f(int, int = 0)`, a call `f(5)` is **ambiguous** because both match. The compiler will report an error.

---

## 7. Inline Functions

The `inline` keyword suggests to the compiler that the function's body should be **inlined** at the call site — that is, instead of jumping to the function and returning, the compiler copies the function's body directly into the caller. This can be faster for small functions called in tight loops, because it eliminates the function-call overhead.

```cpp
inline int square(int x) {
    return x * x;
}

int main() {
    int y = square(5);
    // The compiler might transform this into:
    // int y = 5 * 5;     // no function call
    return 0;
}
```

The truth about `inline` today:

1. Modern compilers decide whether to inline based on the function's size and the optimization level, regardless of the `inline` keyword. `inline` is more of a **suggestion** than a command.
2. The `inline` keyword has a more important legal meaning: it tells the linker that the function may be defined in multiple translation units (e.g., in a header that is included in multiple `.cpp` files) without violating the One Definition Rule. This is why short functions defined in headers are marked `inline`.
3. Member functions defined inside a class body are implicitly `inline`. Free functions defined in headers should be marked `inline` (or be `template` or `constexpr`, which are also implicitly inline).

In practice, you will rarely write `inline` yourself. The compiler is good at inlining small functions automatically, and modern style is to define short functions in headers without `inline` if they are templates or `constexpr`. We mention `inline` here because you will see it in older code and in some standard library headers.

---

## 8. Lambda Expressions (C++11+)

A **lambda expression** is an anonymous function that you can define inline at the point where you use it. Lambdas were added in C++11 and are one of the most important modern features. They are essential for the `<algorithm>` header, callbacks, and any code that wants to pass a small function as an argument.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> nums = {5, 2, 8, 1, 9, 3};

    // Sort in descending order using a lambda
    std::sort(nums.begin(), nums.end(), [](int a, int b) {
        return a > b;       // a comes before b if a > b
    });

    // Print each element using a lambda and std::for_each
    std::for_each(nums.begin(), nums.end(), [](int n) {
        std::cout << n << ' ';
    });
    std::cout << '\n';
    return 0;
}
```

The syntax of a lambda is:

```text
[ capture-list ] ( parameters ) -> return-type { body }
```

- `[ ]` — the **capture list**, which variables from the surrounding scope the lambda can use.
- `( parameters )` — same as a regular function's parameter list. Optional if empty.
- `-> return-type` — optional; the compiler can deduce the return type from the body.
- `{ body }` — the function body.

The **capture list** is what makes C++ lambdas unusual compared to Java or Python. You have to explicitly list which outside variables the lambda can access, and how (by value or by reference). The most common capture modes are:

```cpp
int x = 10;
int y = 20;

// Capture nothing
auto f0 = []() { return 0; };

// Capture all by VALUE (copies x and y at lambda-creation time)
auto f1 = [=]() { return x + y; };

// Capture all by REFERENCE (modifies x and y in the surrounding scope)
auto f2 = [&]() { x++; y++; };

// Capture specific variables by value
auto f3 = [x]() { return x; };       // copies x; does not see changes after

// Capture specific variables by reference
auto f4 = [&x]() { x++; };          // can modify x

// Mix: x by value, y by reference
auto f5 = [x, &y]() { y += x; };

// Capture 'this' for use in a member function (covered in Lesson 06+)
// auto f6 = [this]() { ... };
```

The `[=]` and `[&]` forms are convenient but dangerous — `[&]` captures everything by reference, and if any of those references outlive the surrounding scope, you have a dangling reference. The modern style guide is to capture only what you need, explicitly: `[x, &y]` instead of `[=, &y]` or `[&, x]`.

A lambda is an object with a type. You can store one in a variable with `auto`:

```cpp
auto add = [](int a, int b) { return a + b; };
std::cout << add(3, 4) << '\n';      // prints 7
```

You can also pass lambdas to functions (typically as `std::function<int(int,int)>` parameters, but that is a more advanced topic). For this module, the main place you will use lambdas is with `<algorithm>` functions like `std::sort`, `std::find_if`, `std::transform`, and `std::for_each`. We will see more examples in the cheatsheet.

---

## 9. `constexpr` Functions

A `constexpr` function is one that **can be evaluated at compile time** if its arguments are constant expressions. This lets you write functions that produce compile-time constants — useful for array sizes, template arguments, and other places where a constant is required.

```cpp
#include <iostream>

constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

int main() {
    // Compile-time evaluation — factorial<5> is computed during compilation
    constexpr int f5 = factorial(5);
    int arr[f5];                      // OK — f5 is a compile-time constant
    std::cout << "5! = " << f5 << '\n';

    // Runtime evaluation — n is read at runtime, so factorial runs at runtime
    int n;
    std::cin >> n;
    int result = factorial(n);
    std::cout << n << "! = " << result << '\n';
    return 0;
}
```

Key facts about `constexpr` functions:

- They can be called with **constant expressions** (compile-time arguments) — in that case they are evaluated at compile time, and the result is a compile-time constant.
- They can also be called with **runtime values** — in that case they behave like a regular function and run at runtime.
- They have restrictions: in C++11, the body could only contain a single `return` statement. In C++14 and later, the body can contain loops, local variables, and `if` statements — most "normal" code works.
- A `constexpr` function may call other `constexpr` functions but not regular functions.

Use `constexpr` for things like math constants, table sizes, hash functions, and other computations whose inputs are known at compile time. It costs nothing at runtime (the compiler does the work) and can dramatically improve performance.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Pass by Value, Reference, and Const Reference

Write three functions: `void double_value(int x)` (modifies a copy), `void double_ref(int& x)` (modifies the caller's variable), and `void print_vec(const std::vector<int>& v)` (no copy, no modification). Call each from `main` with appropriate arguments and use `std::cout` to verify the behavior. Confirm that `double_value` does not change the caller's variable, while `double_ref` does.

### Exercise 2: Default Arguments and Overloading

Write a function `std::string repeat(const std::string& s, int times = 1)` that returns `s` concatenated `times` times. Call it three ways: `repeat("ab")`, `repeat("ab", 3)`, `repeat("ab", 5)`. Then add an overload `int repeat(int x, int times)` that returns `x * 10^times` (or similar). Confirm that the compiler picks the right overload based on argument types. Try calling `repeat(5, 3)` — does it call the `int` overload?

### Exercise 3: Lambda with `std::sort`

Write a program with a `std::vector<int>` of `{5, 2, 8, 1, 9, 3, 7}`. Use `std::sort` with a lambda to sort the vector in three different ways: ascending order, descending order, and "odd numbers first, then even numbers" (use the modulo operator). Print the vector after each sort. Include `<algorithm>`. Capture nothing in the lambdas — they take two `int` parameters.

---

## Common Mistakes

### Mistake 1: Returning a reference to a local variable

```cpp
// WRONG — returns a reference to a local that is destroyed when the function returns
const std::string& get_name() {
    std::string local = "Alice";
    return local;     // dangling reference — local is destroyed here
}

int main() {
    const std::string& name = get_name();
    std::cout << name << '\n';      // undefined behavior — name refers to dead memory
    return 0;
}
```

```cpp
// RIGHT — return by value (a copy); the compiler optimizes away the copy with RVO
std::string get_name() {
    std::string local = "Alice";
    return local;     // safe — returns a copy
}
```

When a function returns a reference (`T&` or `const T&`), it must refer to something that outlives the function call. Returning a reference to a **local variable** is undefined behavior — the local is destroyed when the function returns, leaving the reference dangling. The fix is to return by value (`T` instead of `T&`). Modern C++ compilers apply **Return Value Optimization (RVO)** — they construct the result directly in the caller's storage, avoiding the copy. So returning `std::string` by value is both safe and fast. The rule: **never return a reference (or pointer) to a local variable**. Safe things to return references to: parameters passed by reference, static variables, heap-allocated objects (with careful lifetime management), and data members of `*this`.

### Mistake 2: Passing a temporary to a non-const `&`

```cpp
// WRONG — cannot bind a non-const reference to a temporary
void shout(std::string& s) {
    // modifies s
}

int main() {
    shout("Hello");    // ERROR — "Hello" is a temporary, cannot bind to std::string&
    return 0;
}
```

```cpp
// RIGHT — use a const reference (which CAN bind to a temporary) or pass by value
void shout(const std::string& s) {   // const & binds to temporaries
    std::cout << s << "!\n";
}

void shout_by_value(std::string s) {  // accepts a copy
    std::cout << s << "!\n";
}

int main() {
    shout("Hello");             // OK — temporary std::string constructed, bound to const &
    shout_by_value("Hello");    // OK — temporary std::string constructed, copied to s
    return 0;
}
```

A **non-const reference** (`T&`) cannot bind to a temporary (rvalue). The reason is that allowing it would let you modify something that is about to be destroyed — pointless and dangerous. A **const reference** (`const T&`) can bind to a temporary — the language extends the temporary's lifetime to the lifetime of the reference. This is a C++ specific rule with no equivalent in Java or Python. The fix is to make the parameter `const T&` (read-only, no copy) or `T` (by value, makes a copy). Use `const T&` for large read-only parameters and `T` for small ones.

### Mistake 3: Default arguments in the definition, not the declaration

```cpp
// WRONG — default in BOTH declaration (.h) and definition (.cpp)
// math.h:
void greet(std::string name, std::string greeting = "Hello");
// math.cpp:
void greet(std::string name, std::string greeting = "Hello") {   // ERROR: redefinition of default
    // ...
}
```

```cpp
// RIGHT — default in declaration ONLY
// math.h:
void greet(std::string name, std::string greeting = "Hello");
// math.cpp:
#include "math.h"
void greet(std::string name, std::string greeting) {   // no default here
    // ...
}
```

Default arguments can only be specified in **one place** — the declaration. If you have a `.h`/`.cpp` split, the default goes in the `.h` (so callers see it) and not in the `.cpp`. If you accidentally put the default in both, the compiler reports `error: redefinition of default argument`. The fix is to remove it from the `.cpp`. If you have a single-file program (no `.h`), the default goes on the function definition itself, which serves as both declaration and definition — but that only works if the function is defined before it is used.

### Mistake 4: Modifying a `const` reference

```cpp
// WRONG — trying to modify a const reference parameter
void shout(const std::string& s) {
    s += "!";     // ERROR: assignment to const std::string
    std::cout << s << '\n';
}
```

```cpp
// RIGHT — make a local copy if you need to modify
void shout(const std::string& s) {
    std::string loud = s + "!";    // make a copy with the modification
    std::cout << loud << '\n';
}

// OR change the parameter to non-const & if the caller expects modification
void shout_in_place(std::string& s) {
    s += "!";
    std::cout << s << '\n';
}
```

A `const` reference promises not to modify the object. The compiler enforces this — calling any non-`const` method (like `operator+=`, `push_back`, or a non-`const` method) is a compile error. If you need to modify the parameter, you must either make a local copy (`std::string loud = s; loud += "!";`) or change the parameter to a non-const reference (`std::string& s`) — but the latter forces the caller to pass an lvalue, not a temporary. The lesson: think carefully about whether a parameter should be `T`, `T&`, or `const T&`. The default for large types is `const T&`; only change to `T&` if you actually need to modify the caller's variable.

---

## Summary

- A function is defined as `return_type name(parameters) { body }`. Functions must be declared (prototyped) before they are used.
- The `.h`/`.cpp` split puts **declarations in headers** and **definitions in source files**. The One Definition Rule says each function has exactly one definition across the program.
- **Pass by value** for small types (the function gets a copy). **Pass by reference** (`T&`) when you need to modify the caller's variable. **Pass by const reference** (`const T&`) as the default for large types — no copy, no modification.
- **Pass by pointer** (`T*`) is the C-style alternative; the caller passes `&x`, the function dereferences with `*p`. Pointers can be null; references cannot.
- **Default arguments** (`void f(int x, int y = 10);`) let the caller omit trailing arguments. Defaults must be in the declaration, rightmost, and evaluated at the call site.
- **Function overloading** lets multiple functions share a name as long as their parameter lists differ. The compiler picks the best match by overload resolution.
- `inline` suggests inlining; modern compilers decide for themselves. The legal meaning is "safe to define in multiple translation units."
- **Lambda expressions** (`[](int x) { return x * 2; }`) are anonymous functions. The `[ capture ]` clause lists outside variables used (`[=]` by value, `[&]` by reference, `[x, &y]` explicit).
- **`constexpr` functions** can be evaluated at compile time if their arguments are constexpr — useful for compile-time constants, array sizes, and template arguments.

You now know how to define, declare, pass arguments to, overload, and inline functions in C++. In Lesson 06 we tackle the big one — pointers and references in depth, dynamic memory with `new`/`delete`, smart pointers, and RAII. This is the conceptual heart of C++ and the source of nearly every bug the language is famous for.

---

**Next:** [Lesson 06: Pointers and References →](./06-pointers-and-references.md)
