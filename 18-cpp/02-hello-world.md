# Lesson 02: Hello, World!

> Module: C++ · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you wrote, compiled, and ran your first C++ program. The output was just `Hello, World!`, but the four-line program packed a surprising amount of new vocabulary: preprocessor directives, namespaces, I/O streams, the stream insertion operator, and the `main` function. In this lesson we slow down and dissect every piece of that program until each part feels natural. By the end you will know how to compile with the right flags, how to read input from the keyboard, how namespaces work and when to escape them, how to write comments, and how to split a program across multiple files.

You do not need any new tools for this lesson — the same compiler you installed in Lesson 01 (`g++` or `clang++`) is everything you need. Open a terminal, navigate to the folder where you saved `hello.cpp`, and let's go.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain each piece of the Hello, World! skeleton — `#include`, `int main`, `std::cout`, `<<`, `std::endl`, `return 0` — in your own words.
2. Compile a C++ program with the standard flags `-std=c++17 -Wall -Wextra -O2 -g` and know what each flag does.
3. Use `std::cin` to read input from the keyboard, and distinguish between `std::cout`, `std::cerr`, and `std::endl` vs `'\n'`.
4. Split a program across multiple `.cpp` files and a `.h` header, compile them together, and explain why headers exist.

---

## 1. Anatomy of a C++ Program

Every C++ program — from "Hello, World!" to a million-line game engine — has the same skeleton. The skeleton has three parts, and you will see them in this order in nearly every `.cpp` file you write for the rest of your career:

```text
┌─────────────────────────────────────────┐
│  1. Preprocessor directives              │   #include <iostream>
│     (#include, #define)                  │   #include <string>
│                                          │   #define MAX 100
├─────────────────────────────────────────┤
│  2. (Optional) using declarations       │   using std::cout;
│     and namespace declarations          │   using namespace std;
├─────────────────────────────────────────┤
│  3. Function and class definitions      │   int main() {
│     (including main)                     │       ...
│                                          │   }
└─────────────────────────────────────────┘
```

**Preprocessor directives** come first. Every line that begins with `#` is read by the preprocessor before the actual compiler runs. The most common directive is `#include`, which copies the contents of a header file into your source text. You include `<iostream>` for `std::cout`/`std::cin`, `<string>` for `std::string`, `<vector>` for `std::vector`, and so on — each standard library feature lives in its own header. The second most common directive is `#define`, which defines a macro, but in modern C++ you should usually use `const` or `constexpr` variables instead (covered in Lesson 03).

**`using` declarations and namespace declarations** come next (optional). They tell the compiler "treat the names in this namespace as if they were in the global namespace." So `using std::cout;` lets you write `cout` instead of `std::cout`, and `using namespace std;` brings in everything from `std` at once. This is convenient but it has a cost: it pollutes the global namespace with hundreds of names, which can collide with your own. The conventional rule is: never put `using namespace std;` in a header file (because it forces every includer to inherit the pollution), but it is acceptable in a small `.cpp` file at function scope. We will mostly use the explicit `std::` prefix throughout this module — it makes the code a few characters longer but far clearer to readers.

**Function and class definitions** come last. These are the actual C++ code that does work. Among them must be exactly one function named `main`, which is the entry point the operating system calls when your program starts. The signature is `int main()` (no arguments) or `int main(int argc, char* argv[])` (with command-line arguments). The body of `main` runs top to bottom; when `main` returns, the program exits with that return code.

That is the skeleton. Every C++ program you write will fit this shape: includes at the top, optional `using` declarations, then code below. The order matters — the preprocessor must run before any code is compiled, and `using` declarations must come before any code that relies on them. Get used to typing this skeleton from memory; you will type it thousands of times.

---

## 2. Compiling and Running

In Lesson 01 you compiled with `g++ -std=c++17 -o hello hello.cpp`. Let's dissect that command and learn the variations you will need for real programs.

The simplest possible compile command is just `g++ hello.cpp`:

```bash
g++ hello.cpp
```

This runs the compiler with all default settings and produces an executable called **`a.out`** (Linux/Mac) or **`a.exe`** (Windows) — the name `a.out` is a historical artifact, short for "assembler output." Running `./a.out` works, but the default name is unhelpful, so we usually override it with `-o`:

```bash
g++ -std=c++17 -o hello hello.cpp
```

Here `-o hello` says "name the output executable `hello`" instead of `a.out`. The order of flags and the source filename does not matter much, but the conventional order is flags first, then source files. On Windows, you may want to add the `.exe` extension explicitly: `g++ -std=c++17 -o hello.exe hello.cpp`. Most Windows shells will run `hello` just as well as `hello.exe`, but being explicit avoids confusion.

Running the program is the next step:

```bash
./hello        # Linux or Mac
hello.exe      # Windows (PowerShell or cmd)
```

The `./` prefix on Linux/Mac tells the shell "run the file in the current directory" — without it, the shell searches your `PATH` for a command called `hello` and does not find it. On Windows, the current directory is implicitly on the PATH, so `hello` or `hello.exe` works without any prefix.

There is one important nuance: every time you change your source code, you must recompile before running. C++ does not run `.cpp` files directly — it runs the compiled binary. If you edit `hello.cpp`, save, and run `./hello` without recompiling, you will see the old output and be confused. Get into the habit of `g++ -std=c++17 -o hello hello.cpp && ./hello` (the `&&` runs the program only if the compile succeeded). For projects with multiple files, you will eventually want a build tool like `make` or `CMake` to automate recompilation, but for single-file examples, the manual command is fine.

---

## 3. Common Compiler Flags

The compiler accepts dozens of flags. You will only need a handful for this entire module, but they matter — turning on warnings and the right language standard catches bugs that would otherwise take hours to find. Here are the ones you should know:

```bash
g++ -std=c++17 -Wall -Wextra -g -O2 -o hello hello.cpp
```

- **`-std=c++17`** — Tells the compiler to use the **C++17 standard**. Without this, `g++` defaults to an older dialect (often `gnu++17` on modern GCC, which is fine, but older GCC defaulted to `c++14` or `c++11`). Use `-std=c++20` if you want newer features. This flag is what unlocks modern C++ syntax like `auto`, lambdas, range-based `for`, and structured bindings.
- **`-Wall`** — Turns on **most warnings**. The name is misleading; it does not turn on *all* warnings (despite the name), but it turns on the most common and useful ones: unused variables, sign mismatches, missing return statements, and so on. Always compile with `-Wall`.
- **`-Wextra`** — Turns on **extra warnings** that `-Wall` does not enable by default. Things like comparing signed and unsigned integers, or virtual function overrides that are not marked `override`. Combine with `-Wall` for the most common set.
- **`-g`** — Generates **debug information** embedded in the binary. This lets you use a debugger like `gdb` or `lldb` to step through your code line by line, inspect variables, and find crashes. The downside is a larger binary; turn it off for release builds.
- **`-O2`** — Turns on **optimization level 2** (out of 0, 1, 2, 3, and `s` for size). `-O0` (the default) is no optimization — fast to compile, slow to run. `-O2` is the standard "release" optimization: your code runs 2–10× faster but takes longer to compile. `-O3` is more aggressive; `-Os` optimizes for binary size. For everyday development, use `-O0 -g` (debugging); for shipping code, use `-O2` or `-O3`.
- **`-o hello`** — Names the output file `hello`. Without `-o`, you get `a.out`.

A typical debug command is therefore `g++ -std=c++17 -Wall -Wextra -g -o hello hello.cpp` (no optimization, full debug info). A typical release command is `g++ -std=c++17 -Wall -Wextra -O2 -o hello hello.cpp` (optimized, no debug info). Throughout this module we will keep the commands simple — usually just `-std=c++17 -o hello hello.cpp` — but you should turn on `-Wall -Wextra` whenever you want the compiler to help you find bugs. The compiler is your friend; let it speak.

There are many more flags (`-Werror` turns warnings into errors, `-pedantic` rejects non-standard extensions, `-I` adds include directories, `-L` and `-l` add library paths and libraries). We will introduce them as needed in later lessons.

---

## 4. I/O Streams

C++'s input/output system is built around **streams** — abstract sequences of characters that you can read from or write to. The four standard streams you need to know are:

```cpp
#include <iostream>

int main() {
    std::cout << "Normal output goes to stdout" << std::endl;
    std::cerr << "Error output goes to stderr" << std::endl;
    int x;
    std::cin >> x;          // reads an integer from stdin
    std::cout << "You typed: " << x << std::endl;
    return 0;
}
```

- **`std::cout`** — "character output." The standard output stream, connected to your terminal by default. This is where `std::cout << "..."` writes text. It is **buffered** — the program does not actually write each character immediately; it accumulates output in a buffer and flushes it periodically. Buffering makes output fast.
- **`std::cerr`** — "character error." The standard error stream, also connected to your terminal by default but **unbuffered** — every character is written immediately. Use `std::cerr` for error messages and diagnostics so they appear even if the program crashes before flushing `std::cout`.
- **`std::cin`** — "character input." The standard input stream, connected to your keyboard by default. Use `>>` (the stream extraction operator) to read typed values: `std::cin >> x` reads an `int`, `std::cin >> name` reads a `std::string` (up to the next whitespace).
- **`std::clog`** — "character log." A buffered version of `std::cerr`, used for log messages. Less common in beginner code.

The `<<` operator (stream insertion) and `>>` operator (stream extraction) are overloaded to work with many types — `int`, `double`, `char`, `std::string`, C-style strings, and even custom types you define later. You can chain them: `std::cout << "x = " << x << ", y = " << y << std::endl;`.

One subtle point: `std::endl` vs `'\n'`. Both write a newline, but `std::endl` also **flushes the buffer** — it forces the stream to actually emit any buffered text to the terminal immediately. `'\n'` just writes the newline character and lets the buffer flush naturally later. Flushing is slow (it requires a system call), so for high-volume output (printing thousands of lines), prefer `'\n'`. For interactive programs where you want each line to appear immediately, `std::endl` is safer. In practice, the difference rarely matters, and either is fine for this module. Many modern style guides recommend `'\n'` for performance and reserving `std::endl` for cases where you specifically need the flush.

Reading input with `std::cin >> x` skips leading whitespace, reads until it hits a character that does not fit the type (like a non-digit when reading an `int`), and leaves the rest of the input in the buffer. If the user types `42 hello` and you do `std::cin >> x >> name`, `x` becomes `42` and `name` becomes `"hello"`. If the user types something invalid (like `abc` when you asked for an `int`), `std::cin` enters a fail state and stops reading — you have to call `std::cin.clear()` and `std::cin.ignore()` to recover. We will cover error handling in later lessons; for now, assume the user types valid input.

---

## 5. Namespaces

Namespaces are C++'s way of grouping names and preventing collisions. The Standard Library puts everything inside a namespace called `std`, which is why you write `std::cout` rather than just `cout`. Without namespaces, the standard library's `count`, `find`, `sort`, `string`, `vector`, and hundreds of other common names would clash with your own `count` function, your own `string` class, or names from third-party libraries. Namespaces solve this by adding a prefix.

There are three ways to deal with the `std::` prefix, in order from safest to riskiest:

```cpp
// 1. SAFEST — always use the std:: prefix
#include <iostream>

int main() {
    std::cout << "Hello" << std::endl;
    std::string name = "Alice";
    return 0;
}
```

```cpp
// 2. MEDIUM — using declaration: bring in specific names
#include <iostream>
#include <string>

using std::cout;
using std::endl;
using std::string;

int main() {
    cout << "Hello" << endl;          // no std:: needed
    string name = "Alice";
    return 0;
}
```

```cpp
// 3. RISKIEST — using directive: bring in everything
#include <iostream>
#include <string>

using namespace std;

int main() {
    cout << "Hello" << endl;
    string name = "Alice";
    return 0;
}
```

The first form — explicit `std::` everywhere — is the safest and what most professional C++ codebases use. It is a few characters longer, but it makes every line self-documenting: a reader can see at a glance whether a name comes from the standard library or from your own code. The second form (`using std::cout;`) brings in specific names, which is a reasonable compromise if you use one or two names many times. The third form (`using namespace std;`) brings in everything from `std` at once, which is convenient but pollutes the global namespace with hundreds of names. It is acceptable in small `.cpp` files at function scope (inside a function body), but **never put `using namespace std;` in a header file** — every file that includes your header inherits the pollution, often without knowing it, and name collisions become very hard to debug.

Throughout this module we use the explicit `std::` prefix. It is more typing, but it is the most professional and portable style. If you see `using namespace std;` in tutorials online, that is fine for quick examples — just be aware of the rule for headers.

---

## 6. Comments

Comments let you leave notes in your code that the compiler ignores. C++ inherits C's two comment styles and adds one of its own:

```cpp
#include <iostream>

// This is a single-line comment. It starts with // and goes to end of line.

/*
   This is a multi-line (block) comment. It starts with slash-star
   and ends with star-slash. It can span multiple lines.
   Be careful: block comments do NOT nest, so you cannot put one
   block comment inside another.
*/

int main() {
    int x = 5;        // inline comment after code
    /* int y = 10; */ // commented-out code (sometimes useful for debugging)
    std::cout << x << std::endl;
    return 0;
}
```

- **`//`** — single-line comment. Everything from `//` to the end of the line is ignored by the compiler. This is the most common comment style in modern C++. Use it for short notes about a single line or block of code.
- **`/* ... */`** — multi-line (block) comment. Everything between `/*` and `*/` is ignored, even across multiple lines. Useful for long explanations, copyright headers, or temporarily disabling a chunk of code. **Block comments do not nest** — `/* /* */ */` is a syntax error because the first `*/` closes the comment, leaving `*/` dangling. This matters when you try to "comment out" a block of code that already contains a block comment.
- **`///` or `//!`** — documentation comments (Doxygen-style). These are not part of the language — they are conventions used by the Doxygen documentation generator. We will not use them in this module.

A note on comment style: modern C++ practice is to write **self-documenting code** — use descriptive variable names, small focused functions, and clear types — so that comments are needed only for the *why*, not the *what*. A comment that says `// increment i` next to `i++;` adds nothing. A comment that says `// retry up to 3 times because the API returns spurious 503s` adds real value. We will lean toward few comments in this module, since the surrounding text already explains each line.

---

## 7. Multiple Files

Real C++ programs are split across multiple files. The reason is the same as in Java or TypeScript: large files are hard to navigate, and you want to reuse code (functions, classes) across multiple programs without copy-pasting. C++ uses a two-file convention that is unusual among modern languages: every logical unit of code lives in **two** files — a `.cpp` file (the implementation) and a `.h` file (the header, which declares the names).

Here is the smallest possible multi-file program. Create three files in the same folder:

`math_utils.h`:

```cpp
// Declares the function so other files can call it.
int add(int a, int b);
```

`math_utils.cpp`:

```cpp
// Defines the function (the actual body).
#include "math_utils.h"

int add(int a, int b) {
    return a + b;
}
```

`main.cpp`:

```cpp
#include <iostream>
#include "math_utils.h"   // brings in the declaration of add()

int main() {
    std::cout << "2 + 3 = " << add(2, 3) << std::endl;
    return 0;
}
```

Compile both `.cpp` files together (the `.h` file is not compiled directly — it is textually included by the `#include` directive):

```bash
g++ -std=c++17 -o main main.cpp math_utils.cpp
./main
```

You should see `2 + 3 = 5` printed. The header file `math_utils.h` **declares** the function (tells the compiler "this function exists, here is its signature"), and the `math_utils.cpp` file **defines** it (contains the actual body). The `main.cpp` file `#include`s the header so it knows `add` exists, but the actual code for `add` lives in `math_utils.cpp` and gets linked in at the final linking step.

The convention is: angle brackets `<iostream>` for system headers, double quotes `"math_utils.h"` for your own headers. The double-quote form tells the preprocessor to look in the current folder first, then fall back to system include paths. The angle-bracket form skips the current folder. We will use this convention throughout the rest of the module, and Lesson 05 will go deeper into declarations vs definitions.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Read and Echo

Write a program that asks the user for their name, reads the name with `std::cin >> name`, and prints a personalized greeting. Use `std::string` from `<string>`. Sample interaction:

```text
What is your name? Alice
Hello, Alice!
```

Hint: you need `#include <iostream>` and `#include <string>`. Declare `std::string name;` and then `std::cin >> name;`. Notice that `std::cin >> name` only reads up to the next whitespace — if the user types "Ada Lovelace", only "Ada" gets stored. (We will cover reading whole lines with `std::getline` in Lesson 03.)

### Exercise 2: Add Two Numbers

Write a program that asks for two integers, reads them, and prints their sum. Sample interaction:

```text
First number: 5
Second number: 7
5 + 7 = 12
```

Compile with the recommended flags `g++ -std=c++17 -Wall -Wextra -o adder adder.cpp`. If you see any warnings, fix them. (Common warning: "unused variable" — make sure you actually use every variable you declare.)

### Exercise 3: Split Into Two Files

Take your solution to Exercise 2 and split it into three files: `add.h` (declaration of `int add(int, int);`), `add.cpp` (definition), and `main.cpp` (the I/O code, which `#include`s `"add.h"`). Compile with `g++ -std=c++17 -o adder main.cpp add.cpp`. Confirm that running `./adder` produces the same output. This is the standard structure for every multi-file C++ program you will ever write, so it is worth getting comfortable with now.

---

## Common Mistakes

### Mistake 1: Forgetting `#include <iostream>` (or the right header)

```cpp
// WRONG — uses std::cout without including <iostream>
int main() {
    std::cout << "Hello" << std::endl;
    return 0;
}
```

```cpp
// RIGHT — include the header that declares std::cout
#include <iostream>

int main() {
    std::cout << "Hello" << std::endl;
    return 0;
}
```

Every standard library feature lives in a specific header. If you forget the `#include`, you get an error like `'cout' was not declared in this scope`. Common mappings: `<iostream>` for `cin`/`cout`/`cerr`, `<string>` for `std::string`, `<vector>` for `std::vector`, `<map>` for `std::map`, `<cmath>` for `std::sqrt` and friends, `<algorithm>` for `std::sort` and `std::find`. The fix is to look up the header on [cppreference.com](https://en.cppreference.com/) and add it at the top of your file. The order of includes does not usually matter, but grouping system headers together and your own headers below them is conventional.

### Mistake 2: `using namespace std;` in a header file

```cpp
// WRONG — header file mylib.h
#include <iostream>
using namespace std;  // pollutes EVERY file that includes this header

void do_something();
```

```cpp
// RIGHT — header file mylib.h, fully qualified
#include <iostream>

void do_something();
// (or use using std::cout; — but usually no using declarations in headers)
```

If you put `using namespace std;` in a header file, every `.cpp` file that `#include`s your header inherits the directive — and there is no way to undo it. Suddenly every file in your project has hundreds of standard library names in the global namespace, and any local `count`, `find`, `sort`, `string`, or `vector` you define may silently clash with the standard library's. The bugs this causes are miserable to track down. The rule is iron-clad: **never put `using namespace std;` (or any `using namespace` directive) in a header file**. Inside `.cpp` files it is acceptable, especially at function scope, but the safest habit is to write `std::` everywhere.

### Mistake 3: Mixing C and C++ I/O

```cpp
// WRONG — printf and cout can produce out-of-order output
#include <cstdio>
#include <iostream>

int main() {
    std::cout << "Line 1 from cout\n";
    printf("Line 2 from printf\n");
    std::cout << "Line 3 from cout\n";
    return 0;
}
```

```cpp
// RIGHT — call sync_with_stdio(false) OR use one style consistently
#include <cstdio>
#include <iostream>

int main() {
    std::ios::sync_with_stdio(false);  // opt out of C/C++ sync (fast but order-undefined)
    std::cout << "Line 1 from cout\n";
    printf("Line 2 from printf\n");
    std::cout << "Line 3 from cout\n";
    return 0;
}
// BEST — just pick one style and stick with it for the whole program
```

C++'s `<cstdio>` (the C standard I/O library, with `printf`, `scanf`, `fopen`) and C++'s `<iostream>` (`std::cout`, `std::cin`) are two separate I/O systems. By default they are **synchronized** so output appears in the right order, but synchronization is slow. Calling `std::ios::sync_with_stdio(false);` at the start of `main` turns off synchronization for speed, but then `printf` and `std::cout` no longer have a defined order — your output may come out interleaved or out of order. The simplest fix is to pick one style and stick with it. We use `<iostream>` throughout this module because it is type-safe and works with custom types, but `printf` is still popular in low-level code for its precise format control.

### Mistake 4: Forgetting `-std=c++17` for modern features

```bash
# WRONG — uses the compiler's default, which may be C++11 or older
g++ -o prog prog.cpp
# prog.cpp uses: auto x = 5;  (C++11), std::optional (C++17), lambdas (C++11)
# Result: error: 'optional' is not a member of 'std'
```

```bash
# RIGHT — explicitly request C++17 or newer
g++ -std=c++17 -o prog prog.cpp
```

Different compilers default to different C++ standard versions. GCC 11+ defaults to `gnu++17`, but older GCC defaulted to `c++14` or `c++11`. If you use a modern feature (like `std::optional` from C++17, or `std::format` from C++20) and the compiler rejects it with `'optional' is not a member of 'std'`, you probably forgot the `-std` flag. The fix is to add `-std=c++17` (or `-std=c++20` if you need newer features) to every compile command. For projects that get bigger, you will eventually use `CMake` or `make` to manage these flags automatically — but for now, just type them on the command line.

---

## Summary

- Every C++ program has the same skeleton: preprocessor directives (`#include`), optional `using` declarations, and function/class definitions (including `main`).
- Compile with `g++ -std=c++17 -o hello hello.cpp` to produce an executable; run it with `./hello` (Linux/Mac) or `hello.exe` (Windows). Recompile after every change.
- The most important compiler flags are `-std=c++17` (language standard), `-Wall -Wextra` (warnings), `-g` (debug info), and `-O2` (optimization).
- C++ I/O uses **streams**: `std::cout` (output, buffered), `std::cerr` (error, unbuffered), `std::cin` (input). The `<<` operator inserts into output streams; `>>` extracts from input streams.
- `std::endl` writes a newline **and flushes the buffer**; `'\n'` writes a newline without flushing. Use `'\n'` for performance, `std::endl` for immediate visibility.
- Namespaces group names to prevent collisions. Everything in the Standard Library lives in `std`. Prefer the explicit `std::` prefix; `using namespace std;` is acceptable in small `.cpp` files but **never in headers**.
- Comments come in two flavors: `//` single-line and `/* ... */` multi-line. Block comments do not nest.
- Multi-file programs split each unit into a `.h` header (declaration) and a `.cpp` file (definition). Compile all `.cpp` files together; `#include` the headers you need.

You now understand the anatomy of a C++ program and how to compile and run multi-file projects. In Lesson 03 we move on to variables and types — the primitive types, declaration forms, `auto`, constants, strings, and arrays that you will use in every C++ program.

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
