# Lesson 02: Hello, World!

> Module: Go · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you installed Go, created your first module, and ran `fmt.Println("Hello, World!")`. That got you to a working setup. In this lesson we will slow down and look at the moving parts of a Go project — what a module is, what a package is, what `go run` actually does, and what the `fmt` package gives you. By the end, you will know how to organize a real Go project and how to format output any way you like.

This lesson is short on new concepts but heavy on vocabulary. The reason is that every Go lesson after this one will use words like "module", "package", and "import" without re-explaining them. Take a few minutes now to lock the definitions in place, and the rest of the module will read smoothly.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain the difference between a Go **module** and a Go **package**, and know which folder layout each requires.
2. Use `go run`, `go build`, and `go install` correctly and explain the difference between the three.
3. Use the `fmt` package's three main functions: `Println`, `Printf`, and `Print` — and know which format verb to use for each type.
4. Write single-line and block comments in Go, and follow the convention for doc comments.

---

## 1. Modules and Packages

Go code is organized at two levels: **modules** and **packages**. Understanding the difference is essential because every Go project, however small, is structured this way.

A **module** is the unit of distribution. It is a folder that contains a `go.mod` file at its root. The `go.mod` file declares three things: the module's unique path (like `github.com/yourname/project`), the Go version it requires (like `go 1.21`), and any dependencies on other modules. When you publish a Go library, you publish a module. When you `go get` a dependency, you download a module. Every modern Go project is one module, even if it is just one file.

A **package** is a folder of Go source files that all share the same `package` declaration. When you write `package main` at the top of `main.go`, you are saying "this file is part of the package called `main`." If you create a second file in the same folder called `helpers.go` and write `package main` at its top, both files belong to the same package and can see each other's functions without any import. A module can contain many packages, one per subfolder. The relationship looks like this:

```text
myproject/                  ← module root (folder)
├── go.mod                  ← module declaration
├── main.go                 ← package main (executable entry point)
├── README.md
└── internal/               ← subfolder, becomes a new package
    └── utils.go            ← package utils (a library)
```

Let's create a fresh module and look at the `go.mod` file it generates. In a terminal:

```bash
mkdir myproject
cd myproject
go mod init example.com/myproject
```

The `go mod init` command creates a `go.mod` file in the current folder. Open it in your editor — it should look like this:

```text
module example.com/myproject

go 1.21
```

- `module example.com/myproject` — the **module path**. This is a globally unique name for your module. For real projects it is usually a URL like `github.com/yourname/project` so other people can `go get` your module. For local experiments, `example.com/whatever` is fine.
- `go 1.21` — the **minimum Go version** this module requires. The toolchain uses this to decide which language features to allow. If you try to use a feature that requires Go 1.18 (like generics) and your `go.mod` says `go 1.17`, the compiler will reject it.

The `go.mod` file grows automatically as you add dependencies. When you `go get` a third-party package, Go adds a `require` block listing the module path and version. You will see this in action in later lessons.

---

## 2. Anatomy of a Go Program

Let's revisit the `main.go` file from Lesson 01 and look at each part more carefully now that you understand modules and packages.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println("Hello, World!")
    fmt.Println("Args count:", len(os.Args))
    fmt.Println("Program name:", os.Args[0])
}
```

This compiles and runs cleanly. `os.Args` is a slice of strings (we cover slices briefly in Lesson 03 and beyond) containing the command-line arguments; `os.Args[0]` is the name of the program itself, and `len(os.Args)` gives the total count. Note that functions in the `os` package that read from the system often return **two** values — a result and an error — and you will see the `(value, error)` pattern in Lesson 05. For now, focus on the structure: this is what a real Go program looks like.

- `package main` — the **package declaration**. Every `.go` file starts with one. The `main` package is special: it tells Go that this folder contains an executable program, and the toolchain looks for `func main()` inside it as the entry point.
- `import (...)` — the **import block**. When you import multiple packages, you can write them in parentheses, one per line. This is the idiomatic style — `gofmt` will group standard library imports first, then third-party imports, separated by a blank line. You can also write single imports without parentheses (`import "fmt"`), which is what `gofmt` produces when there is only one.
- `func main()` — the **entry point**. The Go runtime calls this function automatically when the program starts. It takes no arguments and returns nothing — command-line arguments come from the `os` package, not from `main`'s signature.
- The function body is wrapped in `{ }` braces and indented with tabs. Go enforces `gofmt` formatting, so do not fight it — let your editor apply it on save.

Here is a slightly larger program that uses two standard library packages and a third-party one (after running `go get github.com/fatih/color`):

```go
package main

import (
    "fmt"
    "os"
    "strings"

    "github.com/fatih/color"
)

func main() {
    name := "Gopher"
    fmt.Println(strings.ToUpper(name))
    color.Green("OS:", os.Getenv("OS"))
}
```

The imports are grouped into two blocks: standard library first, third-party second. `gofmt` enforces this convention so every Go project looks the same. You do not have to remember to do it — your editor's "Format on Save" feature handles it.

---

## 3. Running Your Code

Go gives you three commands for running code, and they produce different results. Knowing which to use when is part of being productive in Go.

### `go run` — compile and run, no binary left behind

```bash
go run .
```

`go run` compiles your code to a temporary folder, runs the resulting executable, and then deletes it. It is the fastest way to iterate during development. The argument `.` means "the package in the current folder" — you can also pass specific files (`go run main.go`) or a folder path (`go run ./cmd/server`). Use `go run` when you are experimenting or writing small scripts.

### `go build` — compile to a binary in the current folder

```bash
go build
```

`go build` compiles your code and produces an executable in the **current folder**. The executable is named after the module (or, if you are in a subfolder, after the subfolder). For a module called `example.com/myproject`, the binary will be named `myproject` (or `myproject.exe` on Windows). The binary is fully self-contained — no external dependencies, no Python runtime, no Node.js — you can copy it to a server with the same OS/architecture and run it directly. Use `go build` when you want to produce a binary you can deploy.

You can also pass `-o` to control the output filename:

```bash
go build -o myapp
```

### `go install` — compile and install to `$GOPATH/bin`

```bash
go install
```

`go install` compiles your code and places the resulting binary in a special folder called `$GOPATH/bin` (on a default install, this is `~/go/bin`). That folder is conventionally on your PATH, which means after `go install` you can run your program from anywhere by just typing its name. Use `go install` for command-line tools you want to use every day — this is how tools like `golangci-lint`, `gofumpt`, and `air` are installed.

The relationship between the three commands is:

```text
                       produces binary?
go run   .         →   no (temporary, deleted after run)
go build           →   yes, in current folder
go install         →   yes, in $GOPATH/bin (on PATH)
```

---

## 4. The `fmt` Package

The `fmt` package (pronounced "fumpt") is Go's standard library for formatted I/O — the equivalent of C's `printf` family, Python's f-strings, and Rust's `println!` macro. It is one of the first packages every Go programmer learns, and it has three core functions you will use constantly.

### `fmt.Println` — print with a newline

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
    fmt.Println("Go", "is", "fun")
}
```

`Println` (capital P, lowercase ln) prints its arguments separated by spaces, followed by a newline. It calls `fmt.Sprint` on each argument to convert it to a string, so it works with any type — `fmt.Println(42)`, `fmt.Println(3.14)`, `fmt.Println(true)` all print their values directly.

### `fmt.Print` — print without a newline

```go
fmt.Print("Hello, ")
fmt.Print("World!")
fmt.Println()        // prints just a newline
```

`Print` does the same as `Println` but without adding a newline at the end. Use it when you want to print a prompt on the same line as user input, or compose a line piece by piece.

### `fmt.Printf` — print with format verbs

`Printf` (capital P, lowercase f) is the most powerful of the three. It uses **format verbs** — placeholders starting with `%` — to insert values into a template string. The template is the first argument; the values follow in order. This is the equivalent of C's `printf` or Python's `%`-formatting.

```go
package main

import "fmt"

func main() {
    name := "Gopher"
    age := 5
    height := 1.75
    fmt.Printf("Name: %s, Age: %d, Height: %.2f meters\n", name, age, height)
}
```

Let's break down the format string `"Name: %s, Age: %d, Height: %.2f meters\n"`:

- `%s` — substitute a **string** (`name`).
- `%d` — substitute a **decimal integer** (`age`).
- `%.2f` — substitute a **float** with 2 digits after the decimal point (`height`). The `.2` between `%` and `f` controls precision.
- `\n` — a literal newline character, because `Printf` does not add one for you (unlike `Println`).

The most common format verbs are:

| Verb | Meaning | Example |
|------|---------|---------|
| `%d` | decimal integer | `42` |
| `%s` | string | `"Gopher"` |
| `%f` | float | `3.140000` |
| `%.2f` | float with 2 decimals | `3.14` |
| `%v` | any value (default format) | works on anything |
| `%+v` | struct with field names | `{Name:Gopher Age:5}` |
| `%#v` | Go-syntax representation | `main.Person{Name:"Gopher"}` |
| `%T` | type of the value | `main.Person` |
| `%q` | quoted string | `"Gopher"` |
| `%x` | hexadecimal | `2a` |

The `%v` verb is a fallback that works on any type — when in doubt, use `%v`. Here is an example with the struct verbs (we will cover structs in Lesson 06):

```go
package main

import "fmt"

func main() {
    type Person struct {
        Name string
        Age  int
    }
    p := Person{Name: "Gopher", Age: 5}
    fmt.Printf("v    : %v\n", p)
    fmt.Printf("+v   : %+v\n", p)
    fmt.Printf("#v   : %#v\n", p)
    fmt.Printf("T    : %T\n", p)
}
```

This will print:

```text
v    : {Gopher 5}
+v   : {Name:Gopher Age:5}
#v   : main.Person{Name:"Gopher", Age:5}
T    : main.Person
```

The `%+v` form is incredibly useful for debugging — it shows you the field names of any struct without you having to write a custom print method.

---

## 5. Comments

Go has two kinds of comments, just like C, Java, and JavaScript.

**Line comments** start with `//` and run to the end of the line:

```go
// This is a line comment.
fmt.Println("hi") // This is an inline comment.
```

**Block comments** start with `/*` and end with `*/`. They can span multiple lines:

```go
/* This is a block comment.
   It can span multiple lines.
   But block comments are rarely used in Go —
   the convention is to use // for everything. */
```

In practice, Go developers almost never use block comments. The community prefers `//` for everything, including multi-line comments — just put `//` at the start of each line. `gofmt` does not enforce this, but convention does.

The one place block comments survive is **package documentation**. A block comment placed immediately before a `package` declaration, with no blank line between, becomes the package's doc comment — accessible via `go doc` or [pkg.go.dev](https://pkg.go.dev/). The same applies to exported functions: a comment that starts with the function's name, placed immediately above the function declaration, becomes its doc comment.

```go
// Package greetings provides friendly hello messages.
package greetings

// Hello returns a friendly greeting for the named person.
// If name is empty, Hello returns a generic greeting.
func Hello(name string) string {
    return "Hello, " + name
}
```

Notice the convention: **the comment starts with the name of the thing it documents** (`Hello` in this case), and is a complete sentence ending in a period. This convention is enforced by `go vet` and the `golint` tool. When you publish a Go package, these doc comments are rendered as HTML on [pkg.go.dev](https://pkg.go.dev/) for everyone to read.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Explore `go.mod`

In your `myproject` folder from Section 1, run `go mod tidy`. This command downloads any missing dependencies and removes dependencies you are no longer using. Then open `go.mod` and read every line. Write down the module path, the Go version, and (if any) the dependencies listed. Try running `go mod why fmt` — this explains why a particular package is in your dependency tree.

### Exercise 2: Use `fmt.Printf` with Multiple Verbs

Create a new program that declares a `name` string, an `age` int, and a `pi` float, then prints them in a single `fmt.Printf` line using `%s`, `%d`, and `%.4f`. Your output should look something like `Name: Ada, Age: 36, Pi: 3.1416`. Then add a second `fmt.Printf` line using `%v` for all three values. Notice that `%v` works on any type, which makes it a useful fallback.

### Exercise 3: Write a Doc Comment

Add a function called `Greet(name string) string` to your program that returns `"Hello, " + name + "!"`. Above the function declaration, add a doc comment that starts with the word `Greet` and explains what the function does. Then run `go doc Greet` from the command line — you should see your doc comment rendered in the terminal. This is the same convention the Go standard library uses, and it is what makes `go doc` work for any package in the Go ecosystem.

---

## Common Mistakes

### Mistake 1: Unused imports cause build failure

```go
// WRONG — "os" is imported but never referenced
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

In Python or JavaScript, an unused import is a harmless warning. In Go it is a **hard compile error**. The compiler stops with `imported and not used: "os"`. The fix is to either remove the import or actually call something from the package. Many editors (including VS Code with the Go extension) will remove unused imports automatically when you save — make sure "Organize Imports on Save" is enabled.

### Mistake 2: Lowercase `fmt.println`

```go
// WRONG — println does not exist on fmt
package main

import "fmt"

func main() {
    fmt.println("Hello, World!")
}
```

```go
// RIGHT — Println, with a capital P, is the exported function
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

In Go, the first letter of a name controls its visibility. `Println` starts with a capital `P` because it is **exported** — accessible from outside the `fmt` package. `fmt.println` does not exist. The compiler will give you `cannot refer to unexported name fmt.println` and suggest you meant `Println`. Whenever you call a function from another package, the first letter of the function name must be capitalized.

### Mistake 3: Using `println` instead of `fmt.Println`

```go
// WORKS but is wrong — println is a built-in for debugging only
package main

func main() {
    println("Hello, World!")
}
```

```go
// RIGHT — use fmt.Println for any real program
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Go does have a built-in function called `println` (lowercase, no `fmt.` prefix), but it is **for debugging the Go runtime itself**, not for your code. The built-in `println` writes to standard error (not standard output), has no formatting options, is not guaranteed to stay in future Go versions, and is documented in the spec as something you should not use. Always use `fmt.Println` instead. The Go team has considered removing `println` from the language for years — code that depends on it may stop compiling.

### Mistake 4: Mixing tabs and spaces

```go
// WRONG — first line uses spaces, second line uses tabs
package main

import "fmt"

func main() {
    fmt.Println("hi")
        fmt.Println("bye")   // ← indented with spaces
}
```

```go
// RIGHT — every indented line uses a tab (gofmt's standard)
package main

import "fmt"

func main() {
        fmt.Println("hi")
        fmt.Println("bye")
}
```

Go is one of the only languages where the official style guide **mandates tabs for indentation**, not spaces. The reason is historical: the original Go designers wanted tabs because they let each viewer pick their own indent size in their editor without reformatting the file. `gofmt` enforces this — if you run `gofmt -w main.go`, it will rewrite all your spaces to tabs. Configure your editor to insert a tab when you press the Tab key, and enable "Format on Save" with `gofmt`. If you mix tabs and spaces manually, `gofmt` will silently rewrite your file.

---

## Summary

- A **module** is a folder with a `go.mod` file — the unit of distribution in Go.
- A **package** is a folder of `.go` files that all share the same `package` declaration — the unit of compilation.
- `go.mod` declares the module path (e.g. `example.com/myproject`), the Go version, and any third-party dependencies.
- Use `go run .` to iterate during development (no binary left behind).
- Use `go build` to produce a deployable binary in the current folder.
- Use `go install` to install a binary to `$GOPATH/bin` so you can run it from anywhere.
- The `fmt` package provides `Println` (newline), `Print` (no newline), and `Printf` (format verbs like `%s`, `%d`, `%v`, `%+v`).
- Comments are `//` for line and `/* */` for block; doc comments above declarations should start with the name of the thing being documented.

You now understand the structure of a Go project and the basics of formatted output. In Lesson 03 we will look at Go's variable declaration forms (`var`, `:=`, `const`), its zero-value safety feature, and its strict approach to type conversion.

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
