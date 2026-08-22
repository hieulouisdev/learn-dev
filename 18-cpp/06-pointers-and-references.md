# Lesson 06: Pointers and References

> Module: C++ · Lesson 6 of 7
> Estimated time: 45–60 minutes

This is the lesson. Everything you have learned so far — variables, types, control flow, functions — exists in some form in Java, Python, Rust, and Go. But pointers, manual memory management, and the C++ memory model are uniquely C and C++. They are the source of nearly every bug the language is famous for, and they are the reason Rust was invented forty years later. In this lesson we go deep into how memory actually works in C++: what a pointer is and what it points at, the difference between the stack and the heap, how to allocate and free memory manually with `new` and `delete`, why dangling pointers and double frees are so dangerous, and how modern C++ (C++11+) uses **smart pointers** and the **RAII** idiom to make memory management almost as safe as a garbage-collected language — with zero runtime cost.

Take this lesson slowly. Read every code example, type it out, and run it. The concepts here are not difficult, but they are unforgiving — a single `delete` you forget, a single pointer you dereference after freeing, and your program crashes or, worse, behaves correctly 99 times out of 100 and crashes on the 100th. By the end of this lesson you will understand what every C++ program is really doing when it allocates a variable, and you will have the foundation you need to read production C++ code with confidence.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain what a pointer is, draw an ASCII diagram showing a variable and a pointer to it, and use `&` (address-of) and `*` (dereference) correctly.
2. Distinguish between pointers and references, explain when to use each, and describe the rules references follow (must be initialized, cannot be null, cannot be reseated).
3. Allocate and free heap memory with `new`/`delete` and `new[]`/`delete[]`, and explain why forgetting to free, freeing twice, or dereferencing after free are all undefined behavior.
4. Use `std::unique_ptr` and `std::shared_ptr` from `<memory>` to manage heap memory automatically, and explain how RAII makes C++ memory management safe without a garbage collector.

---

## 1. What is a Pointer?

A **pointer** is a variable that holds a **memory address**. That is it. A regular `int` variable holds an integer value (like `5`); a pointer-to-int variable holds the address where some other `int` lives. The pointer does not contain the value — it contains the location of the value. This distinction is the entire point of pointers, and it takes practice to keep straight.

Here is the situation in memory. Suppose you have an `int x = 5;` and an `int* p = &x;`. The variable `x` lives at some address (let's call it `0x1000`), holds the value `5`. The pointer `p` lives at another address (say `0x2000`), holds the value `0x1000` — which is the address of `x`. Drawing it:

```text
        Memory
        ┌────────────────────────────────────┐
        │  Address     Name    Value         │
        ├────────────────────────────────────┤
        │  0x1000       x       5            │   ← x is stored here
        │  0x1004       (gap)   ...          │
        │  ...                                  │
        │  0x2000       p       0x1000       │   ← p is stored here, holds x's address
        └────────────────────────────────────┘

        `p` "points to" `x` because p's value is x's address.
```

You can have a pointer to any type: `int*`, `double*`, `char*`, `std::string*`, even `int**` (a pointer to a pointer to an `int`). The type of the pointer tells the compiler how to interpret the bytes at the address — when you dereference an `int*`, the compiler reads 4 bytes as an `int`; when you dereference a `double*`, it reads 8 bytes as a `double`. A pointer whose type is `void*` ("pointer to `void`") holds an address but the compiler does not know the type — you have to cast it before dereferencing. `void*` is mostly a C-era feature; modern C++ code rarely uses it.

The size of a pointer is platform-dependent — on a 64-bit system, all pointers are 8 bytes (64 bits, enough to address 2^64 bytes of memory). On a 32-bit system, all pointers are 4 bytes. Use `sizeof(int*)` or `sizeof(char*)` to check; they are always the same on a given platform, regardless of the pointed-to type.

Why do pointers exist? Three reasons:

1. **To share data with functions without copying.** Instead of passing a 1MB `std::vector` by value (which copies all 1MB), pass a pointer or reference to it (which copies 8 bytes).
2. **To manage memory whose lifetime is not tied to a single function.** A local variable is destroyed when its function returns; if you need the variable to outlive the function, you allocate it on the heap (with `new`) and pass around a pointer to it.
3. **To build dynamic data structures.** Linked lists, trees, graphs — these are all built from nodes connected by pointers. Without pointers, you cannot build a linked list.

Pointers are also the source of the most dangerous bugs in C and C++. We will see why as we go through the lesson.

---

## 2. The Address-of Operator `&`

The `&` operator (called **address-of**) takes an lvalue (a named variable) and returns its address. You use it to initialize a pointer or pass a pointer to a function.

```cpp
#include <iostream>

int main() {
    int x = 5;
    int* p = &x;          // p now holds the address of x

    std::cout << "x = " << x << '\n';
    std::cout << "&x = " << &x << '\n';        // prints the address of x (like 0x7ffd1234abcd)
    std::cout << "p = " << p << '\n';          // prints the same address
    std::cout << "&p = " << &p << '\n';        // prints the address of p itself (different)
    return 0;
}
```

Sample output (your addresses will differ):

```text
x = 5
&x = 0x7ffd1234abcd
p = 0x7ffd1234abcd
&p = 0x7ffd1234abdd
```

Notice that `&x` and `p` print the same value — `p`'s value is `x`'s address. The address of `p` itself (`&p`) is different — `p` is a separate variable that lives somewhere else in memory. A pointer is just a variable; like any variable, it has its own address.

A few rules about `&`:

- `&` requires an **lvalue** — a named thing with an address. `&5` is a compile error because `5` is a literal, not an lvalue. `&x` is fine because `x` has an address.
- `&` does not work on `register` variables (an obscure case you will not encounter).
- You can take the address of any lvalue, including array elements (`&arr[3]`), struct members (`&s.field`), and dereferenced pointers (`&*p`, which is just `p`).

Note: `&` has two meanings in C++. When `&` appears in a **declaration** like `int& ref = x;`, it means "reference." When `&` appears in an **expression** like `&x`, it means "address-of." Same character, two completely different things. The compiler knows which one you mean from context.

---

## 3. The Dereference Operator `*`

The `*` operator (called **dereference**) is the inverse of `&`. Given a pointer, `*p` reads or writes the value at the address the pointer holds.

```cpp
#include <iostream>

int main() {
    int x = 5;
    int* p = &x;          // p points to x

    std::cout << *p << '\n';     // reads x's value through p: prints 5

    *p = 10;               // writes 10 to x through p
    std::cout << x << '\n';      // x is now 10 — p modified it

    *p = *p + 1;           // reads through p, adds 1, writes through p
    std::cout << x << '\n';      // x is now 11
    return 0;
}
```

- `*p` reads the value at the address `p` holds.
- `*p = 10;` writes `10` to the address `p` holds. Because `p` points to `x`, this changes `x`.
- `*p = *p + 1;` reads through `p` (gets `10`), adds `1` (gets `11`), writes through `p` (sets `x` to `11`).

Dereferencing an uninitialized pointer or a null pointer is **undefined behavior**. The program usually crashes with a segmentation fault, but the C++ standard allows anything to happen — including "appears to work fine" until it does not. Always initialize pointers, and check for null before dereferencing if null is a possibility.

Like `&`, the `*` character has two meanings. In a declaration like `int* p = &x;`, the `*` means "pointer to." In an expression like `*p = 10;`, the `*` means "dereference." Same character, two meanings; context tells the compiler which one.

A common style debate: write `int* p` (the `*` hugs the type, suggesting "int pointer") or `int *p` (the `*` hugs the variable, suggesting "the value of `*p` is an `int`")? Both compile identically. The C-style is `int *p`; the C++ style is `int* p`. We use `int* p` throughout this module. (The C-style makes sense when you write `int *p, *q;` to declare two pointers — `int* p, q;` makes `p` a pointer and `q` an int, which is confusing.)

---

## 4. Pointer Arithmetic

C++ lets you add and subtract integers from pointers. The result is a new pointer that points to a different memory location. The arithmetic is **scaled by the size of the pointed-to type** — adding `1` to an `int*` advances it by `4` bytes (the size of an `int`), not by `1` byte.

```cpp
#include <iostream>

int main() {
    int arr[5] = {10, 20, 30, 40, 50};
    int* p = arr;         // arr decays to &arr[0] — pointer to first element

    std::cout << *p << '\n';         // prints 10 (arr[0])
    std::cout << *(p + 1) << '\n';   // prints 20 (arr[1]) — p+1 advances by sizeof(int)=4 bytes
    std::cout << *(p + 2) << '\n';   // prints 30 (arr[2])

    p++;                  // advance p by one element (now points to arr[1])
    std::cout << *p << '\n';         // prints 20

    int diff = (p + 3) - p;          // pointer subtraction — result is 3 (number of elements)
    std::cout << "diff = " << diff << '\n';
    return 0;
}
```

The scaling rule: `p + n` advances `p` by `n * sizeof(*p)` bytes. So `int* p + 1` advances by 4 bytes (one `int`), `double* p + 1` advances by 8 bytes (one `double`), `char* p + 1` advances by 1 byte (one `char`). This makes pointer arithmetic "count in elements," not "count in bytes" — which is convenient when you are walking through an array.

Pointer arithmetic is mainly used for **arrays**. As we saw in Lesson 03, arrays "decay" to pointers when passed to functions, and `arr[i]` is exactly equivalent to `*(arr + i)`. The array subscript operator `[]` is just syntactic sugar for pointer arithmetic. This is why C-style arrays do not know their size — they have decayed to a pointer, and a pointer has no size information.

You can also subtract two pointers (which gives the number of elements between them, as a `ptrdiff_t`), and compare two pointers with `<`, `>`, `<=`, `>=`, `==`, `!=`. These comparisons are only meaningful if both pointers point into the same array (or one past the end); otherwise the result is unspecified.

**Warning**: pointer arithmetic is a frequent source of bugs. Going one element past the end of an array (`arr + 5` when `arr` has 5 elements) is allowed — you can compare against this "one past the end" pointer. But **dereferencing** it is undefined behavior. Walk carefully through arrays; use `std::vector` and range-based `for` whenever possible to avoid manual pointer arithmetic.

---

## 5. Null Pointers

A **null pointer** is a pointer that does not point to any valid object. The modern way to write a null pointer is `nullptr` (C++11+), which is a keyword of type `std::nullptr_t`. The legacy forms are `NULL` (a macro from `<cstddef>`) and the integer `0`. All three compile, but `nullptr` is preferred because it is type-safe — it converts to any pointer type but not to an integer, which prevents some subtle overload-resolution bugs.

```cpp
#include <iostream>

int main() {
    int* p = nullptr;       // modern, preferred
    int* q = NULL;           // legacy C
    int* r = 0;              // also legacy, even older

    if (p == nullptr) {
        std::cout << "p is null\n";
    }
    if (!p) {                // equivalent: nullptr is "false" in a boolean context
        std::cout << "p is still null\n";
    }

    // NEVER do this — dereferencing nullptr is undefined behavior (usually a crash)
    // std::cout << *p << '\n';

    int x = 5;
    p = &x;                  // now p points to x
    if (p != nullptr) {      // always check before dereferencing if null is possible
        std::cout << *p << '\n';
    }
    return 0;
}
```

Dereferencing a null pointer is **undefined behavior**. On most operating systems, the operating system has marked the low addresses (where `nullptr` points) as inaccessible, so the program crashes with a segmentation fault (SIGSEGV). On embedded systems without memory protection, the program may instead read whatever garbage is at address 0 — which can cause spectacularly weird behavior. The rule is: never dereference a pointer that might be null.

The defensive pattern is:

```cpp
void process(int* p) {
    if (p == nullptr) {
        return;       // or throw, or report an error
    }
    // now it is safe to use *p
    std::cout << *p << '\n';
}
```

This is one reason references are preferred for "no null is a valid state" parameters — a reference cannot be null, so you do not need the check.

A **dangling pointer** is worse than a null pointer. A null pointer at least fails predictably when dereferenced. A dangling pointer points to memory that **used to** be valid but has been freed or gone out of scope — the memory has been returned to the allocator and may have been reused for something else. Dereferencing a dangling pointer may give you garbage, may give you the old value (if the memory has not been reused yet), or may crash. This is the hardest bug in C++ to find. We will see how to create and avoid dangling pointers below.

---

## 6. Pointers and Arrays

Arrays and pointers are deeply related in C and C++. When you write `int arr[5];` and use the name `arr` in an expression, it **decays** to a pointer to the first element. This is why passing an array to a function loses the size information — the function receives a pointer, not an array.

```cpp
#include <iostream>

void print_array(int* p, int size) {
    for (int i = 0; i < size; i++) {
        std::cout << p[i] << ' ';    // p[i] is *(p + i)
    }
    std::cout << '\n';
}

int main() {
    int arr[5] = {10, 20, 30, 40, 50};

    // arr decays to int* when passed to a function
    print_array(arr, 5);

    // You can also use arr directly as a pointer:
    int* p = arr;            // same as int* p = &arr[0];
    std::cout << p[2] << '\n';         // prints 30 — arr[2] and p[2] are the same

    // Pointer arithmetic
    std::cout << *(p + 3) << '\n';     // prints 40 — same as p[3]

    // The arrays relationship: arr[i] ≡ *(arr + i) ≡ i[arr] (yes, really)
    std::cout << 2[arr] << '\n';       // prints 30 — obscure but legal C
    return 0;
}
```

The relationship `arr[i] == *(arr + i)` is fundamental. C and C++ define array subscripting in terms of pointer arithmetic — `arr[i]` is just shorthand for `*(arr + i)`. The bizarre consequence is that `i[arr]` also works — because `*(i + arr)` is the same as `*(arr + i)` due to commutativity. You will never write `2[arr]` in real code, but knowing it is legal helps you understand that `[]` is just sugar for pointer arithmetic.

The "array decay" rule is why C-style arrays are dangerous: once passed to a function, the function has no way to know the array's size. You have to pass the size as a separate parameter (the `int size` in `print_array` above), or use a sentinel value (like the null terminator at the end of a C-string). This is the source of countless buffer-overflow bugs in C and C++ programs. The fix is `std::vector` or `std::array`, which **do** know their size. Use them whenever you have the choice.

---

## 7. References in Depth

We met references briefly in Lesson 03. Now we look at them in more depth and contrast them with pointers.

A **reference** is an **alias** for another variable. Declared with `&` after the type:

```cpp
int x = 5;
int& ref = x;     // ref is now another name for x

ref = 10;          // modifies x — they share the same storage
std::cout << x;    // prints 10
```

Internally, the compiler usually implements a reference as a pointer — `ref` is a hidden pointer that the compiler auto-dereferences for you. The user-visible behavior is "another name for the same variable," but the underlying mechanism is essentially a constant pointer that is auto-dereferenced.

The **four rules of references**:

1. **A reference must be initialized when declared.** `int& ref;` is a compile error — you must write `int& ref = x;`. This rule prevents dangling references from being declared without a target.
2. **A reference cannot be null.** Unlike a pointer, a reference always refers to a valid object. (Pedantically, you can create a dangling reference by binding it to a temporary that immediately dies, or by binding it to dereferenced null pointer — both are undefined behavior, but the language has no "null reference" concept.)
3. **A reference cannot be reseated.** Once bound, a reference is bound to the same variable for its entire lifetime. `ref = y;` does not rebind `ref` to `y` — it **assigns** the value of `y` to the variable `ref` refers to (which is `x`). This is the most confusing reference behavior for beginners.
4. **A reference does not have its own address.** `&ref` gives you the address of the referent (i.e., `&x`), not the address of the reference itself. There is no "pointer to a reference" — references are not objects.

These rules make references **safer than pointers** in most cases. The C++ style guide is: **prefer references over pointers when "no value" is not a valid state and you do not need to rebind**. Use pointers when null is a meaningful option, when you need to do pointer arithmetic, or when you need to rebind the pointer to a different variable later.

The most common use of references is **function parameters**. As we saw in Lesson 05:

```cpp
// Pass by const reference — no copy, no modification
void print_vector(const std::vector<int>& v);

// Pass by reference — modify the caller's variable
void fill_vector(std::vector<int>& v);

// Pass by const reference (preferred for large types)
std::string concat(const std::string& a, const std::string& b);
```

References are also used for **range-based for loops** (`for (const auto& x : things)`), for **function return values** when the returned object outlives the call (like returning a reference to a class member), and for **rvalue references** (`&&`, used in move semantics — a more advanced topic).

A reference to a local variable becomes **dangling** when the local goes out of scope. We cover this in the dangling pointer section below — references and pointers share this danger.

---

## 8. Dynamic Memory: `new` and `delete`

So far, all our variables have lived on the **stack** — the area of memory managed automatically by the compiler. When you write `int x = 5;` inside a function, `x` is allocated on the stack. When the function returns, `x` is automatically destroyed (its memory is reclaimed by adjusting the stack pointer). Stack allocation is fast (one instruction) and safe (no leaks) — but it has one big limitation: the variable must die when its function returns. If you need a variable that outlives the function that creates it, you need the **heap**.

The **heap** is a region of memory managed manually by the programmer. You allocate memory with `new`, get back a pointer to it, and are responsible for freeing it with `delete` when you are done. The variable lives on the heap until you explicitly free it — even after the function that allocated it returns.

```cpp
#include <iostream>

int* make_int(int value) {
    int* p = new int(value);   // allocate one int on the heap, initialize it
    return p;                   // safe — the int outlives the function
}

int main() {
    int* p = make_int(42);
    std::cout << *p << '\n';    // prints 42
    delete p;                    // free the heap memory — REQUIRED
    return 0;
}
```

Let's dissect:

- `new int(value)` — asks the runtime for enough memory to hold one `int` (4 bytes), initializes it with `value`, and returns a pointer to it. The memory is on the heap, not the stack.
- The function returns the pointer. The heap memory is not destroyed when the function returns — only the stack-allocated pointer variable `p` (in `make_int`) is destroyed. The heap memory lives on.
- The caller receives the pointer in its own `p`, dereferences it, prints the value.
- `delete p;` — **returns the heap memory to the runtime**. This is mandatory. If you forget, the memory is **leaked** — it stays allocated forever (until the program exits), and you have no way to access or free it. Repeat this bug many times in a long-running program and you run out of memory.

The stack vs heap distinction is crucial. Here is an ASCII diagram:

```text
            STACK                              HEAP
┌──────────────────────┐         ┌──────────────────────────┐
│ function locals      │         │ memory allocated by new │
│ int x = 5            │         │                          │
│ int* p (in make_int) │ ──────▶ │ int  (value = 42)       │
│                      │         │                          │
│ automatically freed  │         │ manually freed by delete │
│ when function returns│         │ when you say so          │
└──────────────────────┘         └──────────────────────────┘
```

Stack memory is **automatic** — allocated when the function is entered, freed when it returns. Heap memory is **manual** — you allocate it with `new`, you free it with `delete`, and the program does nothing to help you remember.

### Arrays on the heap

For arrays, use `new[]` and `delete[]` (with square brackets):

```cpp
#include <iostream>

int main() {
    int* arr = new int[10];         // allocate 10 ints on the heap

    for (int i = 0; i < 10; i++) {
        arr[i] = i * i;             // initialize
    }

    for (int i = 0; i < 10; i++) {
        std::cout << arr[i] << ' ';
    }
    std::cout << '\n';

    delete[] arr;                   // MUST use delete[] (not delete) for arrays
    return 0;
}
```

**Critical rule**: if you allocate with `new`, free with `delete`. If you allocate with `new[]`, free with `delete[]`. Mismatching them (`new` then `delete[]`, or `new[]` then `delete`) is **undefined behavior**. On some platforms it appears to work; on others it crashes or corrupts memory. Always match them.

### Manual memory management is dangerous

Manual memory management with `new`/`delete` is the source of three of the most dangerous bugs in C++:

1. **Memory leak**: you `new` something but never `delete` it. The memory stays allocated until the program exits. In a long-running program (a web server, a game), leaks accumulate and eventually exhaust memory.
2. **Double free**: you `delete` the same pointer twice. The second `delete` corrupts the heap allocator's bookkeeping, often causing a crash hours later in completely unrelated code. This is one of the hardest bugs to find.
3. **Dangling pointer** (use-after-free): you `delete` a pointer, then later dereference it. The memory has been returned to the allocator, but your pointer still holds the address — and the allocator may have given that address to someone else. You read or write someone else's data, causing silent corruption or crashes.

Here is a small program that demonstrates all three bugs (do not run it on a production system):

```cpp
#include <iostream>

int main() {
    // Memory leak — allocate, never free
    int* leak = new int(42);
    // (forgot delete leak;)

    // Double free — delete twice
    int* d = new int(10);
    delete d;
    // delete d;       // UNCOMMENT THIS FOR UNDEFINED BEHAVIOR

    // Dangling pointer — use after free
    int* dangling = new int(100);
    delete dangling;
    // std::cout << *dangling << '\n';   // UNCOMMENT FOR UNDEFINED BEHAVIOR

    // Returning a pointer to a stack local — also dangling
    // int* get_local() { int x = 5; return &x; }   // VERY BAD
    // (the local is destroyed when the function returns)
    return 0;
}
```

These bugs are so common that modern C++ (C++11+) introduced **smart pointers** to make them nearly impossible. We cover smart pointers next. The rule for new code is: **never write `delete` yourself. Use smart pointers.** The rest of this lesson teaches smart pointers; the `new`/`delete` material above is for understanding what smart pointers do under the hood.

---

## 9. Smart Pointers (C++11+)

A **smart pointer** is a class that wraps a raw pointer and **automatically deletes the pointed-to object** when the smart pointer goes out of scope. Smart pointers give you the safety of garbage collection without the runtime cost — the destructor runs at a deterministic time (when the smart pointer goes out of scope), not at some unpredictable future garbage-collection pause.

The three smart pointers in the C++ standard library (from `<memory>`) are:

| Smart Pointer | Ownership Model | When to Use |
|---------------|-----------------|-------------|
| `std::unique_ptr<T>` | **Single owner.** Only one `unique_ptr` owns the object at a time. Cannot be copied; can be moved. | The default choice. Use for almost all heap allocation. |
| `std::shared_ptr<T>` | **Shared ownership.** Multiple `shared_ptr`s can point to the same object. Uses reference counting. | When ownership is genuinely shared (rare). |
| `std::weak_ptr<T>` | **Non-owning observer** of a `shared_ptr`. Does not extend the lifetime. | To break reference cycles in `shared_ptr` graphs. |

### `std::unique_ptr` — the default

`std::unique_ptr<T>` owns an object on the heap. When the `unique_ptr` is destroyed (goes out of scope), it automatically `delete`s the object. There can be only one `unique_ptr` owning a given object — you cannot copy a `unique_ptr`, only move it.

```cpp
#include <iostream>
#include <memory>       // for std::unique_ptr

int main() {
    // Create a unique_ptr with std::make_unique (C++14+, preferred)
    auto p = std::make_unique<int>(42);

    std::cout << *p << '\n';         // prints 42 (dereference like a raw pointer)
    std::cout << p.get() << '\n';     // .get() returns the raw pointer (without transferring ownership)

    // p goes out of scope here — the int is automatically deleted
    // No memory leak, no double free possible
    return 0;
}
```

- `std::make_unique<int>(42)` (C++14+) creates a `unique_ptr<int>` and is the preferred way — it is safer than `std::unique_ptr<int>(new int(42))` because it avoids a subtle exception-safety bug.
- You dereference with `*p` and access members with `p->member` (just like a raw pointer).
- `p.get()` returns the underlying raw pointer (useful when calling APIs that take raw pointers — but the `unique_ptr` still owns the memory).
- When `p` goes out of scope, its destructor calls `delete` on the underlying pointer. **No memory leak possible.**

Transferring ownership with `std::move`:

```cpp
#include <iostream>
#include <memory>

void take_ownership(std::unique_ptr<int> p) {
    std::cout << "Now I own: " << *p << '\n';
    // p is destroyed here — the int is deleted
}

int main() {
    auto p = std::make_unique<int>(99);
    take_ownership(std::move(p));    // transfers ownership to the function
    // p is now empty (nullptr) — using *p here would be undefined behavior
    std::cout << "p is now: " << (p ? "not null" : "null") << '\n';
    return 0;
}
```

`std::move(p)` casts `p` to an rvalue reference, which signals "I am giving up ownership — please move from me instead of copying." After the move, `p` is in a "moved-from" state, which for `unique_ptr` means it holds `nullptr`. This is the only way to transfer `unique_ptr` ownership — copy is forbidden by design.

### `std::shared_ptr` — shared ownership

`std::shared_ptr<T>` allows multiple smart pointers to share ownership of the same object. It uses **reference counting** — each `shared_ptr` keeps a count of how many `shared_ptr`s point to the object. When the count drops to zero (the last `shared_ptr` is destroyed), the object is deleted.

```cpp
#include <iostream>
#include <memory>

int main() {
    auto p1 = std::make_shared<int>(42);
    auto p2 = p1;                       // copy — both point to the same int, count = 2
    auto p3 = p2;                       // copy — count = 3

    std::cout << *p1 << ' ' << *p2 << ' ' << *p3 << '\n';   // 42 42 42
    std::cout << "use count: " << p1.use_count() << '\n';   // 3

    {
        auto p4 = p1;                   // count = 4 inside this block
        std::cout << "use count: " << p1.use_count() << '\n';   // 4
    }                                   // p4 destroyed — count back to 3

    std::cout << "use count: " << p1.use_count() << '\n';   // 3
    return 0;
}                                       // p1, p2, p3 destroyed — count drops to 0, int deleted
```

- `std::make_shared<int>(42)` (preferred) creates a `shared_ptr<int>`.
- Copying a `shared_ptr` increments the count; destroying one decrements it.
- `use_count()` returns the current count.
- When the count hits zero, the pointed-to object is deleted.

`shared_ptr` is more expensive than `unique_ptr` — every copy and every destruction involves an atomic counter update (thread-safe but slow). Use `shared_ptr` only when ownership is genuinely shared; otherwise prefer `unique_ptr`. A common use case is a graph or tree where multiple parents need to point to the same child.

### `std::weak_ptr` — breaking cycles

`std::weak_ptr<T>` is a non-owning observer of a `shared_ptr`. It does not extend the object's lifetime — if all `shared_ptr`s are destroyed, the object is deleted even if there are still `weak_ptr`s observing it. `weak_ptr` is mainly used to **break reference cycles**: if two `shared_ptr`s point at each other (A → B and B → A), neither's count ever reaches zero, so both leak. Making one of the links a `weak_ptr` breaks the cycle.

```cpp
#include <memory>
#include <iostream>

struct Node {
    std::shared_ptr<Node> next;       // strong reference — extends lifetime
    std::weak_ptr<Node>     prev;     // weak reference — does not extend lifetime
    ~Node() { std::cout << "Node destroyed\n"; }
};

int main() {
    auto a = std::make_shared<Node>();
    auto b = std::make_shared<Node>();
    a->next = b;                       // a -> b (shared)
    b->prev = a;                       // b -> a (weak — does not cycle)
    // When a and b go out of scope, both are destroyed (no cycle)
    return 0;
}
```

`weak_ptr` is an advanced topic — you will rarely need it as a beginner. The rule for now: use `unique_ptr` by default, `shared_ptr` when ownership is genuinely shared, and `weak_ptr` to break cycles in `shared_ptr` graphs.

---

## 10. RAII (Resource Acquisition Is Initialization)

**RAII** is the central design idiom of C++. The name is awkward but the idea is simple: **tie the lifetime of a resource (memory, file, lock, socket) to the lifetime of an object.** When the object is constructed, the resource is acquired. When the object is destroyed (goes out of scope), the resource is released. Because C++ guarantees that destructors run when objects go out of scope — even when an exception is thrown — this pattern makes resource management automatic and exception-safe.

Here is RAII in action for several kinds of resources:

```cpp
#include <iostream>
#include <memory>
#include <fstream>
#include <mutex>

void process_file(const std::string& filename) {
    // RAII for files: std::fstream opens in its constructor, closes in its destructor
    std::ifstream f(filename);
    if (!f) return;
    std::string line;
    while (std::getline(f, line)) {
        std::cout << line << '\n';
    }
    // No need to close — f's destructor closes the file when f goes out of scope
}

void process_data() {
    // RAII for memory: std::vector manages its own buffer
    std::vector<int> v(1000);
    for (int i = 0; i < 1000; i++) v[i] = i * i;
    // No need to free — v's destructor frees its buffer when v goes out of scope

    // RAII for heap memory: std::unique_ptr
    auto p = std::make_unique<int>(42);
    // No need to delete — p's destructor deletes when p goes out of scope
}

std::mutex m;
void safe_increment(int& counter) {
    // RAII for locks: std::lock_guard locks in its constructor, unlocks in its destructor
    std::lock_guard<std::mutex> lock(m);
    counter++;
    // No need to unlock — lock's destructor unlocks when lock goes out of scope
}
```

In each case:

- The resource is **acquired in the constructor** of a stack-allocated object (`std::ifstream`, `std::vector`, `std::unique_ptr`, `std::lock_guard`).
- The resource is **released in the destructor**, which runs automatically when the object goes out of scope — at the end of the function, the end of a `{}` block, or during stack unwinding when an exception is thrown.
- The programmer never explicitly frees the resource. The destructor does it.

Why is RAII so powerful? Three reasons:

1. **It cannot be forgotten.** Unlike `new`/`delete` (where forgetting `delete` causes a leak), the destructor runs no matter what. Even if the function returns early, throws an exception, or has multiple exit points — the destructor always runs.
2. **It is exception-safe.** If an exception is thrown in the middle of a function, all stack-allocated objects' destructors run as the stack unwinds. Resources are not leaked. This is something garbage-collected languages struggle with — they typically need `try`/`finally` blocks for non-memory resources.
3. **It is zero-cost.** There is no runtime overhead — the destructors are just function calls at known points, and the compiler can often inline them. C++'s "zero-cost abstractions" principle is realized through RAII.

Almost every modern C++ type uses RAII:

- `std::string`, `std::vector`, `std::map` — manage their own heap memory.
- `std::unique_ptr`, `std::shared_ptr` — manage heap objects.
- `std::fstream`, `std::ofstream` — manage file handles.
- `std::thread`, `std::jthread` — manage OS threads.
- `std::mutex` with `std::lock_guard`/`std::unique_lock` — manage locks.
- `std::unique_lock` — also for condition variables.

The lesson for new C++ programmers: **never write `delete` yourself**. Use RAII types (`std::vector`, `std::string`, `std::unique_ptr`, `std::fstream`, `std::lock_guard`) and let the destructors handle resource management. This is the modern C++ way, and it is what makes C++11 and later far safer than C++98 and C — without sacrificing performance.

If you want to use RAII with your own classes (e.g., a class that wraps a database connection or a network socket), you write the resource acquisition in the constructor and the release in the destructor. We will cover classes and destructors in future modules. The principle is the same: **acquire in the constructor, release in the destructor, and let C++'s automatic lifetime management do the rest.**

---

## Try It Yourself

Before moving on, do these three exercises. They should take 10–15 minutes each. This is the most important lesson in the module — do not skip these.

### Exercise 1: Pointer Basics

Write a program that declares an `int x = 5;`, takes its address with `&`, dereferences the pointer with `*`, and modifies `x` through the pointer. Print `x` and `*p` after each modification to confirm they always show the same value. Print the address stored in `p` using `std::cout << p`. Then declare a `nullptr` and verify that `if (!p)` evaluates to true. Add a `nullptr` check before any dereference so the program never crashes from a null dereference.

### Exercise 2: Smart Pointer Ownership

Write a function `std::unique_ptr<int> make_value(int v)` that returns a `unique_ptr<int>` wrapping `v`. From `main`, call it, store the result, dereference and print it, then call a second function `void consume(std::unique_ptr<int> p)` that takes ownership by accepting the `unique_ptr` by value (you will need to pass it with `std::move`). Print the value inside `consume`. After `consume` returns, check whether the `unique_ptr` in `main` is null (it should be — ownership was transferred). Include `<memory>` and use `std::make_unique`.

### Exercise 3: RAII Comparison

Write two versions of a function that opens a file (or, if you have no file, allocates an `int` on the heap). Version 1 uses raw `new`/`delete` — allocate in the function, dereference, print, and explicitly delete at the end. Version 2 uses `std::make_unique` — let RAII handle the cleanup. Demonstrate that version 2 is shorter and that even if you `return` early from the middle of the function, version 2 does not leak (the `unique_ptr`'s destructor runs as the stack unwinds). Add comments noting the difference.

---

## Common Mistakes

### Mistake 1: Forgetting to `delete` (memory leak)

```cpp
// WRONG — allocates with new, never deletes — leaks memory
void leak() {
    int* p = new int(42);
    std::cout << *p << '\n';
    // forgot delete p — the int is leaked
}

int main() {
    while (true) {
        leak();        // each call leaks 4 bytes — eventually runs out of memory
    }
    return 0;
}
```

```cpp
// RIGHT — use std::unique_ptr; the destructor deletes automatically
#include <memory>

void no_leak() {
    auto p = std::make_unique<int>(42);
    std::cout << *p << '\n';
    // p's destructor deletes the int — no leak
}
```

Every `new` must be matched by exactly one `delete`. If you forget, the memory is leaked — it stays allocated until the program exits. In a short-lived program this might be acceptable, but in a long-running program (a web server, a game loop) leaks accumulate and eventually exhaust memory. The fix is to **never write `delete` yourself** — use `std::unique_ptr` (or `std::vector`, `std::string`, etc.) and let the destructor handle it. The RAII idiom makes leaks almost impossible.

### Mistake 2: `delete`-ing twice (double free)

```cpp
// WRONG — deletes the same pointer twice — undefined behavior
int main() {
    int* p = new int(42);
    delete p;
    delete p;          // DOUBLE FREE — undefined behavior, usually crashes the program
    return 0;
}
```

```cpp
// RIGHT — use std::unique_ptr; cannot be deleted twice
#include <memory>

int main() {
    auto p = std::make_unique<int>(42);
    // No delete needed — p's destructor handles it
    // No way to "delete twice" — the destructor runs exactly once
    return 0;
}
```

Deleting the same pointer twice is **undefined behavior**. The first `delete` returns the memory to the allocator; the second `delete` tries to return it again, corrupting the allocator's bookkeeping. The crash may not happen immediately — it may happen hours later in completely unrelated code, making this one of the hardest bugs to find. To avoid this: (1) use smart pointers, which make double-free impossible; (2) if you must use raw pointers, set them to `nullptr` after `delete` (`delete p; p = nullptr;`) so a second `delete` is a no-op (deleting `nullptr` is safe and does nothing).

### Mistake 3: Dereferencing nullptr or a dangling pointer

```cpp
// WRONG — dereferencing nullptr is undefined behavior (usually a crash)
int main() {
    int* p = nullptr;
    std::cout << *p << '\n';        // CRASH — segmentation fault
    return 0;
}
```

```cpp
// WRONG — use after free is undefined behavior (may print garbage, may crash)
int main() {
    int* p = new int(42);
    delete p;
    std::cout << *p << '\n';        // UNDEFINED — p is now dangling
    return 0;
}
```

```cpp
// RIGHT — always check before dereferencing if null is possible; never use after free
int main() {
    int* p = nullptr;
    if (p != nullptr) {
        std::cout << *p << '\n';
    } else {
        std::cout << "p is null, skipping\n";
    }

    // For heap memory, use unique_ptr — it is automatically set to nullptr when moved-from
    return 0;
}
```

Dereferencing a null pointer causes a **segmentation fault** on most operating systems — the program crashes immediately. This is the "good" case because the bug is obvious. The "bad" case is a **dangling pointer** (use-after-free): the memory was freed and may have been reallocated for something else. Dereferencing a dangling pointer may give you garbage data, may give you the old data (if the memory has not been reused yet), or may crash. The behavior is **undefined** — it can change between runs, between compilers, between optimization levels. This makes dangling pointer bugs extraordinarily hard to reproduce and fix.

The fix is:

1. Prefer references over pointers when null is not a valid state.
2. Prefer smart pointers over raw pointers for ownership.
3. If you must use raw pointers, always check for null before dereferencing.
4. Never use a pointer after `delete` — set it to `nullptr` immediately, or (better) use a smart pointer that handles this for you.

### Mistake 4: Returning a pointer/reference to a local variable

```cpp
// WRONG — returns a pointer to a local that is destroyed when the function returns
int* get_local() {
    int x = 5;
    return &x;          // x is destroyed here — the returned pointer is dangling
}

int main() {
    int* p = get_local();
    std::cout << *p << '\n';    // UNDEFINED BEHAVIOR — reads from dead memory
    return 0;
}
```

```cpp
// RIGHT — return by value (RVO makes the copy free) or use the heap with a smart pointer
int get_value() {
    int x = 5;
    return x;            // returns a copy — safe
}

#include <memory>
std::unique_ptr<int> get_heap_value() {
    auto p = std::make_unique<int>(5);
    return p;            // returns ownership — safe
}
```

A local variable is destroyed when its function returns. If you return a pointer or reference to it, the caller receives a dangling pointer — the variable is gone, the memory has been returned to the stack, and accessing it through the pointer is undefined behavior. The same rule applies to references: `int& get_local() { int x = 5; return x; }` is just as broken. The compiler usually warns you with `-Wall` (`warning: reference to local variable 'x' returned`).

The fixes are: (1) return by value (`int get_value()`) — the compiler applies Return Value Optimization (RVO) and constructs the result directly in the caller's storage, so there is no copy; (2) if you genuinely need the variable to outlive the function, allocate it on the heap with `std::make_unique` and return the smart pointer. Both are safe. Returning a reference to a local is never safe.

---

## Summary

- A **pointer** is a variable that holds a **memory address**. Use `&` (address-of) to get a variable's address, `*` (dereference) to read or write through a pointer. Pointers are 8 bytes on 64-bit systems, regardless of the pointed-to type.
- **Pointer arithmetic** is scaled by the pointed-to type's size — `int* p + 1` advances by 4 bytes (one `int`). Array subscripting `arr[i]` is sugar for `*(arr + i)`.
- **Null pointers** (`nullptr`, C++11+) point to no object. Dereferencing `nullptr` is undefined behavior (usually a crash). Check for null before dereferencing if null is a possibility.
- **Arrays decay to pointers** when passed to functions, which is why C-style arrays lose their size. Use `std::vector` or `std::array` to keep the size information.
- A **reference** (`int& ref = x;`) is an alias for `x`. References must be initialized, cannot be null, and cannot be reseated. Prefer references over pointers when "no value" is not a valid state.
- The **stack** is automatic, fast, and freed when functions return. The **heap** is manual — you allocate with `new`, free with `delete`, and are responsible for the lifetime.
- `new`/`delete` allocate/free single objects; `new[]`/`delete[]` allocate/free arrays. **Never mismatch them** — undefined behavior.
- Manual memory management causes three classic bugs: **memory leaks** (forget to delete), **double frees** (delete twice), and **dangling pointers** (use after free). All are undefined behavior.
- **Smart pointers** (`<memory>`): `std::unique_ptr` (single owner, the default), `std::shared_ptr` (reference-counted shared ownership), `std::weak_ptr` (non-owning observer for breaking cycles). Smart pointers automatically `delete` in their destructors.
- **RAII** (Resource Acquisition Is Initialization) is the central C++ idiom: acquire resources in constructors, release them in destructors. Because destructors run automatically when objects go out of scope (even on exceptions), RAII makes resource management automatic and exception-safe. **Never write `delete` yourself** — use RAII types like `std::unique_ptr`, `std::vector`, `std::string`, `std::fstream`, and `std::lock_guard`.

You have completed the hardest lesson in the module. Pointers, manual memory, smart pointers, and RAII are the heart of C++ — and now you understand them. In Lesson 07 we wrap up with a one-page cheatsheet that you can print and keep next to you as you write C++ code.

---

**Next:** [Lesson 07: C++ Cheatsheet →](./07-cpp-cheatsheet.md)
