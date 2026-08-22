# Module 15: Go — Simple, Fast, and Concurrent

<p align="center">
  <img src="../logorepo.png" alt="Learn Dev Logo" width="120" height="120" />
</p>

Welcome to Module 15. You have come a long way — HTML for structure, CSS for style, Python for general-purpose programming, and Rust for fearless systems work. Now we meet **Go** (often called **Golang**), a language created at Google to solve a very specific problem: how do you write server software for a company with billions of users, tens of thousands of engineers, and millions of lines of code, without collapsing under its own complexity? Go is the answer Google came up with, and the answer turned out to be so good that the rest of the world adopted it too.

Go is famous for being almost boring in the best possible way. It has only 25 keywords, compiles in milliseconds, ships as a single self-contained binary, and has concurrency built into the language itself. **Docker, Kubernetes, Terraform, Prometheus, Hugo, and thousands of cloud tools you already use are written in Go.** If you want to work on cloud infrastructure or modern backends, Go is one of the most valuable languages to learn right now.

This module is gentler than Rust but stricter than Python, so it sits nicely between the two. By the end you will understand Go's design philosophy, write real programs with structs and interfaces, and get your first taste of goroutines and channels — the feature that makes Go's concurrency story legendary.

---

## What is Go?

Go is a **statically-typed, compiled, garbage-collected programming language** developed at Google. "Statically-typed" means every variable has a type known to the compiler at compile time, just like Rust and Java — type errors are caught before your program runs. "Compiled" means Go source code is translated directly into a single native binary ahead of time, with no runtime interpreter and no separate dependency files. "Garbage-collected" means Go automatically reclaims memory that is no longer used, so you never have to call `free()` the way you do in C — unlike Rust, which achieves safety through compile-time ownership rules instead.

The language was created in **2007** by three legendary engineers at Google: **Robert Griesemer**, **Rob Pike**, and **Ken Thompson**. The names matter, because all three had already shaped computing history. Ken Thompson co-created the **Unix operating system** and the **B programming language** (a direct ancestor of C). Rob Pike co-created the **UTF-8 character encoding** that every modern web page uses. Robert Griesemer worked on **Google's V8 JavaScript engine** and the **Java HotSpot compiler**. When three engineers of that caliber say "we are designing a new language," the programming world listens.

Why did they design Go? Out of frustration. In 2007, Google's codebases were built primarily in **C++ and Java** — two extremely powerful but extremely complex languages. C++ builds at Google scale could take 30 minutes or more, and the language had grown so feature-rich that even senior engineers struggled to read each other's code. Java was verbose: writing a simple HTTP handler could require dozens of lines of boilerplate, factory classes, and XML configuration. Pike famously described the constant complexity as "an industry-wide problem." Go was their answer: a language designed to be **simple, fast to compile, fast to run, and easy to read** at scale. They deliberately rejected features like inheritance, generics (later added conservatively in 1.18), and exceptions, because every feature added is a feature a new engineer has to learn.

Go was **publicly announced in November 2009** and reached **stable 1.0 in March 2012**. Since then, Go has guaranteed **backward compatibility** — code written for 1.0 still compiles on Go 1.21 today. That stability promise is one reason companies trust Go for long-lived infrastructure projects. Today Go is used in production by **Google** (much of its internal infrastructure), **Docker** (containerization), **Kubernetes** (container orchestration, originally built at Google), **Terraform** (infrastructure as code by HashiCorp), **Uber** (geo-fence services, rewritten from Node.js and Python), **Twitch** (video transcoding services), and **Dropbox** (storage backend, rewritten from Python). The Go gopher mascot — a friendly cartoon gopher drawn by Renée French — is one of the most recognizable logos in programming.

---

## Why Learn Go?

You already know Python and Rust. Why add Go now? Here are the strongest reasons.

- **Simple syntax — only 25 keywords.** Go's entire language fits on one page. There is no inheritance hierarchy to learn, no template metaprogramming, no implicit constructors, no operator overloading. A new Go engineer can read an unfamiliar Go codebase on day one and understand what it does. This simplicity is intentional — Google needed a language that tens of thousands of engineers could all use productively without becoming language lawyers.

- **Blazing fast compile times.** A medium-sized Go project compiles in seconds, not minutes. A large Go project at Google scale compiles in under a minute. This is a deliberate design choice — Go's designers believed that fast compiles change how engineers work, because you can run your tests after every small change without losing focus. Compared to a typical C++ or Rust build, the difference is dramatic.

- **Single static binary deployment.** When you run `go build`, you get one self-contained executable file with no external dependencies, no DLLs to ship, no Python virtual environments to replicate, no Node.js runtimes to install. You copy that binary to a Docker container (or a server, or a Raspberry Pi with the same OS/architecture) and it just runs. This makes Go incredibly easy to deploy, which is why every modern cloud-native tool ships as a single Go binary.

- **Excellent standard library.** Go ships with a production-grade HTTP server (`net/http`), a JSON encoder (`encoding/json`), a TLS stack, a testing framework (`testing`), a SQL interface (`database/sql`), and a context system for cancellation — all built in. For many backend services you never need a third-party framework at all. The Go team treats the standard library as a first-class product.

- **Goroutines make concurrency easy.** In most languages, starting a thread costs megabytes of memory and milliseconds of startup time. In Go, you write `go myFunction()` and the runtime spawns a **goroutine** — a lightweight thread that costs only 2 KB of stack and starts in microseconds. You can run hundreds of thousands of goroutines in a single process. Combined with **channels** for safe communication, Go makes concurrent programming approachable in a way that C, Java, Python, and even Rust do not.

- **Growing in cloud and backend.** The Cloud Native Computing Foundation's flagship projects — Kubernetes, Prometheus, containerd, etcd, gRPC, Istio — are nearly all written in Go. If you want to work on cloud infrastructure, DevOps tooling, site reliability engineering, or modern backend services, Go is the dominant language of that ecosystem.

---

## Lesson Index

This module contains 7 lessons. Each lesson is a separate Markdown file in this folder. Read them in order — Go's simplicity is deceptive, and later lessons build on conventions established early.

| # | Lesson | What You Will Learn |
|---|--------|---------------------|
| 01 | [What is Go?](./01-what-is-go.md) | What Go is, where it came from, how to install it, and your first program. |
| 02 | [Hello, World!](./02-hello-world.md) | Modules, packages, `go run` vs `go build`, and the `fmt` package. |
| 03 | [Variables and Types](./03-variables-and-types.md) | `var`, `:=`, zero values, basic types, type conversion, constants, pointers. |
| 04 | [Control Flow](./04-control-flow.md) | `if` with init, `for` (the only loop), `range`, `switch`, `defer`. |
| 05 | [Functions](./05-functions.md) | Multiple returns, named returns, variadic functions, closures, `init`. |
| 06 | [Structs and Interfaces](./06-structs-and-interfaces.md) | Structs, methods, embedding, implicit interfaces, generics. |
| 07 | [Go Cheatsheet](./07-go-cheatsheet.md) | A printable one-page summary of everything. |

---

## How to Practice

For this entire module, you will need three things. The setup takes about 5 minutes and you only do it once.

1. **Install Go from [go.dev/dl](https://go.dev/dl/).** The website auto-detects your operating system and offers the correct installer. On Windows, download the `.msi` installer and run it (it will add Go to your PATH automatically). On Mac, download the `.pkg` installer, or use Homebrew with `brew install go`. On Linux, download the `.tar.gz`, extract it to `/usr/local/go`, and add `/usr/local/go/bin` to your PATH. When installation finishes, open a fresh terminal and verify with:

   ```bash
   go version
   ```

   You should see something like `go version go1.21.5 linux/amd64`. If you see `command not found`, close every terminal and open a new one so the PATH change takes effect. Any Go version 1.21 or newer will work for this entire module.

2. **Install Visual Studio Code** with the official Go extension. Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/). Once installed, open the Extensions panel (`Ctrl+Shift+X` / `Cmd+Shift+X`), search for the "Go" extension (published by the Go Team at Google), and click Install. This gives you autocompletion, go-to-definition, inline error highlighting, automatic formatting on save via `gofmt`, and a debugger — all powered by the same `gopls` language server that powers Go itself.

3. **Create your first Go module.** In a terminal, navigate to a folder where you want to keep your code, then run these three commands:

   ```bash
   mkdir hello
   cd hello
   go mod init example.com/hello
   ```

   The `go mod init` command creates a `go.mod` file — the equivalent of Python's `requirements.txt` or Rust's `Cargo.toml`. It marks the folder as a Go module and lets you run Go code inside it. Create a file called `main.go` with the contents below, then run `go run .` to compile and execute it.

   ```go
   package main

   import "fmt"

   func main() {
       fmt.Println("Hello, World!")
   }
   ```

   You should see `Hello, World!` printed. If you do, your Go installation is fully working.

---

## Estimated Time

If you spend 30 to 60 minutes per lesson (reading carefully, typing out every example, and doing the exercises), you should complete this module in roughly **8 to 12 hours of total study**. Go is significantly faster to learn than Rust, because there are no ownership rules and no borrow checker — but it still requires you to absorb its conventions: capitalization for visibility, the `if err != nil` pattern, and the goroutine/channel mental model. Take your time on Lesson 04 (control flow — `for` is the only loop, which surprises many beginners) and Lesson 06 (structs and interfaces — Go's approach to polymorphism is genuinely different from Java or Python).

If you get stuck, two resources are excellent companions. The official **[A Tour of Go](https://go.dev/tour/)** is an interactive tutorial that runs in your browser, and the **[Effective Go](https://go.dev/doc/effective_go)** document explains the idiomatic patterns experienced Go developers use. The Go compiler's error messages are clear and helpful — read them carefully, they almost always tell you exactly which line to fix. Take breaks often, type every example yourself rather than copy-pasting, and welcome the simplicity: Go's restrictions are its biggest feature.

---

<p align="center">
  Ready? Open <a href="./01-what-is-go.md">Lesson 01: What is Go?</a> and write your first Go program.
</p>
