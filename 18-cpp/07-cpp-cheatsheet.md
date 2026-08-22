# Lesson 07: C++ Cheatsheet

> Module: C++ · Lesson 7 of 7
> Estimated time: 30–45 minutes

This is the final lesson of the C++ module — and it is not really a lesson at all. It is a **reference card**: a single page that summarizes everything you have learned in Lessons 01–06, organized so you can scan it quickly while you are writing code. Print it out, bookmark it, or keep it open in a second monitor. The goal is to make the most-used syntax and idioms available at a glance, so you do not have to hunt through six lessons when you forget whether `std::endl` flushes or how to write a lambda capture.

Read this page once now to see the big picture, then come back to it as a reference whenever you need a refresher. Each section links back to the lesson that explains it in depth. After the cheatsheet, you will find a "What's Next?" section pointing you to Module 19: Kotlin — the next stop in the Learn Dev curriculum.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use this page as a quick-reference for C++ syntax, types, control flow, functions, and pointers.
2. Identify the most common C++ pitfalls at a glance and know the fix for each.
3. Recall the 10 most-used standard library headers and what each provides.
4. Decide what to study next — Module 19 (Kotlin) or a deeper C++ topic like templates, the STL, or game engine development.

---

## 1. Hello, World!

The minimal C++ program. Every program you write will start from this skeleton.

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

Save as `hello.cpp`, compile with `g++ -std=c++17 -o hello hello.cpp`, run with `./hello`. (See [Lesson 01](./01-what-is-cpp.md) and [Lesson 02](./02-hello-world.md) for the breakdown.)

---

## 2. Compiler Commands

The recommended compile command for everyday development:

```bash
g++ -std=c++17 -Wall -Wextra -O2 -o hello hello.cpp
```

| Flag | Purpose |
|------|---------|
| `-std=c++17` | Use the C++17 standard (use `-std=c++20` for newer features) |
| `-Wall` | Enable most common warnings |
| `-Wextra` | Enable extra warnings (sign-compare, missing-field-initializers, etc.) |
| `-Werror` | Treat warnings as errors (strict, optional) |
| `-pedantic` | Reject non-standard compiler extensions (strict, optional) |
| `-O0` | No optimization (default; fast compile, slow run; for debugging) |
| `-O2` | Optimization level 2 (release; standard balance of speed and compile time) |
| `-O3` | Optimization level 3 (more aggressive; sometimes slower than -O2) |
| `-Os` | Optimize for binary size |
| `-g` | Include debug info (for `gdb`/`lldb`); use with `-O0` |
| `-o hello` | Name the output executable `hello` (default: `a.out`) |
| `-c` | Compile to object file (`.o`) without linking |
| `-E` | Run preprocessor only (output `.ii`) |
| `-S` | Compile to assembly (`.s`) without assembling |
| `-I path` | Add `path` to the include search path |
| `-L path` | Add `path` to the library search path |
| `-l name` | Link with library `libname.so` or `libname.a` |

Typical debug build: `g++ -std=c++17 -Wall -Wextra -g -O0 -o prog prog.cpp`
Typical release build: `g++ -std=c++17 -Wall -Wextra -O2 -o prog prog.cpp`
Multi-file build: `g++ -std=c++17 -Wall -Wextra -o prog main.cpp utils.cpp math.cpp`

(See [Lesson 02](./02-hello-world.md) for the full breakdown.)

---

## 3. Primitive Types

| Type | Typical Size | Range / Notes |
|------|--------------|---------------|
| `bool` | 1 byte | `true` or `false` |
| `char` | 1 byte | -128 to 127 (or 0 to 255 if `unsigned char`) |
| `signed char` | 1 byte | -128 to 127 |
| `unsigned char` | 1 byte | 0 to 255 |
| `char8_t` | 1 byte | UTF-8 character (C++20+) |
| `char16_t` | 2 bytes | UTF-16 character (C++11+) |
| `char32_t` | 4 bytes | UTF-32 character (C++11+) |
| `wchar_t` | 2 or 4 bytes | Wide character (platform-dependent; prefer `char*_t`) |
| `short` | 2 bytes | -32768 to 32767 |
| `unsigned short` | 2 bytes | 0 to 65535 |
| `int` | 4 bytes | -2.1 billion to +2.1 billion (typically) |
| `unsigned int` | 4 bytes | 0 to 4.3 billion |
| `long` | 4 or 8 bytes | Platform-dependent (32-bit on Windows, 64-bit on Linux/Mac) |
| `long long` | 8 bytes | -9.2 × 10^18 to +9.2 × 10^18 (C++11+) |
| `unsigned long long` | 8 bytes | 0 to 1.8 × 10^19 |
| `float` | 4 bytes | ~7 decimal digits |
| `double` | 8 bytes | ~15 decimal digits |
| `long double` | 16 bytes | Platform-dependent (often 80-bit on x86) |

**Fixed-width integers** from `<cstdint>`:

| Type | Width | Notes |
|------|-------|-------|
| `int8_t` | 8 bits signed | Same as `signed char` |
| `uint8_t` | 8 bits unsigned | Same as `unsigned char` |
| `int16_t` | 16 bits signed | Same as `short` on most platforms |
| `uint16_t` | 16 bits unsigned | |
| `int32_t` | 32 bits signed | Same as `int` on most platforms |
| `uint32_t` | 32 bits unsigned | |
| `int64_t` | 64 bits signed | Same as `long long` on most platforms |
| `uint64_t` | 64 bits unsigned | |
| `size_t` | platform-dependent | Unsigned type for sizes (typically `uint64_t` on 64-bit) |
| `ptrdiff_t` | platform-dependent | Signed type for pointer differences |

(See [Lesson 03](./03-variables-and-types.md) for the full breakdown.)

---

## 4. Variables

The three initialization forms:

```cpp
int x = 5;     // copy initialization (legacy C, allows narrowing)
int x(5);      // direct initialization (rarely used for primitives)
int x{5};      // brace initialization (C++11+, preferred — prevents narrowing)
int x{};       // brace initialization with no value — zero-initializes
```

`auto` (type deduction, C++11+):

```cpp
auto i = 5;              // int
auto d = 3.14;           // double
auto s = std::string("hi");   // std::string (auto deduces from initializer)
auto p = std::make_unique<int>(42);   // std::unique_ptr<int>
for (const auto& x : vec) { /* ... */ }   // const reference in range-for
```

Constants:

```cpp
const int x = 5;            // runtime const (value known only at runtime)
constexpr int y = 10;       // compile-time const (C++11+, can be used in array sizes)
#define MAX 100             // preprocessor macro — AVOID, use const/constexpr instead
```

**Const correctness rule**: mark every variable `const` unless you need to modify it. `const` is viral — if you pass a `const` object to a function, the function must take it by `const T&` (or by value).

(See [Lesson 03](./03-variables-and-types.md) for the full breakdown.)

---

## 5. Strings

`std::string` from `<string>` is the modern C++ string — mutable, manages its own memory, supports `+`/`+=`/`==`/`[]`/`.length()`/`.substr()`/`.find()`.

| Operation | Syntax | Notes |
|-----------|--------|-------|
| Declare | `std::string s = "hello";` | Or `std::string s{"hello"};` |
| Length | `s.length()` or `s.size()` | Both return `size_t` |
| Concatenate | `s + " world"` or `s += " world"` | Returns new string or modifies in place |
| Compare | `s == "hello"`, `s < "world"` | Lexicographic comparison |
| Index | `s[0]`, `s.at(0)` | `.at()` does bounds checking, throws `std::out_of_range` |
| Substring | `s.substr(0, 3)` | Start index, length |
| Find | `s.find("ell")` | Returns index or `std::string::npos` if not found |
| Insert | `s.insert(2, "XX")` | Inserts at position |
| Erase | `s.erase(2, 3)` | Erases 3 chars starting at position 2 |
| C-string | `s.c_str()` | Returns `const char*` (null-terminated) |
| Empty? | `s.empty()` | Returns `true` if length is 0 |
| Clear | `s.clear()` | Makes the string empty |

`std::string_view` (C++17+) is a lightweight non-owning reference to a string. Use it for function parameters that just need to read the string without copying:

```cpp
void print(std::string_view s);     // accepts std::string, const char*, or literal — no copy
```

C-style strings (`const char*`, `char[]`) are legacy — avoid for new code, but you will see them in APIs and string literals. (See [Lesson 03](./03-variables-and-types.md) for the full breakdown.)

---

## 6. Containers (Preview)

| Container | Header | Description |
|-----------|--------|-------------|
| `std::array<T, N>` | `<array>` | Fixed-size array, knows its size, stack-allocated |
| `std::vector<T>` | `<vector>` | Dynamic array, the default container |
| `std::deque<T>` | `<deque>` | Double-ended queue, fast push/pop at both ends |
| `std::list<T>` | `<list>` | Doubly-linked list, O(1) insert/erase anywhere |
| `std::forward_list<T>` | `<forward_list>` | Singly-linked list, smaller than `std::list` |
| `std::map<K, V>` | `<map>` | Sorted key-value map (red-black tree), O(log n) |
| `std::unordered_map<K, V>` | `<unordered_map>` | Hash table, O(1) average lookup |
| `std::set<T>` | `<set>` | Sorted set of unique elements |
| `std::unordered_set<T>` | `<unordered_set>` | Hashed set of unique elements |
| `std::stack<T>` | `<stack>` | LIFO stack (adapter over `std::deque` by default) |
| `std::queue<T>` | `<queue>` | FIFO queue (adapter) |
| `std::priority_queue<T>` | `<queue>` | Max-heap (adapter) |

Common `std::vector` methods:

```cpp
std::vector<int> v = {1, 2, 3};
v.push_back(4);          // add to end
v.pop_back();            // remove from end
v.size();                // number of elements
v.empty();               // true if size == 0
v.clear();                // remove all elements
v[0];                    // access (no bounds check)
v.at(0);                 // access (with bounds check, throws)
v.front();               // first element
v.back();                // last element
v.begin();               // iterator to first
v.end();                 // iterator to one-past-last
v.reserve(100);          // pre-allocate capacity for 100 elements (avoid reallocations)
```

(See [Lesson 03](./03-variables-and-types.md) and [Lesson 05](./05-functions.md) for usage examples.)

---

## 7. Control Flow

```cpp
// if / else if / else
if (cond) { /* ... */ }
else if (cond2) { /* ... */ }
else { /* ... */ }

// if with initializer (C++17+)
if (auto it = m.find(key); it != m.end()) { /* use it */ }

// switch
switch (x) {
    case 1: /* ... */; break;
    case 2:
    case 3: /* stacked cases — same body */; break;
    default: /* ... */; break;
}

// classic for
for (int i = 0; i < 10; i++) { /* ... */ }

// range-based for (C++11+)
for (int n : vec) { /* by value — copies */ }
for (int& n : vec) { /* by reference — modifies elements */ }
for (const auto& n : vec) { /* by const reference — preferred for big types */ }

// while
while (cond) { /* ... */ }

// do-while (runs at least once)
do { /* ... */ } while (cond);

// break / continue
for (int i = 0; i < 10; i++) {
    if (i == 5) break;        // exit the loop entirely
    if (i % 2 == 0) continue; // skip to next iteration
}

// ternary
int max = (a > b) ? a : b;

// goto (avoid — only use for breaking out of nested loops)
goto label;
label:
```

(See [Lesson 04](./04-control-flow.md) for the full breakdown.)

---

## 8. Functions

```cpp
// Definition
int add(int a, int b) {
    return a + b;
}

// Declaration (prototype) — in a .h file
int add(int a, int b);

// Pass by value (small types)
void f(int x);

// Pass by reference (modify caller's variable)
void f(int& x);

// Pass by const reference (no copy, no modification — preferred for big types)
void f(const std::string& s);

// Pass by pointer (lower-level, can be null)
void f(int* p);

// Default arguments (in declaration only, rightmost)
void greet(const std::string& name, const std::string& greeting = "Hello");

// Function overloading (same name, different params)
int square(int x);
double square(double x);

// inline (suggests inlining; modern compilers decide)
inline int square(int x) { return x * x; }

// constexpr function (can run at compile time)
constexpr int factorial(int n) { return n <= 1 ? 1 : n * factorial(n - 1); }

// Lambda (C++11+)
auto add = [](int a, int b) { return a + b; };

// Lambda with captures
int x = 10;
auto get_x = [x]() { return x; };          // capture x by value
auto inc_x = [&x]() { x++; };              // capture x by reference
auto sum_all = [=]() { /* captures all by value */ };
auto mod_all = [&]() { /* captures all by reference */ };

// Lambda used with <algorithm>
std::sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
std::for_each(v.begin(), v.end(), [](int n) { std::cout << n; });
auto it = std::find_if(v.begin(), v.end(), [](int n) { return n > 5; });
```

(See [Lesson 05](./05-functions.md) for the full breakdown.)

---

## 9. Pointers and References Cheat Card

```cpp
// Address-of: get the address of a variable
int x = 5;
int* p = &x;          // p holds x's address

// Dereference: read or write through a pointer
std::cout << *p;       // prints 5
*p = 10;               // sets x to 10

// Null pointer (C++11+)
int* p = nullptr;
if (p != nullptr) { std::cout << *p; }    // always check before dereferencing

// Pointer arithmetic (scaled by pointed-to type)
int arr[5] = {10, 20, 30, 40, 50};
int* p = arr;          // arr decays to &arr[0]
std::cout << *(p + 2);  // prints 30 — same as arr[2]

// Reference: alias for a variable
int x = 5;
int& ref = x;          // ref is another name for x
ref = 10;              // modifies x
// References: must be initialized, cannot be null, cannot be reseated

// Heap allocation — single object
int* p = new int(42);
delete p;              // MUST delete exactly once

// Heap allocation — array
int* arr = new int[10];
delete[] arr;          // MUST use delete[] (not delete)

// Smart pointers (modern, preferred — <memory> header)
#include <memory>
auto p = std::make_unique<int>(42);          // single owner
auto sp = std::make_shared<int>(42);         // shared, reference-counted
std::weak_ptr<int> wp = sp;                  // non-owning observer
// No delete needed — destructors run automatically

// Common pointer/reference parameter patterns
void f(int x);                    // by value (small types)
void f(int& x);                   // by reference (modify caller)
void f(const int& x);             // by const reference (read-only, no copy)
void f(int* p);                  // by pointer (caller passes &x)
void f(const int* p);            // by const pointer (read-only through pointer)
void f(int* p, size_t n);        // C-style array + size — avoid, use std::vector
```

Smart pointer comparison:

| Feature | `unique_ptr<T>` | `shared_ptr<T>` | `weak_ptr<T>` |
|---------|-----------------|-----------------|---------------|
| Ownership | Single owner | Shared (ref-counted) | None (observer) |
| Copyable? | No (move only) | Yes (increments count) | Yes (no count change) |
| Overhead | None (sizeof == raw ptr) | Atomic counter (memory + atomic ops) | Small (count observer) |
| Use case | Default for heap allocation | Genuine shared ownership | Breaking cycles |
| Frees memory when... | Goes out of scope | Count reaches zero | Never (does not own) |

(See [Lesson 06](./06-pointers-and-references.md) for the full breakdown.)

---

## 10. Memory Model

```text
            STACK                              HEAP
┌──────────────────────┐         ┌──────────────────────────┐
│ function locals      │         │ memory allocated by new  │
│ int x = 5            │         │                          │
│ int* p               │ ──────▶ │ int (heap-allocated)    │
│                      │         │                          │
│ automatic:           │         │ manual:                  │
│ - allocated on entry  │         │ - allocated with new     │
│ - freed on return    │         │ - freed with delete       │
│ - fast (1 instruction)│        │ - slow (allocator call)  │
│ - size limited (~MB)  │        │ - size limited by RAM     │
└──────────────────────┘         └──────────────────────────┘
```

- **Stack**: automatic, fast, freed when functions return. Limited size (~1–8 MB by default on Linux; crashes if you overflow it). Use for local variables.
- **Heap**: manual (`new`/`delete`), slower, freed when you say so. Limited by available RAM. Use for large objects or objects that need to outlive the function that creates them.
- **Static storage**: globals, `static` locals, string literals. Live for the entire program. Initialized before `main` runs.

The Three Classic Bugs (and their fixes):

| Bug | Cause | Symptom | Fix |
|-----|-------|---------|-----|
| Memory leak | `new` without `delete` | Memory usage grows until OOM | Use `std::unique_ptr` / `std::vector` |
| Double free | `delete` twice | Crash, often in unrelated code | Use `std::unique_ptr` (cannot double-free) |
| Dangling pointer | Use after `delete` (or after scope exit) | Garbage data, crash, or "appears to work" | Use `std::unique_ptr`, set raw pointers to `nullptr` after delete |
| Stack overflow | Recursion too deep or large stack arrays | Segmentation fault | Use iteration, allocate large arrays on heap |

**RAII** (Resource Acquisition Is Initialization):

- **Acquire** resources in constructors.
- **Release** resources in destructors.
- The destructor runs **automatically** when the object goes out of scope — even on exceptions.
- Examples: `std::string`, `std::vector`, `std::unique_ptr`, `std::shared_ptr`, `std::fstream`, `std::lock_guard`, `std::thread`, `std::jthread`.

(See [Lesson 06](./06-pointers-and-references.md) for the full breakdown.)

---

## 11. Common Idioms

- **RAII** — acquire in constructor, release in destructor. Let object lifetime manage resource lifetime. Never write `delete` yourself.
- **Smart pointers** — `std::unique_ptr` by default, `std::shared_ptr` for shared ownership, `std::weak_ptr` to break cycles.
- **`auto`** — type deduction for complex iterator types; reduces verbosity.
- **Range-based `for`** — `for (const auto& x : things)` for read-only iteration over containers.
- **`const` correctness** — mark every variable `const` unless you need to modify it; take parameters by `const T&` when only reading.
- **`<algorithm>` header** — use `std::sort`, `std::find`, `std::transform`, `std::copy`, `std::accumulate` instead of writing your own loops.
- **`std::string` and `std::vector`** — use these instead of `char*` and raw arrays. Manage their own memory.
- **Brace initialization** — `int x{5};` prevents narrowing conversions.
- **`nullptr`** — use instead of `NULL` or `0` for null pointers.
- **`std::move`** — to transfer ownership of `unique_ptr` or move from temporaries.
- **`using std::cout;`** — bring in specific names (preferable to `using namespace std;`).

---

## 12. Common Pitfalls

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Memory leak** | `new` without `delete` | Use `std::unique_ptr` / `std::vector` |
| **Double free** | `delete` twice | Use `std::unique_ptr` (cannot double-free) |
| **Dangling pointer** | Use after `delete` | Use `std::unique_ptr`; set raw ptrs to `nullptr` |
| **Uninitialized variable** | Reads as garbage in release builds | Always initialize: `int x{};` or `int x = 0;` |
| **Integer overflow** | `INT_MAX + 1` wraps to `INT_MIN` | Use `long long` or check bounds; `<limits>` for `max()` |
| **Integer division** | `5 / 2 == 2` not `2.5` | Cast to `double` first: `static_cast<double>(a) / b` |
| **Signed/unsigned comparison** | `size_t vs int` warning; wrong logic | Use `size_t` for sizes, or use range-for |
| **Narrowing in init** | `int x = 3.14;` silently truncates | Use brace init: `int x{3.14};` is a compile error |
| **Forgetting `break` in switch** | Fall-through to next case | Add `break;`; mark intentional with `[[fallthrough]];` |
| **`if (x = 5)` typo** | Assignment, always true | Use `==`; turn on `-Wall` |
| **Returning ref to local** | Dangling reference | Return by value (RVO makes it free) |
| **`new`/`delete[]` mismatch** | Undefined behavior | Match `new` with `delete`, `new[]` with `delete[]` |
| **Modifying container during iteration** | Iterator invalidation | Collect changes, apply after the loop |
| **`using namespace std;` in header** | Pollutes every includer | Use `std::` prefix; only `using` in `.cpp` at function scope |
| **Mixed C/C++ I/O** | Out-of-order output | Pick one style, or call `std::ios::sync_with_stdio(false);` |
| **Wrong compiler standard** | Modern features rejected | Add `-std=c++17` or `-std=c++20` to compile command |

---

## 13. The 10 Most-Used Standard Library Headers

| Header | What It Provides |
|--------|------------------|
| `<iostream>` | `std::cout`, `std::cin`, `std::cerr`, `std::endl` — input/output streams |
| `<string>` | `std::string`, `std::wstring`, `std::to_string`, `std::stoi` |
| `<vector>` | `std::vector<T>` — the default dynamic array container |
| `<map>` | `std::map<K,V>` (sorted) and `std::multimap<K,V>` |
| `<unordered_map>` | `std::unordered_map<K,V>` (hashed) and `std::unordered_multimap` |
| `<algorithm>` | `std::sort`, `std::find`, `std::find_if`, `std::transform`, `std::copy`, `std::accumulate`, `std::for_each`, `std::count`, `std::remove_if`, and dozens more |
| `<memory>` | `std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`, `std::make_unique`, `std::make_shared` |
| `<functional>` | `std::function`, `std::bind`, `std::hash`, `std::ref` |
| `<chrono>` | `std::chrono::duration`, `std::chrono::time_point`, `std::this_thread::sleep_for` |
| `<fstream>` | `std::ifstream`, `std::ofstream`, `std::fstream` — file I/O |

Other frequently-used headers worth knowing:

- `<array>` — `std::array<T, N>` (fixed-size array)
- `<set>` / `<unordered_set>` — set containers
- `<deque>` / `<list>` / `<forward_list>` — other sequence containers
- `<queue>` / `<stack>` — adapters
- `<cmath>` — `std::sqrt`, `std::sin`, `std::cos`, `std::pow`, etc.
- `<cstdint>` — fixed-width integer types (`int32_t`, `uint64_t`, etc.)
- `<limits>` — `std::numeric_limits<T>::max()` / `::min()`
- `<utility>` — `std::pair`, `std::move`, `std::forward`, `std::swap`
- `<tuple>` — `std::tuple`, `std::get`, `std::make_tuple`
- `<optional>` (C++17+) — `std::optional<T>` for values that may be absent
- `<variant>` (C++17+) — `std::variant<T1, T2, ...>` type-safe union
- `<string_view>` (C++17+) — `std::string_view` non-owning string reference
- `<exception>` / `<stdexcept>` — `std::exception`, `std::runtime_error`, `std::logic_error`
- `<thread>` / `<mutex>` / `<atomic>` — concurrency primitives
- `<type_traits>` — type introspection (advanced)

The complete reference is at [cppreference.com](https://en.cppreference.com/) — bookmark it. Every standard library type, function, and header is documented there with examples.

---

## 14. What's Next?

You have completed Module 18: C++. You now know:

- The history of C++ and its relationship to C.
- How to compile C++ programs with `g++` or `clang++` using the right flags.
- The primitive types, declaration forms, `auto`, `const`/`constexpr`, strings, and arrays.
- Every form of control flow C++ offers.
- How to define functions, pass arguments by value/reference/pointer, use defaults, overloading, lambdas, and `constexpr`.
- The conceptual heart of C++: pointers, references, dynamic memory, smart pointers, and RAII.

This is enough to read and write production C++ code — but it is far from the whole language. Topics you should explore next include:

- **Classes and OOP** — constructors, destructors, access modifiers, inheritance, virtual functions, polymorphism, abstract classes, interfaces. This is the next natural step; it is where C++'s OOP features (similar to Java's) come in.
- **Templates** — generic programming. `template<typename T> T add(T a, T b)`. The entire STL is built on templates. Templates are C++'s equivalent of Java generics but far more powerful (and far more confusing).
- **The STL in depth** — iterators, algorithms (`<algorithm>`), function objects, and the full container library.
- **Move semantics and rvalue references** (`T&&`, `std::move`) — the C++11 feature that makes passing large objects efficient without copying.
- **Exceptions** (`try`/`catch`/`throw`) — C++'s error-handling mechanism.
- **Concurrency** (`<thread>`, `<mutex>`, `<atomic>`, `<future>`) — multi-threaded programming.
- **C++20 features** — concepts, ranges, modules, coroutines.

For each topic, the canonical references are **[cppreference.com](https://en.cppreference.com/)** for the standard library and **[LearnCpp.com](https://www.learncpp.com/)** for tutorials. For books, **Bjarne Stroustrup's "A Tour of C++"** (3rd edition, C++20) is a concise tour of the language by its creator, and **Scott Meyers's "Effective Modern C++"** (C++11/14) is the classic guide to writing idiomatic modern C++.

### Module 19: Kotlin

The Learn Dev curriculum continues with **[Module 19: Kotlin](/home/z/my-project/learn-dev/19-kotlin/)**. Kotlin is a modern, concise, JVM-based language that has become Google's preferred language for Android development. It fixes many of Java's pain points (verbose boilerplate, null safety, lack of extension functions) while remaining 100% interoperable with Java — you can call Java code from Kotlin and vice versa. If you completed Module 17 (Java), Kotlin will feel like Java with the rough edges sanded off. If you skipped Java, Kotlin's cleaner syntax makes it a gentler introduction to the JVM ecosystem.

Open `/home/z/my-project/learn-dev/19-kotlin/` and start Lesson 01. You have earned a break from manual memory management — Kotlin's garbage collector will hold your hand for a while.

---

**You finished Module 18. See you in Kotlin.**
