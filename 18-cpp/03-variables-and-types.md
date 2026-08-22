# Lesson 03: Variables and Types

> Module: C++ · Lesson 3 of 7
> Estimated time: 30–45 minutes

In Lesson 02 you dissected the Hello, World! program and learned how to compile multi-file projects. Now it is time to put data into your programs. C++ is statically typed, which means every variable has a type that the compiler knows at compile time — like Rust, Go, Java, and TypeScript, and unlike Python or JavaScript. In this lesson you will learn the primitive types, the three different ways to initialize a variable, the modern `auto` keyword, how constants work, the difference between C-style and C++-style strings and arrays, and the basics of references. By the end, you will be able to declare and use every kind of variable you need for the rest of the module.

If you have completed the Java or Rust module, the basic ideas will feel familiar — `int`, `double`, `bool`, and `char` exist in those languages too. But C++ has some surprising wrinkles: integer sizes vary by platform, brace initialization prevents narrowing conversions, `std::string` is mutable, and references are a fundamentally different feature from anything in Java or Rust. Take it slowly.

---

## Learning Objectives

After this lesson, you will be able to:

1. Name C++'s primitive types (`int`, `double`, `char`, `bool`, etc.), explain their platform-dependent sizes, and use `<cstdint>` for fixed-width integers like `int32_t`.
2. Declare variables using all three initialization forms — copy (`int x = 5;`), direct (`int x(5);`), and brace (`int x{5};`) — and explain why brace initialization is preferred.
3. Use `auto` for type deduction, `const` for runtime constants, and `constexpr` for compile-time constants.
4. Distinguish between `std::string` and C-style `char*`, and between C-style arrays `int nums[5]`, `std::array<int, 5>`, and `std::vector<int>`.

---

## 1. Primitive Types

C++ inherits C's primitive types and adds a few of its own. The most important ones are:

| Type | Description | Typical size | Notes |
|------|-------------|--------------|-------|
| `bool` | Boolean | 1 byte | `true` or `false` only. |
| `char` | Single character | 1 byte | Holds ASCII or UTF-8 byte. |
| `short` | Short integer | 2 bytes | At least 16 bits. |
| `int` | Default integer | 4 bytes | At least 16 bits, usually 32. |
| `long` | Long integer | 4 or 8 bytes | At least 32 bits. |
| `long long` | Long long integer | 8 bytes | At least 64 bits (C++11+). |
| `float` | Single-precision float | 4 bytes | ~7 decimal digits. |
| `double` | Double-precision float | 8 bytes | ~15 decimal digits. |
| `long double` | Extended precision | 16 bytes | Platform-dependent. |
| `wchar_t` | Wide character | 2 or 4 bytes | For non-ASCII text (legacy; prefer `char8_t`/`char16_t`/`char32_t` in C++20+). |

Each integer type also has an `unsigned` variant: `unsigned int`, `unsigned long`, etc. Unsigned types can hold non-negative values only but go twice as high — `unsigned int` ranges from 0 to ~4.2 billion, while signed `int` ranges from -2.1 billion to +2.1 billion. Use `unsigned` for quantities that are inherently non-negative (file sizes, memory addresses, array indices), but be careful with arithmetic: `unsigned int x = 0; x--;` makes `x` equal to 4294967295 (unsigned wrap-around), which is a common source of bugs.

**Crucial warning: these sizes are platform-dependent.** The C++ standard guarantees minimums (e.g., `int` is "at least 16 bits") but allows implementations to use larger sizes. On most 64-bit platforms, `int` is 32 bits, `long` is 64 bits on Linux but 32 bits on Windows, and `long long` is 64 bits everywhere. This inconsistency is one of C++'s most famous footguns. To avoid it, use the **fixed-width integer types** from the `<cstdint>` header (added in C++11):

```cpp
#include <cstdint>

int32_t  a = 42;        // exactly 32 bits, signed
uint64_t b = 9999999999ULL; // exactly 64 bits, unsigned
int8_t   c = 65;        // exactly 8 bits, signed (essentially a signed char)
uint8_t  d = 200;       // exactly 8 bits, unsigned
int16_t  e = -1234;     // exactly 16 bits, signed
```

These types let you say exactly what you mean — `int32_t` is 32 bits on every platform, period. When you are writing code that reads binary files, talks to hardware, or transmits data over a network, always use the fixed-width types. For everyday code where exact size does not matter, plain `int` is fine and more readable.

To find the size of any type at compile time, use the `sizeof` operator: `std::cout << sizeof(int) << '\n';` prints `4` on most platforms. To find the limits, include `<limits>` and use `std::numeric_limits<int>::max()` and `::min()`. We will use these in later lessons.

---

## 2. Variable Declaration

Declaring a variable in C++ is simple — you write the type, the name, and (optionally) an initial value:

```cpp
int x = 5;
double pi = 3.14159;
char grade = 'A';
bool ready = true;
std::string name = "Alice";
```

C++ uses **static typing**: the type of every variable must be known to the compiler at compile time. You cannot change a variable's type after declaration (`x = "hello";` is a compile error if `x` was declared `int`). The compiler uses the type to decide how much memory to allocate, what operations are valid, and what conversions to apply.

Variable names (called **identifiers**) must follow a few rules: they can contain letters, digits, and underscores, but cannot start with a digit; they are case-sensitive (`x` and `X` are different); and they cannot be C++ keywords (`int`, `class`, `return`, etc.). The convention in C++ (inherited from C) is `snake_case` for variables and functions, `PascalCase` for classes and structs, and `UPPER_SNAKE_CASE` for macros. We follow that convention throughout this module.

**Uninitialized variables contain garbage.** If you write `int x;` without an initial value, the compiler allocates space for `x` but does not write anything to it. Reading `x` is **undefined behavior** — it might be 0, it might be 42, it might be -9876543. In a debug build the compiler often helpfully zeros new variables, but in a release build (with optimization) you get whatever happened to be in memory. This is a classic C++ security bug — never read a variable before initializing it. When in doubt, initialize: `int x = 0;` or `int x{};` (which zero-initializes).

Here is a complete program demonstrating declarations:

```cpp
#include <iostream>
#include <string>

int main() {
    int age = 30;
    double height = 1.75;
    char initial = 'A';
    bool is_student = false;
    std::string name = "Alice";

    std::cout << name << " is " << age << " years old, "
              << height << "m tall, initial " << initial << "."
              << std::endl;
    return 0;
}
```

Each line declares one variable with a type, a name, and an initial value. The `<<` operator chains together to print them all on one line. Note how `std::cout` knows how to print each type — `int`, `double`, `char`, `bool` (printed as `1` or `0` by default, or `"true"`/`"false"` after `std::boolalpha`), and `std::string` — without you specifying format strings like C's `printf` does.

---

## 3. Initialization Forms

C++ has, somewhat notoriously, **three different ways to initialize a variable**. This is a historical accident — each form was added in a different era, and they have subtly different semantics. Knowing all three will help you read other people's code, but you only need to use one form yourself.

```cpp
int x = 5;     // (1) Copy initialization — inherited from C
int x(5);      // (2) Direct initialization — function-call syntax
int x{5};      // (3) Brace (uniform) initialization — C++11+, preferred
```

**Copy initialization** (`int x = 5;`) is the oldest form, inherited from C. The `=` suggests an assignment, but technically it is initialization (a distinction that matters for complex types). It works for primitives and most types, but it has a quirk: it allows **narrowing conversions**. `int x = 3.14;` compiles without complaint and silently truncates `3.14` to `3`, losing data. This is usually not what you want.

**Direct initialization** (`int x(5);`) uses function-call syntax. It works for most types and was the preferred form in early C++ for objects with constructors. It has the same narrowing issue as copy initialization: `int x(3.14);` also silently truncates. Today it is rarely used for primitives but you will see it for class types.

**Brace initialization** (`int x{5};`), introduced in C++11, is also called **uniform initialization** or **list initialization**. The braces `{ }` were originally used for arrays and aggregate types; C++11 extended them to work for every type. Brace initialization has a critical safety advantage: it **prevents narrowing conversions**. `int x{3.14};` is a **compile error**, not a silent truncation — the compiler refuses to lose data without an explicit cast. This is the modern preferred form, and what we will use throughout the rest of the module whenever the choice matters.

```cpp
int a = 3.14;    // OK, but a == 3 (silent truncation)
int b(3.14);     // OK, but b == 3 (silent truncation)
int c{3.14};     // COMPILE ERROR: narrowing conversion from double to int
int d = (int)3.14;  // OK, explicit cast, d == 3
int e = int(3.14);  // OK, explicit cast (C++ style), e == 3
int f{int(3.14)};   // OK, explicit cast then brace init, f == 3
```

There is also a fourth form: `int x{};` initializes `x` to its **default value**, which for primitives is zero. So `int x{};` is equivalent to `int x = 0;` but works for any type — `std::string s{};` constructs an empty string, `std::vector<int> v{};` constructs an empty vector. This is the safest way to declare a variable when you do not yet know its value: it guarantees a known state and prevents the garbage-value bug.

A small caveat on brace initialization: for some obscure technical reasons involving constructors that take `std::initializer_list`, brace initialization can sometimes pick a different constructor than you intended. This is rare in beginner code but worth knowing about — if your class has both a `std::initializer_list` constructor and another constructor with the same number of arguments, brace init prefers the initializer-list version. For primitive types and the standard containers, brace init is always safe.

---

## 4. `auto` (C++11+)

The `auto` keyword tells the compiler to **deduce the type** of a variable from its initializer. Instead of writing the type yourself, you write `auto` and let the compiler figure it out. This is the same idea as `var` in Java 10+, `let` in Rust, or `:=` in Go.

```cpp
auto x = 5;            // x is int (5 is an int literal)
auto pi = 3.14;        // pi is double (3.14 is a double literal)
auto name = "Alice";   // name is const char* (!) — surprising, see below
auto initial = 'A';    // initial is char
auto flag = true;      // flag is bool
```

`auto` is most useful when the type name is long or annoying to type. The classic example is iterating over a `std::map`:

```cpp
std::map<std::string, int> ages;
// ...fill the map...

// Without auto, the iterator type is verbose:
for (std::map<std::string, int>::const_iterator it = ages.cbegin();
     it != ages.cend(); ++it) {
    std::cout << it->first << ": " << it->second << '\n';
}

// With auto, it is short:
for (auto it = ages.cbegin(); it != ages.cend(); ++it) {
    std::cout << it->first << ": " << it->second << '\n';
}
```

A few important rules about `auto`:

1. **`auto` strips `const` and references** by default. If you have `const int& ref = x;` and write `auto y = ref;`, `y` is a plain `int` copy, not a reference. To preserve `const` or reference, write them explicitly: `const auto& y = ref;`.

2. **String literals deduce to `const char*`, not `std::string`.** `auto name = "Alice";` makes `name` a `const char*` (a C-style string pointer). If you want a `std::string`, write `auto name = std::string("Alice");` or just `std::string name = "Alice";`.

3. **Use `auto` when the type is obvious from the initializer or when the type is complex.** Do not use `auto` for primitives where the type is not obvious — `auto x = 5;` is fine, but `auto x = some_function();` is fine only if `some_function`'s return type is obvious or unimportant.

4. **`auto` works in function return types too** (C++14+): `auto add(int a, int b) { return a + b; }` deduces the return type as `int`. We will use this in Lesson 05.

The modern C++ style guide commonly recommends: "use `auto` by default; write the explicit type only when it improves readability or when type deduction would pick the wrong thing." We use `auto` sparingly in this module because explicit types are clearer for beginners — but you will see it everywhere in production C++ code.

---

## 5. Constants

C++ has three ways to mark a value as constant, and they are not interchangeable. Knowing the difference matters because it affects whether the compiler can put the value in read-only memory, fold it at compile time, or just check that you do not modify it at runtime.

```cpp
const int x = 5;          // (1) Runtime const
constexpr int y = 10;     // (2) Compile-time const (C++11+)
#define MAX 100           // (3) Preprocessor macro (legacy — avoid)
```

**`const`** means "this variable cannot be modified after initialization." The value might not be known at compile time — `const int x = read_from_file();` is valid (the value is only known at runtime, but once read it cannot be changed). The compiler enforces read-only-ness but does not necessarily fold the value into other expressions. `const` works for any type.

**`constexpr`** (introduced in C++11) means "this variable's value is known at compile time, and the compiler can use it in constant expressions." `constexpr int y = 10;` lets you write `int arr[y];` (an array whose size is a compile-time constant). `constexpr` is stricter than `const` — the initializer must be a constant expression itself. There are also `constexpr` functions, which can be evaluated at compile time if their arguments are constexpr (covered in Lesson 05).

**`#define`** is a **preprocessor macro**, not a variable. The preprocessor replaces every occurrence of `MAX` in your source with the literal text `100` before the compiler runs. Macros have no type, no scope, and no debugger visibility — the compiler never sees the name `MAX`, only the number `100`. Macros are inherited from C and are considered bad practice in modern C++ for constants. Use `const` or `constexpr` instead. (Macros are still useful for conditional compilation — `#ifdef DEBUG` — but not for constant values.)

```cpp
// Modern, idiomatic way to define a constant
constexpr int MAX_USERS = 100;
constexpr double PI = 3.14159265358979;
constexpr std::string_view GREETING = "Hello";  // C++17+

// Use them like any other variable
int user_counts[MAX_USERS];
double circumference = 2 * PI * radius;
```

A note on **`const` correctness**: a good C++ programmer marks every variable `const` if it does not need to be modified. This is called "const correctness," and it pays off enormously — the compiler catches accidental modifications, the code is more parallelizable (const data is safe to share across threads), and the intent is clearer to readers. Make `const` your default; remove it only when you actually need to mutate the variable.

---

## 6. Strings

C++ has **two kinds of strings**: the modern `std::string` from the `<string>` header, and the legacy C-style `char*` strings inherited from C. You will see both in production code, but for new code you should always prefer `std::string`.

```cpp
#include <iostream>
#include <string>

int main() {
    // Modern C++ string — mutable, safe, with methods
    std::string greeting = "Hello";
    greeting += ", World!";            // concatenation with +=
    std::string name = "Alice";
    std::string full = greeting + " My name is " + name;  // concatenation with +

    std::cout << "Length: " << full.length() << '\n';     // .length() or .size()
    std::cout << "Substring: " << full.substr(0, 5) << '\n';   // .substr()
    std::cout << "First char: " << full[0] << '\n';       // operator[]
    std::cout << "Find: " << full.find("World") << '\n';   // .find() returns index

    if (greeting == "Hello, World!") {                     // comparison operators
        std::cout << "Match!\n";
    }
    return 0;
}
```

`std::string` is **mutable** — you can change a string's contents after creation, unlike Java's `String`. It supports `+` (concatenation), `+=` (append), `==`/`!=`/`<` (comparison), `[]` (character access), `.length()` (size), `.substr(start, len)` (substring), `.find(needle)` (substring search), and dozens of other methods. It manages its own memory — when the string goes out of scope, its destructor frees the underlying character buffer automatically. This is an example of RAII, which we cover in Lesson 06.

**C-style strings** (`const char*` or `char[]`) are simply arrays of characters terminated by a null byte (`'\0'`). They have no length field — to find the length you have to walk the array until you hit `'\0'`, which is O(n). They do not support `+` or `==` (you use `strcat` and `strcmp` from `<cstring>`). They are the source of countless security bugs (buffer overflows, missing null terminators). The only places you still need C-style strings are: when calling legacy C APIs, when writing string literals (which are `const char*`), or in embedded code where every byte counts. For new code, always use `std::string`.

A useful modern addition is **`std::string_view`** (C++17+), a lightweight non-owning reference to a string or substring. It is the C++ equivalent of Go's string slice or Rust's `&str`. Use it for function parameters that just need to read a string without copying:

```cpp
#include <string_view>

void print(std::string_view s) {   // no copy — accepts std::string, const char*, or literal
    std::cout << s << '\n';
}
print("Hello");                     // OK
print(std::string("World"));        // OK
```

We will use `std::string` for most of this module. `std::string_view` is worth knowing about but is a more advanced topic.

---

## 7. Arrays

C++ has **three kinds of arrays**, each from a different era. They look similar but behave very differently. Here is the summary:

```cpp
// (1) C-style array — fixed size, decays to pointer, no size info
int nums1[5] = {1, 2, 3, 4, 5};

// (2) std::array — fixed size, knows its size, modern wrapper (C++11+)
#include <array>
std::array<int, 5> nums2 = {1, 2, 3, 4, 5};

// (3) std::vector — dynamic size, grows as needed, the default choice
#include <vector>
std::vector<int> nums3 = {1, 2, 3, 4, 5};
```

**C-style arrays** (`int nums[5];`) are inherited from C. They are fast, fixed-size, and have no `.size()` method — once you pass them to a function, they "decay" into a pointer and you lose the length information. This is a famous source of bugs in C and C++. You should generally avoid C-style arrays in new code. Use them only when calling legacy C APIs or in performance-critical code where you cannot afford even `std::array`'s tiny overhead.

**`std::array<T, N>`** (from `<array>`, C++11+) is a thin wrapper around a C-style array. It has the same fixed size and stack allocation, but it knows its own size (`.size()`), supports range-based `for`, can be copied with `=`, and does not decay to a pointer. Use `std::array` instead of C-style arrays whenever the size is known at compile time.

**`std::vector<T>`** (from `<vector>`) is **the default container in C++**. It is a dynamic array — it grows as you `push_back` elements. It manages its own memory (heap-allocated, freed in the destructor), supports `.size()`, `.push_back()`, `.pop_back()`, `[]` access, range-based `for`, and a dozen other useful methods. Whenever you need a list of things and do not have a specific reason to use something else, use `std::vector`.

Here is a typical program using all three:

```cpp
#include <iostream>
#include <array>
#include <vector>

int main() {
    int c_arr[5] = {10, 20, 30, 40, 50};        // C-style
    std::array<int, 5> std_arr = {10, 20, 30, 40, 50};   // modern fixed
    std::vector<int> vec = {10, 20, 30, 40, 50};        // dynamic

    vec.push_back(60);                          // now vec has 6 elements

    // Range-based for works on all three (we cover this in Lesson 04)
    for (int x : vec) std::cout << x << ' ';
    std::cout << '\n';

    // std::array and std::vector know their size; C-style does not (without a separate variable)
    std::cout << "std::array size: " << std_arr.size() << '\n';
    std::cout << "vector size: " << vec.size() << '\n';
    std::cout << "C-array size: " << sizeof(c_arr) / sizeof(c_arr[0]) << '\n';  // hacky
    return 0;
}
```

Note the size trick for C-style arrays: `sizeof(c_arr) / sizeof(c_arr[0])` divides the total byte size by the size of one element to get the count. This only works where the array is in scope — once you pass `c_arr` to a function, it decays to a pointer and `sizeof` returns the pointer size (8 bytes), not the array size. This is one of the most common beginner mistakes in C and C++. Use `std::array` or `std::vector` to avoid it entirely.

---

## 8. References

A **reference** is an alias for another variable. It is one of C++'s signature features, distinct from anything in Java or Python (which have implicit references everywhere) and from Rust (which has explicit borrows). References look like the variable they alias — there is no special syntax to use them.

```cpp
#include <iostream>

int main() {
    int x = 5;
    int& ref = x;       // ref is now an alias for x

    std::cout << x << '\n';      // prints 5
    std::cout << ref << '\n';    // also prints 5

    ref = 10;            // modifying ref modifies x
    std::cout << x << '\n';      // now prints 10

    return 0;
}
```

Key facts about references:

- **A reference must be initialized when declared.** `int& ref;` is a compile error — you must say what it refers to.
- **A reference cannot be reseated.** Once `ref` is bound to `x`, it is bound to `x` for its entire lifetime. `ref = y;` assigns the value of `y` to `x` — it does not rebind `ref` to `y`.
- **A reference cannot be null.** Unlike a pointer, a reference always refers to a valid object. (Pedantically, you can create a dangling reference, but the language does not have a "null reference" concept.)
- **References are often used as function parameters** to avoid copying large objects: `void print(const std::string& s);` takes a string by reference (no copy) but promises not to modify it (because of `const`). We will use this extensively in Lesson 05.

References are generally **safer than pointers** because they cannot be null and cannot be reseated. The C++ style guide is: use references when "no value" is not a valid state (the variable must always refer to something), and use pointers when null is a meaningful option. We will see pointers in Lesson 06; for now, just know that `int& ref = x;` makes `ref` another name for `x`, and any change to one changes the other.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Explore Primitive Sizes

Write a program that uses `sizeof` to print the size (in bytes) of `bool`, `char`, `short`, `int`, `long`, `long long`, `float`, `double`, and `long double` on your machine. Include `<cstdint>` and also print the size of `int32_t` and `uint64_t`. Run it on your platform and write down the numbers. Compile with `-Wall -Wextra` and fix any warnings.

### Exercise 2: Test the Three Init Forms

Write a program that declares the same variable three ways: `int a = 3.14;`, `int b(3.14);`, and `int c{3.14};`. Compile it. The first two should compile (with warnings from `-Wconversion` or `-Wall`); the third should produce a compile error. Read the error message carefully. Then change all three to use `int` literals (`5`) and confirm they all work. Add a `const` variable and a `constexpr` variable, and print all of them.

### Exercise 3: String and Vector Warmup

Write a program that asks the user for their name (using `std::cin >> name`), then asks for three numbers, stores them in a `std::vector<double>`, and prints the sum and the average. Use `for (double n : nums)` to iterate. Compile with `g++ -std=c++17 -Wall -Wextra -o stats stats.cpp` and run. Confirm that your output looks correct for the input you provide.

---

## Common Mistakes

### Mistake 1: Reading an uninitialized variable

```cpp
// WRONG — x contains garbage; reading it is undefined behavior
#include <iostream>

int main() {
    int x;
    std::cout << x << '\n';   // prints 0 in debug builds, garbage in release builds
    return 0;
}
```

```cpp
// RIGHT — always initialize
#include <iostream>

int main() {
    int x = 0;       // or int x{}; for zero-init, or int x = compute_value();
    std::cout << x << '\n';
    return 0;
}
```

In a debug build (compiled with `-g` and no optimization), the compiler often helpfully zeroes new variables, so reading `x` prints `0` and the bug goes unnoticed. In a release build (compiled with `-O2`), the compiler does not zero anything, and `x` contains whatever happened to be in that memory location — often a large negative number or a value from a previous function call. This is **undefined behavior**, which means the compiler is allowed to do literally anything — crash, leak data, format your hard drive (in theory). The fix is to always initialize your variables: `int x = 0;`, `int x{};`, or `int x = compute();`. Turn on `-Wall -Wextra` and the compiler will warn you about uninitialized variables.

### Mistake 2: Integer division

```cpp
// WRONG — 5 / 2 is 2 in integer division, not 2.5
#include <iostream>

int main() {
    int a = 5;
    int b = 2;
    double result = a / b;          // computes 5 / 2 = 2 (integer), then converts to 2.0
    std::cout << result << '\n';    // prints 2, not 2.5
    return 0;
}
```

```cpp
// RIGHT — convert at least one operand to double BEFORE the division
#include <iostream>

int main() {
    int a = 5;
    int b = 2;
    double result = static_cast<double>(a) / b;  // 5.0 / 2 = 2.5
    std::cout << result << '\n';                 // prints 2.5
    return 0;
}
```

C++'s `/` operator on two `int`s performs integer division — it discards the remainder, so `5 / 2` is `2`, not `2.5`. This is inherited from C and is the same as Java and Go. To get a floating-point result, at least one operand must be a floating-point type. The cleanest way is `static_cast<double>(a) / b`, which casts `a` to `double` before the division. (The C-style cast `(double)a / b` works too, but `static_cast` is the modern C++ style.) A common alternative is `1.0 * a / b`, which multiplies by `1.0` to force floating-point arithmetic.

### Mistake 3: Signed/unsigned comparison

```cpp
// WRONG — comparing signed int to unsigned; usually compiles with a warning
#include <iostream>
#include <vector>

int main() {
    std::vector<int> v = {1, 2, 3};
    for (int i = 0; i < v.size(); i++) {   // i is signed int, v.size() is size_t (unsigned)
        std::cout << v[i] << '\n';
    }
    return 0;
}
```

```cpp
// RIGHT — use size_t for the loop counter, or use range-for
#include <iostream>
#include <vector>

int main() {
    std::vector<int> v = {1, 2, 3};
    for (size_t i = 0; i < v.size(); i++) {   // size_t is unsigned, matches .size()
        std::cout << v[i] << '\n';
    }
    // Even better — range-based for, no index needed:
    for (int x : v) {
        std::cout << x << '\n';
    }
    return 0;
}
```

`std::vector::size()` returns `size_t`, which is an **unsigned** type (typically `uint64_t` on 64-bit platforms). When you compare a signed `int` to an unsigned `size_t`, the compiler implicitly converts the `int` to unsigned first. If your `int` is negative, it becomes a huge positive number — suddenly `-1 < v.size()` evaluates to `false`, and a loop that should never execute runs millions of times. The fix is to use `size_t` for the loop counter, or (better) use a range-based `for` loop that does not need an index at all. With `-Wsign-compare` (part of `-Wextra`), the compiler will warn you about every signed/unsigned comparison so you can fix them.

### Mistake 4: Narrowing in initialization

```cpp
// WRONG — copy init allows narrowing silently
#include <iostream>

int main() {
    int x = 3.14;        // compiles (with -Wconversion warning), x == 3, data lost
    double big = 1e20;
    int y = big;         // compiles, y == some garbage (overflow)
    std::cout << x << ' ' << y << '\n';
    return 0;
}
```

```cpp
// RIGHT — brace init refuses to narrow
#include <iostream>

int main() {
    int x{3.14};          // COMPILE ERROR — narrowing from double to int
    double big = 1e20;
    int y{static_cast<int>(big)};  // OK — explicit cast, programmer's responsibility
    std::cout << x << ' ' << y << '\n';
    return 0;
}
```

Copy and direct initialization allow narrowing conversions — silently truncating `3.14` to `3`, or wrapping a huge `double` to whatever `int` can hold. This is a frequent source of subtle bugs. **Brace initialization (`int x{3.14};`) refuses to narrow** — it is a compile error, forcing you to write `int x{int(3.14)};` or `int x{static_cast<int>(3.14)};` to acknowledge the data loss explicitly. This is one of the biggest practical reasons to prefer brace initialization in modern C++. We use it throughout the rest of the module.

---

## Summary

- C++'s primitive types include `bool`, `char`, `int`, `long`, `long long`, `float`, `double`, and `long double`. Sizes are platform-dependent — use `<cstdint>` types like `int32_t` and `uint64_t` when exact size matters.
- Variables are declared with a type, a name, and (almost always) an initializer: `int x = 5;`. C++ is statically typed; types cannot change after declaration.
- There are three initialization forms: copy (`int x = 5;`), direct (`int x(5);`), and brace (`int x{5};`). Brace init is preferred because it **prevents narrowing conversions**.
- `auto` deduces the type from the initializer: `auto x = 5;` makes `x` an `int`. Use it when the type is obvious or complex.
- `const` marks runtime constants; `constexpr` marks compile-time constants (can be used in array sizes and other constant expressions). Avoid `#define` for constants — it is a legacy macro.
- `std::string` (from `<string>`) is the modern C++ string: mutable, supports `+`/`+=`/`==`, manages its own memory. C-style `char*` strings are legacy — avoid for new code.
- For arrays: use `std::vector<T>` (dynamic, the default), `std::array<T, N>` (fixed-size, modern), or rarely C-style `int arr[N]` (legacy, no size info).
- A reference (`int& ref = x;`) is an alias for `x`. It must be initialized, cannot be null, and cannot be reseated. Use references for function parameters to avoid copies.
- Always initialize your variables, watch for integer division and signed/unsigned mismatches, and prefer brace initialization to catch narrowing at compile time.

You now know C++'s primitive types, declaration forms, `auto`, constants, strings, arrays, and references. In Lesson 04 we cover control flow — `if`, `switch`, the various `for` loops, `while`, `do-while`, and the ternary operator.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
