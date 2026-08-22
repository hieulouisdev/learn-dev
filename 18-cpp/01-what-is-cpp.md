# Lesson 01: What is C++?

> Module: C++ · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first C++ lesson. In the next 30 minutes, you will learn what C++ is, where it came from, how it relates to its ancestor C, how to install a C++ compiler on your computer, and how to write, compile, and run your first C++ program. By the end, you will have typed `std::cout << "Hello, World!"` into a real C++ source file and watched the compiler turn it directly into a native executable that runs on your machine.

You do not need any prior systems-programming experience for this lesson. If you completed the Python, Rust, Go, TypeScript, or Java modules, you already understand variables, functions, and control flow — and that is more than enough. C++ feels familiar because it uses C-style syntax (curly braces, semicolons, the same `if`/`for`/`while` keywords you saw in Java and Go), but it is stricter about memory and far more powerful than Java or Go. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what C++ is, where it came from, and why Bjarne Stroustrup created it.
2. Describe how C++ relates to C — what they share, what C++ adds, and why "C++ is mostly a superset of C."
3. Install a C++ compiler (`g++` on Linux, `clang++` on Mac, MinGW or MSVC on Windows) and verify it from the terminal.
4. Write your first C++ program (`hello.cpp`) and explain what `#include <iostream>`, `std::cout`, `<<`, and `std::endl` each do.

---

## 1. What is C++, Really?

C++ is a **general-purpose, statically-typed, compiled programming language** that supports procedural, object-oriented, generic, and functional programming — often within the same file. "General-purpose" means C++ is not tied to one domain: it powers operating systems, game engines, browsers, databases, embedded devices, financial trading systems, and AI frameworks. "Statically-typed" means every variable has a type known to the compiler at compile time, like Rust, Go, Java, and TypeScript — type errors are caught before your program runs. "Compiled" means C++ source code is translated directly into native machine code ahead of time, producing a stand-alone executable that runs without an interpreter or a virtual machine. Unlike Java (which compiles to bytecode for the JVM) and Python (which is interpreted at runtime), C++ produces a binary that runs directly on the CPU.

The language was started by **Bjarne Stroustrup** in **1979** at **Bell Labs** in Murray Hill, New Jersey — the same legendary research lab that gave the world C, UNIX, C++, and a great deal of modern computing. Stroustrup had just completed his **PhD on Simula** at Cambridge University in the UK. Simula, designed in Norway in the 1960s, is widely credited as the first object-oriented programming language — it introduced classes, inheritance, and virtual functions decades before Java or C++ existed. Stroustrup loved Simula's class model and the way it let him model real-world entities as objects. But Simula was painfully slow: programs that should have run in seconds took minutes, because Simula's runtime did a great deal of dynamic type checking and memory management behind the scenes.

At Bell Labs, Stroustrup needed to write distributed-system simulations that ran fast, and Simula's performance was a non-starter. He tried writing them in **C**, but C had no classes, no inheritance, and no way to organize code into objects — it was a low-level procedural language designed to write the UNIX operating system. Stroustrup's solution was to add Simula's class model on top of C without sacrificing C's speed. He started the project in 1979 and called it **"C with Classes."** The first version ran internally at Bell Labs in **1980**, and Stroustrup spent the next few years adding features: virtual functions (1983), operator overloading, references, and templates came later. He built the first compiler, called **CFront**, which translated C++ source into C source and then compiled the C with a regular C compiler — a clever bootstrap that let C++ reuse C's existing toolchains.

In **1983**, the language was **renamed "C++"** — a name suggested by **Rick Mascitti**, another Bell Labs engineer. The name is a programmer's pun: `++` is the C increment operator, so "C++" reads as "the successor to C" or "one step beyond C." The first edition of Stroustrup's book **"The C++ Programming Language"** was published in **1985** and became the de facto language reference for the next decade. By the early 1990s, C++ had become one of the most popular languages in industry, and in **1998** the **International Organization for Standardization (ISO)** published the first official C++ standard, known as **C++98**. Subsequent standards — **C++03** (bug-fix), **C++11** (massive modernization with lambdas, smart pointers, `auto`, move semantics), **C++14** (refinements), **C++17** (`std::optional`, structured bindings), **C++20** (concepts, ranges, modules), and **C++23** — have steadily modernized the language. This module targets **C++17 or newer**, which every modern compiler supports by default.

---

## 2. Relationship to C

C++ is **mostly a superset of C**. This is one of the most important facts about the language, and it shapes everything you will learn in this module. Most valid C programs compile without modification as C++ programs. If you write a C program using `printf` and `malloc`, save it as `.cpp`, and compile it with `g++`, it will usually work. C++ inherits C's primitive types (`int`, `char`, `float`, `double`), its operators, its syntax for `if`/`for`/`while`, its function-call conventions, its preprocessor (`#include`, `#define`), and most of its standard library (the C standard library is available in C++ as `<cstdio>`, `<cstdlib>`, `<cstring>`, etc., with a `c` prefix instead of a `.h` suffix).

But C++ is not just C with extra syntax. The languages have diverged in subtle ways over forty years, and a few valid C programs are not valid C++ — for example, C++ has stricter type checking, requires explicit casts in places C permits implicit ones, and reserves keywords like `class`, `new`, `delete`, `template`, and `namespace` that C does not have. Beyond mere syntax, C++ adds an enormous amount to C:

- **Classes and inheritance** — full object-oriented programming with constructors, destructors, virtual functions, and access control (`public`, `private`, `protected`).
- **Templates** — generic programming that lets you write code that works on any type, the way generics work in Java or Rust but far more powerful. The entire C++ Standard Template Library (STL) is built on templates.
- **Exceptions** — `try`/`catch`/`throw` for error handling, which C does not have (C uses return codes).
- **References** — safer aliases for variables that look like the variable itself, in addition to C's raw pointers.
- **Namespaces** — a way to group names and avoid collisions, like `std::` for the standard library. C has no namespaces.
- **Function overloading** — multiple functions can have the same name as long as their parameter types differ.
- **RAII (Resource Acquisition Is Initialization)** — the central C++ idiom that ties resource lifetimes (memory, files, locks, sockets) to object lifetimes. We cover RAII in Lesson 06.
- **The STL** — `std::vector`, `std::string`, `std::map`, `std::unordered_map`, `std::algorithm`, and the rest of the modern standard library that C simply does not have.

In practice, modern C++ code looks very different from C code — you will write `std::vector<int>` instead of C arrays, `std::string` instead of `char*`, `new`/`delete` (or, better, smart pointers) instead of `malloc`/`free`. But the option to drop down to C-level code is always there when you need the speed or the low-level control. That flexibility is both C++'s greatest strength and its greatest source of complexity.

---

## 3. Installing a Compiler

Before you can write C++, you must install a **C++ compiler**. Unlike Python (which ships with an interpreter) or Java (which ships with `javac` and the JVM in one JDK download), C++ does not have a single canonical toolchain. The two big open-source compilers are **GCC** (the GNU Compiler Collection, whose C++ compiler is `g++`) and **LLVM/Clang** (whose C++ compiler is `clang++`). Microsoft's **MSVC** is the third major compiler, available on Windows. All three are excellent and all three support C++17, C++20, and C++23 by default. Pick whichever is easiest to install on your platform.

### On Linux (Debian or Ubuntu)

Open a terminal and run:

```bash
sudo apt update
sudo apt install g++
```

If you also want `make`, `gdb` (the debugger), and the standard build tools, install the meta-package instead:

```bash
sudo apt install build-essential
```

On Fedora, run `sudo dnf install gcc-c++`. On Arch, run `sudo pacman -S gcc`. Linux distributions almost always ship a recent-enough GCC — version 10 or newer supports C++17 fully, version 12 or newer supports most of C++20.

### On Mac

Mac users get **clang++** for free with the Xcode Command Line Tools. Open a terminal and run:

```bash
xcode-select --install
```

A dialog pops up asking if you want to install the Command Line Tools. Click "Install" and wait a few minutes. You do not need the full Xcode IDE (which is several gigabytes) — the Command Line Tools are a much smaller download and include `clang++`, `make`, `git`, and the standard library. Verify the installation with `clang++ --version`. (You can also run `g++ --version` on Mac — Apple aliases `g++` to `clang++` so the two commands are interchangeable.)

### On Windows

Windows is the platform that requires the most setup. You have two good options:

**Option A — MinGW-w64 via MSYS2.** This is the path most similar to Linux. Go to [msys2.org](https://www.msys2.org/) and download the MSYS2 installer. Run it, then open the "MSYS2 UCRT64" terminal that MSYS2 installs and run:

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc
```

This installs a recent GCC. Add the MSYS2 `mingw64/bin` folder to your system PATH so that `g++` is available from any terminal (PowerShell, cmd, or VS Code's terminal). Verify with `g++ --version`.

**Option B — Visual Studio Community with the C++ workload.** This is Microsoft's officially-blessed path. Download **Visual Studio Community** (free for individuals and small teams) from [visualstudio.microsoft.com](https://visualstudio.microsoft.com/). During installation, check the box for **"Desktop development with C++"** — this installs Microsoft's MSVC compiler, the Windows SDK, and the Visual Studio IDE (which is excellent for C++ development). MSVC's command-line compiler is `cl.exe`, and you run it from the "x64 Native Tools Command Prompt for VS" that Visual Studio installs.

### Verifying the Installation

Open a fresh terminal and run:

```bash
g++ --version
# or, on Mac:
clang++ --version
```

You should see a version banner like `g++ (Ubuntu 13.2.0) 13.2.0` or `Apple clang version 15.0.0`. Any version from the last few years supports C++17 by default. If you see `command not found`, close every terminal window and open a new one so the PATH change takes effect. If that does not help, the install directory is not on your PATH — add it manually. On Windows, remember to use the "x64 Native Tools Command Prompt" if you installed MSVC, so that `cl.exe` is on the PATH.

---

## 4. Your First Program

It is finally time to write C++. Open a terminal, create a folder for your C++ experiments (`mkdir cpp-playground && cd cpp-playground`), and inside it create a file called `hello.cpp`. Type this exact code:

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

Save the file. Then compile it from the terminal:

```bash
g++ -std=c++17 -o hello hello.cpp
```

If the compiler is happy, it produces no output and exits silently. List the files in your folder with `ls` (Mac/Linux) or `dir` (Windows), and you will see a new executable called `hello` (or `hello.exe` on Windows). Run it:

```bash
./hello
# or on Windows:
hello.exe
```

You should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, compiled, and ran your first C++ program.** Now let's break the file into its parts, because every word in that four-line program matters and C++ is notoriously terse about what each piece does.

Here is the program again with each line explained:

```cpp
#include <iostream>
```

- `#` — the start of a **preprocessor directive**. The preprocessor runs before the actual compiler and manipulates your source text. Lines beginning with `#` are not C++ statements — they are instructions to the preprocessor.
- `include` — the directive that says "copy the contents of another file into this file at this point."
- `<iostream>` — the header file being included. Angle brackets `< >` tell the preprocessor to look in the system's standard include directory (rather than the current folder). `<iostream>` declares the input/output stream classes — `std::cout`, `std::cin`, `std::cerr` — that you need to print to the terminal.
- No semicolon at the end. Preprocessor directives do not end with `;`.

```cpp
int main() {
```

- `int` — the return type of the function. `main` returns an `int` to the operating system. `0` means success; any non-zero value means an error.
- `main` — the function name. The operating system calls this function when your program starts. Every C++ program must have exactly one `main` function.
- `()` — empty parameter list. The simpler of `main`'s two valid signatures. (The other is `int main(int argc, char* argv[])` if you need command-line arguments.)
- `{` — the opening brace that begins the function body.

```cpp
    std::cout << "Hello, World!" << std::endl;
```

- `std::` — the **namespace** prefix. `std` is the C++ Standard Library namespace; the `::` is the scope operator. Every standard library name (`cout`, `cin`, `string`, `vector`, ...) lives in `std::` unless you bring it in with a `using` statement (covered in Lesson 02).
- `cout` — short for "character output." `std::cout` is the standard output stream — the terminal, by default. It is an object of type `std::ostream`.
- `<<` — the **stream insertion operator**. It takes the value on its right and "inserts" it into the stream on its left. You can chain `<<` to print multiple things: `std::cout << "x = " << x << std::endl;`.
- `"Hello, World!"` — a string literal. C++ string literals use double quotes.
- `std::endl` — "end line." It writes a newline character to the stream and **flushes** the stream's buffer (forces the text to actually appear on the terminal). A faster alternative is the character literal `'\n'`, which writes a newline without flushing.
- `;` — every C++ statement ends with a semicolon. The compiler will refuse to compile without it.

```cpp
    return 0;
}
```

- `return 0;` — returns `0` from `main` to the operating system. `0` conventionally means "the program ran successfully." Non-zero values signal errors.
- `}` — the closing brace that ends the function body. (Note: modern C++ allows `main` to omit the `return 0;` — if `main` reaches its closing brace without a return, the compiler inserts `return 0;` for you. But typing it explicitly is good style and clearer to readers coming from C.)

That is the entire program. Four lines, four concepts: the preprocessor (`#include`), the entry point (`int main`), I/O streams (`std::cout <<`), and the return code (`return 0`). You will see this exact skeleton at the top of nearly every C++ program you write for the rest of your career.

---

## 5. The Compilation Process

When you ran `g++ -std=c++17 -o hello hello.cpp`, you told `g++` to do several things in sequence. C++'s compilation pipeline is older and more layered than Java's or Rust's, and understanding the steps will help you read error messages later. Here is the full pipeline as a diagram:

```text
              preprocessor            compiler            assembler            linker
hello.cpp  ────────────────▶  hello.ii  ────────────▶  hello.s  ──────────▶  hello.o  ────────────▶  hello
 (source)     (#include,         (C++ source        (assembly,         (object file,         (executable,
                #define)            expanded)          human-readable)     binary, no linking)    ready to run)
```

Here is what happens at each step:

1. **Preprocessing.** The preprocessor reads your `.cpp` file and handles every line beginning with `#`. It copies the contents of `<iostream>` (and any other headers you `#include`) into your source text, expands any `#define` macros, and strips out comments. The output is a single text file (conventionally `.ii` for "C++ intermediate") containing all the C++ code that will actually be compiled.

2. **Compilation.** The compiler reads the preprocessed text and translates the C++ into an intermediate representation, performs type checking, and then generates assembly language for your target CPU. The output is a `.s` file containing human-readable assembly (if you have ever seen assembly, it looks like `mov`, `add`, `call`, `ret`).

3. **Assembly.** The assembler translates the assembly into machine code (binary) for your specific CPU. The output is an **object file** (`.o` on Linux/Mac, `.obj` on Windows). The object file contains compiled code but is not yet runnable — it has unresolved references to functions in other files (like `std::cout`'s implementation in the standard library).

4. **Linking.** The linker takes one or more object files and combines them with the standard library to produce the final **executable**. The linker resolves cross-references: it finds where `std::cout`'s code actually lives (in `libstdc++` on Linux, in `libc++` on Mac) and patches the calls in your object file to point at the right addresses. The output is your `hello` binary, ready to run.

In practice, you do not invoke these steps separately — `g++` runs them all for you in sequence. But you can ask `g++` to stop at any step with flags like `-E` (stop after preprocessing), `-S` (stop after compilation, output `.s`), or `-c` (stop after assembly, output `.o`). Knowing these flags helps when you are debugging weird include errors or splitting a project across multiple files. We will use `-c` in Lesson 02 when we cover multi-file programs.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `g++ --version` (or `clang++ --version` on Mac). Write down the version number. If `g++` reports anything below version 8, you need to install a newer version — C++17 requires at least GCC 7 or Clang 5. Then run `g++ -std=c++17 -x c++ -E - < /dev/null > /dev/null && echo "C++17 supported"` — this asks the compiler to preprocess an empty C++ input with C++17 enabled and prints `C++17 supported` if your compiler accepts the flag. If both commands work, your C++ installation is ready for the rest of the module.

### Exercise 2: Print Something Different

Open your `hello.cpp` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning C++."`. Save the file, recompile with `g++ -std=c++17 -o hello hello.cpp`, and run with `./hello`. Confirm your custom message appears. Notice that you must recompile after every change — C++ does not run `.cpp` files directly; it runs the compiled executable. If you forget to recompile, you will see the old output.

### Exercise 3: Print Multiple Lines

Modify your program so it prints three lines instead of one. Use multiple `std::cout` statements, and use `std::endl` after each one. For example, print:

```text
Hello, World!
I am learning C++.
This is fun!
```

Recompile and run. Notice that you can chain `<<` operations on a single `std::cout` line — `std::cout << "a" << std::endl << "b" << std::endl;` is equivalent to three separate statements. Try both forms and confirm they produce the same output.

---

## Common Mistakes

### Mistake 1: Forgetting `#include <iostream>`

```cpp
// WRONG — uses std::cout without including <iostream>
int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

```cpp
// RIGHT — include the header that declares std::cout
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

If you forget the `#include <iostream>` line at the top, the compiler does not know what `std::cout` is and you get an error like `error: 'cout' was not declared in this scope` or `'cout' is not a member of 'std'`. Headers in C++ declare the names you use; without the right `#include`, those names simply do not exist. Every standard library feature has a specific header — `<iostream>` for `cout`/`cin`, `<string>` for `std::string`, `<vector>` for `std::vector`, `<cmath>` for math functions. The fix is to look up the header for the type you are using and add it at the top of your file.

### Mistake 2: Using `cout` instead of `std::cout` (without `using namespace std`)

```cpp
// WRONG — cout is not in the global namespace
#include <iostream>

int main() {
    cout << "Hello, World!" << endl;
    return 0;
}
```

```cpp
// RIGHT — use the std:: prefix (preferred)
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

Everything in the C++ Standard Library lives in the `std` namespace. If you write `cout` without the `std::` prefix and without a `using namespace std;` directive at the top, the compiler does not find it and reports `error: 'cout' was not declared in this scope`. The fix is either to add the `std::` prefix (preferred, especially in headers) or to add `using namespace std;` below your includes (acceptable in small `.cpp` files but bad practice in headers, as we will see in Lesson 02).

### Mistake 3: Saving as `.c` instead of `.cpp`

```bash
# WRONG — saving as .c makes g++ compile it as C, not C++
g++ -o hello hello.c
# hello.c contains: std::cout << "Hello, World!" << std::endl;
# Compiler error: 'cout' was not declared in this scope (C has no cout)
```

```bash
# RIGHT — save C++ source in a .cpp file
g++ -std=c++17 -o hello hello.cpp
```

The file extension tells `g++` (and `clang++`) which language rules to apply. A `.c` file is compiled as **C**, not C++ — and C does not have `std::cout`, classes, references, or any of the C++ additions. If you accidentally save your C++ code as `.c`, you will get a flood of errors like `'cout' was not declared in this scope` and `'std' has not been declared`. The fix is simple: rename the file to `.cpp` and recompile. C++ uses `.cpp`, `.cc`, `.cxx`, and `.C` (capital C) as conventional extensions; `.cpp` is the most widely used and what we use throughout this module.

### Mistake 4: Forgetting `return 0;` (understanding the rule)

```cpp
// WORKS in C++ but older C compilers may complain
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
}
```

```cpp
// RIGHT — explicit return 0 is clear and portable
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

Modern C++ (since C++98) has a special rule for `main` only: if `main` reaches its closing brace without a `return`, the compiler inserts `return 0;` for you. So the program on the left compiles and runs correctly under every modern C++ compiler. However, this implicit-return rule applies **only to `main`** — every other function that returns `int` must explicitly return a value, or your program has undefined behavior. Many teachers and style guides recommend writing `return 0;` explicitly in `main` for clarity and portability with very old C compilers that did not implement the rule. We follow that convention throughout this module.

---

## Summary

- C++ is a general-purpose, statically-typed, compiled programming language that supports procedural, object-oriented, generic, and functional programming — often within the same file.
- It was created by **Bjarne Stroustrup** at Bell Labs starting in **1979**, originally called **"C with Classes"**, renamed **"C++"** in **1983** by Rick Mascitti (the `++` is the C increment operator, signaling "one step beyond C").
- The first ISO standard was **C++98**; major modernizations came in **C++11** (lambdas, smart pointers, `auto`, move semantics), **C++14**, **C++17** (`std::optional`, structured bindings), **C++20** (concepts, ranges, modules), and **C++23**. This module targets **C++17 or newer**.
- C++ is **mostly a superset of C**: most valid C programs compile as C++, and C++ inherits C's primitive types, operators, syntax, preprocessor, and the C standard library.
- C++ adds to C: **classes, templates, exceptions, references, namespaces, function overloading, RAII, and the Standard Template Library (STL)**.
- Install a compiler: `sudo apt install g++` on Ubuntu, `sudo dnf install gcc-c++` on Fedora, `xcode-select --install` on Mac (gets you `clang++`), or MinGW-w64 via MSYS2 / Visual Studio Community on Windows.
- Your first program is `#include <iostream>` + `int main() { std::cout << "Hello, World!" << std::endl; return 0; }`, saved as `hello.cpp`.
- Compile with `g++ -std=c++17 -o hello hello.cpp`, run with `./hello`. The compilation pipeline has four stages — preprocessing, compilation, assembly, and linking — that `g++` runs for you in sequence.

You wrote, compiled, and ran your first C++ program. The hardest step — getting the toolchain working — is done. In Lesson 02 we go deeper into the anatomy of a C++ program: namespaces, I/O streams, compiler flags, and how to split a program across multiple files.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
