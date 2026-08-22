# Lesson 01: What is Go?

> Module: Go · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first Go lesson. In the next 30 minutes, you will learn what Go is, where it came from, how to install it on your computer, and how to write and run your first Go program. By the end, you will have typed `fmt.Println("Hello, World!")` into a real Go source file and watched the compiler turn it into a running binary.

You do not need any prior systems-programming experience for this lesson. If you completed the Python or Rust modules, you already understand variables, functions, and control flow — and that is more than enough. Go feels familiar because it uses C-style syntax, but it removes the complexity that makes C and C++ painful. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Go is, where it came from, and why it was created.
2. Install Go 1.21 or newer on Windows, Mac, or Linux and verify it from the terminal.
3. Distinguish between a Go module (`go.mod`) and a Go package (a folder of `.go` files).
4. Write your first Go program (`fmt.Println("Hello, World!")`) and understand what each part of the file does, including the capital `P` in `Println`.

---

## 1. What is Go, Really?

Go (often called **Golang**, from its old domain `golang.org`) is a **statically-typed, compiled, garbage-collected programming language** developed at Google. "Statically-typed" means every variable has a type known to the compiler at compile time, the same as Rust and Java — type errors are caught before your program runs. "Compiled" means Go source code is translated directly into a single native binary ahead of time, with no runtime interpreter. "Garbage-collected" means Go automatically reclaims memory that is no longer used, so you never call `free()` manually the way you do in C — unlike Rust, which uses compile-time ownership rules instead of a garbage collector.

The language was started in **2007** by three engineers at Google: **Robert Griesemer**, **Rob Pike**, and **Ken Thompson**. The story goes that Pike and Thompson were waiting for a long C++ build to finish at Google and started sketching, on a whiteboard, what a simpler language would look like. The names matter because all three had already shaped computing history. **Ken Thompson co-created the Unix operating system** and designed the B programming language, which directly inspired C. **Rob Pike co-created the UTF-8 character encoding** that every modern web page uses. **Robert Griesemer** worked on Google's V8 JavaScript engine and the Java HotSpot compiler. When three engineers of that caliber say "we are designing a new language," the programming world listens.

Why did they design Go? Out of pure frustration with the state of programming at Google scale. In 2007, Google's codebases were built primarily in **C++ and Java**. C++ builds at Google could take 30 minutes or more, and the language had grown so feature-rich that even senior engineers struggled to read each other's code. Java was verbose — a simple HTTP handler could require dozens of lines of boilerplate plus XML configuration files. The three founders wanted a language that was **simple to read, fast to compile, fast to run, and easy to learn** for new engineers who had just joined Google. They deliberately rejected features like inheritance, exceptions, and (initially) generics, because every feature added is a feature every new engineer has to learn.

Go was **publicly announced in November 2009** and reached **stable 1.0 in March 2012**. Since then, Go has promised **backward compatibility** — code written for 1.0 still compiles on Go 1.21 today. The Go gopher mascot, drawn by illustrator **Renée French**, is one of the most recognizable logos in programming. The community refers to itself as **Gophers** — friendly, enthusiastic, and famously welcoming to beginners. If you see a cartoon gopher on a T-shirt at a tech conference, that is the Go mascot.

---

## 2. Installing Go

Before you can write Go, you must install the Go toolchain — the compiler, the standard library, and the build tools. Unlike Rust (which uses `rustup`) or Python (which uses installers from python.org), Go ships as a single archive that you download from **[go.dev/dl](https://go.dev/dl/)**. The website auto-detects your operating system and offers the correct installer. As of this writing, the latest stable version is Go 1.22; any version 1.21 or newer will work for this entire module.

### On Windows

Download the MSI installer (something like `go1.21.x.windows-amd64.msi`) and double-click it. The installer places Go at `C:\Program Files\Go` and **automatically adds Go to your PATH** — you do not need to tick any checkbox. When installation finishes, close any open terminal and open a new one so the PATH change takes effect. Verify with `go version` (see below).

### On Mac

Download the PKG installer (something like `go1.21.x.darwin-amd64.pkg`) and run it. Follow the prompts — the defaults are correct. The installer places Go at `/usr/local/go/bin` and adds it to your PATH. If you use **Homebrew**, you can also run `brew install go` to get the same result with one command. After installation, open a fresh Terminal window so the new command is visible.

### On Linux

Download the `.tar.gz` archive (something like `go1.21.x.linux-amd64.tar.gz`). Extract it to `/usr/local` with this command (you will be asked for your password):

```bash
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.21.x.linux-amd64.tar.gz
```

Then add `/usr/local/go/bin` to your PATH by editing your `~/.profile` or `~/.bashrc` and adding the line `export PATH=$PATH:/usr/local/go/bin`. Save the file, then either run `source ~/.bashrc` or open a fresh terminal. Some distributions also package Go in their repositories (`sudo apt install golang` on Debian/Ubuntu), but the official archive is more up-to-date and is the recommended method.

### Verifying the Installation

Open a fresh terminal and type this single command. You should see a version banner.

```bash
go version
```

If you see something like `go version go1.21.5 linux/amd64`, congratulations — Go is installed and ready. If you see `command not found`, your shell did not pick up the PATH change. Close every terminal window and open a new one. If that does not help, the install directory (`/usr/local/go/bin` on Linux, `C:\Program Files\Go\bin` on Windows) is not on your PATH — add it manually.

A quick word on **`GOPATH`** (legacy) versus **Go modules** (modern). Before Go 1.11 (released in 2018), all Go code lived inside a single directory called `$GOPATH/src`, and every project had to be checked out at a path that matched its import URL. It was confusing. Modern Go (1.16+) uses **modules** instead: each project has its own `go.mod` file and can live anywhere on your disk. You will create your first module in Section 4 below. You may see old tutorials mention `$GOPATH` — ignore them; modules are the standard now.

---

## 3. Your First Program

It is finally time to write Go. Open a terminal, create a folder for your Go experiments, and inside it run a single command that initializes a Go module. A **module** is the unit of distribution in modern Go — it is a folder that contains a `go.mod` file at its root, plus one or more packages (subfolders) of Go source files. Modules let you track dependencies, version your code, and build everything in one place.

```bash
mkdir hello
cd hello
go mod init example.com/hello
```

The `go mod init` command creates a `go.mod` file in the current folder. The argument `example.com/hello` is the **module path** — a unique name for your module. For real projects this would be a URL like `github.com/yourname/project`, but for local experiments `example.com/hello` is fine. The `go.mod` file itself is short — it declares the module path and the Go version. You will look inside it in Lesson 02.

Now create a file called `main.go` in your editor and type this exact code:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Save the file. Then run it with a single command:

```bash
go run .
```

You should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, compiled, and ran your first Go program.** Let's break this file into its parts, because every line matters and Go syntax has a few conventions that surprise beginners.

- `package main` — the **package declaration**. Every Go file must start with a `package` line. `package main` is special: it tells the Go toolchain that this folder contains an **executable program** (not a library). When you run `go build`, the toolchain looks for a `package main` with a `func main()` inside it and turns that into a binary. Any other package name (like `package utils`) compiles to a library that other code can import but cannot run on its own.
- `import "fmt"` — the **import statement**. `"fmt"` (pronounced "fumpt", short for "format") is the name of a standard-library package that handles formatted input and output. To use `fmt.Println`, you must import `fmt` first. Go enforces this strictly — unused imports are a **compile error**, not a warning, which surprises developers coming from Python or JavaScript.
- `func main()` — the **function declaration**. `func` is the keyword that declares a function (Go's equivalent of Python's `def` or Rust's `fn`). `main` is a special name: the runtime calls `main()` automatically when the program starts. The empty parentheses `()` mean this function takes no arguments.
- `{` and `}` — the **braces** that contain the function body. Unlike Python (which uses indentation) Go uses braces, like C, Java, and Rust. Indentation in Go is just for readability — the compiler ignores it. The standard is tabs, not spaces, and `gofmt` enforces this automatically.
- `fmt.Println("Hello, World!")` — the **statement** that does the actual work. It calls the `Println` function from the `fmt` package and passes it the string `"Hello, World!"`. The capital `P` in `Println` is significant — Go uses **capitalization to control visibility**. A name starting with a capital letter is **exported** (public, accessible from outside the package). A name starting with a lowercase letter is unexported (private to the package). This rule replaces `public`/`private` keywords in Java or Python's `_` convention. We will revisit this in Lesson 02.

---

## 4. Go's Design Philosophy

Every programming language has a personality, and Go's personality is **opinionated minimalism**. The Go team deliberately rejects features that other languages added in the name of expressiveness. The result is a language with only **25 keywords** (Python has 35, Rust has 50+, C++ has nearly 100), no inheritance, no operator overloading, no exceptions, and (until version 1.18) no generics. Go's philosophy is: **the more features a language has, the harder it is to read code written by someone else.**

The most visible expression of this philosophy is **`gofmt`**, the built-in code formatter. Every Go developer in the world formats their code with the same tool, with the same rules, producing the same output. There are no Go style debates because the language itself enforces one style. When you save a `.go` file in VS Code with the Go extension installed, `gofmt` runs automatically and reformats the file. You can also run it manually:

```bash
gofmt -w main.go
```

The `-w` flag tells `gofmt` to write the formatted result back to the file. Without it, `gofmt` prints the formatted version to stdout and leaves the file untouched. Because every Go project is `gofmt`-formatted, you can read any Go codebase and never waste mental energy on style differences.

Go also enforces discipline at compile time in ways that surprise Python developers. **Unused imports are a compile error.** **Unused local variables are a compile error.** You cannot write `import "fmt"` in your file and then never call any `fmt.` function — the build fails with `imported and not used: "fmt"`. This sounds strict until you realize how much debugging time it saves: the compiler refuses to ship code that has obvious smells, and you never accidentally import a library you do not need. The Go team's view is: **a build error today is cheaper than a runtime bug tomorrow.**

Finally, Go rejected two features that almost every other modern language has: **inheritance** and **exceptions**. There are no classes in Go — instead there are structs (Lesson 06) and composition through embedding. There is no `try/catch` — instead functions return values and errors explicitly (Lesson 05). These choices were controversial at first, but a decade of production use has shown that they make Go codebases dramatically easier to maintain over time. Generics were finally added in **Go 1.18 (March 2022)**, but conservatively — they exist for the cases that genuinely needed them, and the rest of the language continues to favor simplicity.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `go version`. Write down the version number you see. If it is anything below 1.21, go back to [go.dev/dl](https://go.dev/dl/) and install a newer version. Then run `go env GOROOT` — this should print the path where Go is installed (something like `/usr/local/go` on Linux). If both commands work, your Go installation is complete and ready for the rest of the module.

### Exercise 2: Print Something Different

Open your `main.go` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning Go."`. Save the file, then run `go run .` again. Confirm your custom message appears in the terminal. Notice how fast compilation is — Go's builds are measured in milliseconds, not seconds, even for larger projects.

### Exercise 3: Build a Binary

So far you have used `go run`, which compiles your code to a temporary folder, runs it, and deletes the binary. Try `go build` instead — this compiles your code and produces an executable in the current folder. Run `go build` and then list the files with `ls` (Mac/Linux) or `dir` (Windows). You should see a new executable named `hello` (or `hello.exe` on Windows). Run it directly with `./hello` (Mac/Linux) or `hello.exe` (Windows) — it prints `Hello, World!` without needing Go to be installed on the target machine.

---

## Common Mistakes

### Mistake 1: Forgetting `package main`

```go
// WRONG — no package declaration at the top
import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

```go
// RIGHT — every Go file starts with a package declaration
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Every Go file must begin with a `package` line. If you forget it, the compiler stops with `expected 'package', found 'import'`. The fix is always the same: add `package main` (for an executable) or `package yourlib` (for a library) as the very first line of the file. There can be no comments or whitespace above it other than a `//go:build` directive, which is an advanced feature you do not need yet.

### Mistake 2: Lowercase `fmt.println` instead of `fmt.Println`

```go
// WRONG — println does not exist on the fmt package
package main

import "fmt"

func main() {
    fmt.println("Hello, World!")
}
```

```go
// RIGHT — the function is capitalized because it is exported
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

In Go, **capitalization controls visibility**. Names that start with a capital letter are exported (public); names that start with a lowercase letter are unexported (private to the package). `fmt.Println` is exported — the `P` is capital. `fmt.println` does not exist, so the compiler stops with `cannot refer to unexported name fmt.println`. This rule replaces the `public`/`private` keywords you might know from Java. Whenever you call a function from another package, the first letter of the function name must be capitalized.

### Mistake 3: Unused imports cause compile errors

```go
// WRONG — "os" is imported but never used
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println("Hello, World!")
}
```

```go
// RIGHT — only import what you actually use
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

In Python or JavaScript, an unused import is a warning at most. In Go, it is a **compile error**. If you import a package and then never reference it, `go build` fails with `imported and not used: "os"`. The fix is to either remove the import or actually use it. This feels strict until you realize how much confusion it prevents: every import in a Go file is one the file actually needs, which makes refactoring safer and dependencies easier to reason about.

### Mistake 4: Running `go run` outside a folder with a `go.mod`

```text
hello/
└── main.go        ← go.mod is missing in this folder

$ go run .
go: cannot find main module; found .go files but no go.mod
```

```text
hello/
├── go.mod          ← created by `go mod init`
└── main.go

$ go run .
Hello, World!
```

Modern Go (1.16+) requires every folder of Go code to be inside a module — that is, to have a `go.mod` file somewhere up the directory tree. If you just create `main.go` in a fresh folder and run `go run .`, you get `cannot find main module`. The fix is to run `go mod init <module-path>` once in the folder, which creates the `go.mod` file. After that, `go run .`, `go build`, and `go test` all work normally. You only need to do this once per project.

---

## Summary

- Go (Golang) is a statically-typed, compiled, garbage-collected language developed at Google by Robert Griesemer, Rob Pike, and Ken Thompson starting in 2007.
- It was created as a reaction against the complexity of C++ (slow compiles, unreadable template code) and Java (verbose boilerplate) at Google scale.
- Go was publicly announced in November 2009 and shipped stable 1.0 in March 2012, with a strong backward-compatibility promise.
- Install Go from [go.dev/dl](https://go.dev/dl/); verify with `go version` — any version 1.21 or newer works for this module.
- `GOPATH` is the legacy layout; modern Go (1.16+) uses **modules** — each project is a folder with a `go.mod` file at the root.
- Your first program is `package main` + `import "fmt"` + `func main() { fmt.Println("Hello, World!") }`.
- The capital `P` in `Println` matters — Go uses capitalization, not keywords, to control visibility (capital = exported, lowercase = private).
- Go has only 25 keywords, an opinionated `gofmt` formatter, and rejects inheritance and exceptions in favor of simplicity.

You wrote and ran your first Go program. The hardest step — getting the toolchain working — is done. In Lesson 02 we will go deeper into how Go projects are organized: modules, packages, the difference between `go run`, `go build`, and `go install`, and the full power of the `fmt` package.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
