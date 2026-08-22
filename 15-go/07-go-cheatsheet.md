# Lesson 07: Go Cheatsheet

> Module: Go · Lesson 7 of 7
> Estimated time: 30–45 minutes

This is the final lesson of the Go module. It is not a lesson in the usual sense — there are no new concepts, no exercises, and no mistakes section. Instead, it is a **printable reference** that summarizes everything you learned in Lessons 01 through 06 in a single file you can keep next to you while you write Go.

Read through it once now, then bookmark it. Whenever you forget a syntax detail (was it `:=` or `var`? does `switch` fall through by default? what is the verb for "struct with field names"?) come back here. Every snippet below is a complete, idiomatic Go pattern that you can copy into your own code.

---

## 1. Hello, World!

The minimal Go program. Every executable starts with `package main` and a `func main()`.

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Run it with `go run .`. Build a binary with `go build`. Install to `$GOPATH/bin` with `go install`. Initialize a new module with `go mod init example.com/yourname/project`.

---

## 2. Go Commands

These are the commands you will use every day. Memorize the first six; the rest are useful but less common.

| Command | What it does |
|---------|--------------|
| `go run .` | Compile and run the current package, then delete the binary |
| `go run main.go` | Compile and run a specific file |
| `go build` | Compile and produce an executable in the current folder |
| `go build -o myapp` | Same, but name the output `myapp` |
| `go install` | Compile and install to `$GOPATH/bin` (on PATH) |
| `go mod init <path>` | Create a `go.mod` file in the current folder |
| `go mod tidy` | Add missing dependencies, remove unused ones |
| `go get <module>` | Download and add a third-party dependency |
| `go get -u <module>` | Upgrade a dependency to its latest version |
| `go test` | Run all tests in the current package |
| `go test -v` | Run tests with verbose output |
| `go test ./...` | Run tests in all packages recursively |
| `go fmt` | Format all `.go` files in the current folder (uses tabs) |
| `go vet` | Run the standard linter — catches common mistakes |
| `go doc <name>` | Show the documentation for a package or symbol |
| `go env` | Print all Go environment variables |
| `go version` | Print the installed Go version |
| `go clean` | Remove build cache and binaries |

The single most useful command after `go run` is `go doc`. Run `go doc fmt.Println` to see the signature of `Println`; run `go doc net/http` to see the entire `net/http` package documentation in your terminal. It is the fastest way to look up an API without leaving the command line.

---

## 3. Variables

```go
// Three declaration forms
var x int = 5      // full form
var y = 5          // type inferred (int)
z := 5             // short form (inside functions only)

// Package-level (must use var, no :=)
var counter = 0

// Multiple at once
a, b := 3, 4
a, b = b, a        // swap

// Zero values — every Go variable is initialized
var i int        // 0
var s string     // ""
var b bool       // false
var p *int       // nil

// Constants
const Pi = 3.14159
const Greeting = "Hello, World!"

// iota — enumerated constants (Go's replacement for `enum`)
type Weekday int
const (
    Sunday Weekday = iota   // 0
    Monday                  // 1
    Tuesday                 // 2
    Wednesday               // 3
    Thursday                // 4
    Friday                  // 5
    Saturday                // 6
)
```

The rule of thumb: **inside a function, use `:=`; outside a function, use `var`.** Constants are evaluated at compile time. `iota` starts at 0 in each `const` block and increments by 1 for each line.

---

## 4. Types

| Type | Description | Zero value |
|------|-------------|------------|
| `int`, `int8`, `int16`, `int32`, `int64` | signed integers, various widths | `0` |
| `uint`, `uint8`, `uint16`, `uint32`, `uint64` | unsigned integers | `0` |
| `uintptr` | unsigned integer big enough to hold a pointer | `0` |
| `float32`, `float64` | IEEE 754 floats (default is `float64`) | `0.0` |
| `bool` | `true` or `false` | `false` |
| `string` | immutable UTF-8 byte sequence | `""` |
| `byte` | alias for `uint8` | `0` |
| `rune` | alias for `int32`, represents a Unicode code point | `0` |
| `*T` (pointer) | holds the memory address of a `T` | `nil` |
| `[N]T` | array of N values of type T (fixed length) | zero-filled |
| `[]T` | slice of T (variable length) | `nil` |
| `map[K]V` | hash map from K to V | `nil` |
| `struct{...}` | compound type with named fields | each field at its zero value |

```go
// Type conversion — Go has NO implicit conversion
var i int = 5
var f float64 = float64(i)        // explicit
var n int = int(f)                // truncates
var s string = strconv.Itoa(42)   // int to string ("42")
var num, err = strconv.Atoi("42") // string to int

// Pointers — & takes address, * dereferences
x := 42
p := &x          // p is *int
fmt.Println(*p)  // 42
*p = 100
fmt.Println(x)   // 100

// NO pointer arithmetic: p++ would not compile
```

---

## 5. Control Flow

```go
// if — no parentheses, mandatory braces, opening { on same line
if x > 0 {
    fmt.Println("positive")
} else if x == 0 {
    fmt.Println("zero")
} else {
    fmt.Println("negative")
}

// if with init statement (x is scoped to the if/else blocks)
if x := compute(); x > 10 {
    fmt.Println("big:", x)
}

// for — the only loop in Go
// C-style
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

// while-style
for n > 0 {
    n--
}

// infinite
for {
    if done { break }
}

// range — iterate collections
for i, v := range slice {
    fmt.Println(i, v)
}
for _, v := range slice { /* skip index */ }
for k, v := range myMap { /* maps are iterated in random order */ }
for i, r := range "Hello, 世界" { /* i is byte offset, r is rune */ }

// switch — does NOT fall through by default
switch day {
case "Saturday", "Sunday":
    fmt.Println("weekend")
case "Monday":
    fmt.Println("Monday")
default:
    fmt.Println("weekday")
}

// switch with fallthrough (rare)
switch n {
case 1:
    fmt.Println("one")
    fallthrough
case 2:
    fmt.Println("two")
}

// switch without a value — long if/else chain replacement
switch {
case age < 13:
    return "child"
case age < 65:
    return "adult"
default:
    return "senior"
}

// break and continue — same as in C
// labeled break for nested loops
Outer:
    for i := 0; i < 5; i++ {
        for j := 0; j < 5; j++ {
            if i*j >= 6 { break Outer }
        }
    }

// defer — schedule a call to run when the function returns (LIFO order)
defer f.Close()
defer fmt.Println("runs last")
defer fmt.Println("runs first")
// Deferred calls run in reverse order of declaration.
```

---

## 6. Functions

```go
// Basic function with shared parameter type
func add(a, b int) int {
    return a + b
}

// Multiple return values — Go's signature feature
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("cannot divide by zero")
    }
    return a / b, nil
}

// Caller:
result, err := divide(10, 2)
if err != nil {
    return err
}
fmt.Println(result)

// Discard a return value with _
result, _ := divide(10, 2)  // discards the error (use carefully!)

// Named return values
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return  // naked return — uses the named values
}

// Variadic function — any number of args
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
sum(1, 2, 3)        // 6
sum(1, 2, 3, 4, 5)  // 15
nums := []int{1, 2, 3}
sum(nums...)         // spread a slice

// Closures — anonymous functions that capture their scope
add := func(a, b int) int { return a + b }
add(3, 4) // 7

// Closure capturing a variable
func makeCounter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}
c := makeCounter()
c() // 1
c() // 2

// init() — runs automatically before main(), in declaration order
var config map[string]string
func init() {
    config = map[string]string{"env": "production"}
}
```

---

## 7. Structs and Interfaces

```go
// Define a struct
type Person struct {
    Name string  // capital = exported (public)
    Age  int
}

// Create instances
p := Person{Name: "Hieu", Age: 25}
p2 := Person{"Hieu", 25}  // positional, discouraged

// Method with value receiver (read-only)
func (p Person) Greet() string {
    return "Hi, I'm " + p.Name
}

// Method with pointer receiver (can modify)
func (p *Person) HaveBirthday() {
    p.Age++
}

// Embedding (composition over inheritance)
type Employee struct {
    Person       // embedded — fields and methods are promoted
    Title string
}
e := Employee{Person: Person{Name: "Hieu", Age: 25}, Title: "Engineer"}
e.Name    // "Hieu" — promoted from Person
e.Greet() // "Hi, I'm Hieu" — method promoted from Person

// Interface — implicit satisfaction (no `implements` keyword)
type Shape interface {
    Area() float64
}

type Rectangle struct{ Width, Height float64 }
func (r Rectangle) Area() float64 { return r.Width * r.Height }

type Circle struct{ Radius float64 }
func (c Circle) Area() float64 { return 3.14159 * c.Radius * c.Radius }

// Both Rectangle and Circle satisfy Shape automatically
func printArea(s Shape) { fmt.Println(s.Area()) }
printArea(Rectangle{Width: 3, Height: 4})
printArea(Circle{Radius: 5})

// Empty interface (now aliased as `any`)
func printAnything(v any) { fmt.Println(v) }
printAnything(42)
printAnything("hello")

// Type assertion
var i any = "hello"
s, ok := i.(string)  // s = "hello", ok = true
n, ok := i.(int)     // n = 0,       ok = false
// s := i.(string)    // panics if not a string (without comma-ok)

// Type switch
switch v := i.(type) {
case string:
    fmt.Println("string:", v)
case int:
    fmt.Println("int:", v)
default:
    fmt.Printf("unknown: %T\n", v)
}

// Generics (Go 1.18+)
func Map[T, U any](in []T, f func(T) U) []U {
    out := make([]U, len(in))
    for i, v := range in {
        out[i] = f(v)
    }
    return out
}
doubled := Map([]int{1, 2, 3}, func(n int) int { return n * 2 })
// doubled = [2, 4, 6]
```

---

## 8. Error Handling

Go has no exceptions. Errors are returned as the last value of a function, and the caller is expected to check them.

```go
// Sentinel errors — package-level error values
var ErrNotFound = errors.New("not found")

func getUser(id int) (string, error) {
    if id <= 0 {
        return "", ErrNotFound
    }
    return "user" + strconv.Itoa(id), nil
}

// Wrapping errors with context
func loadConfig(path string) error {
    f, err := os.Open(path)
    if err != nil {
        return fmt.Errorf("open %s: %w", path, err)  // %w wraps the original
    }
    defer f.Close()
    return nil
}

// errors.Is — check if an error matches a sentinel (handles wrapping)
if errors.Is(err, ErrNotFound) {
    fmt.Println("user not found")
}

// errors.As — extract a typed error from a wrapped chain
var pathErr *fs.PathError
if errors.As(err, &pathErr) {
    fmt.Println("path error:", pathErr.Path)
}

// The idiom — used millions of times in Go code
result, err := doSomething()
if err != nil {
    return err  // or: log it, wrap it, return a different error
}
// use result

// Custom error type — for richer error info
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}
```

The pattern `if err != nil { return err }` is the single most common line in Go. It is verbose but explicit — every error path is visible at the call site, which makes debugging easier. Get used to seeing and writing it.

---

## 9. Concurrency (Brief Preview)

Go's headline feature is **goroutines** — lightweight threads that cost only 2 KB of stack and start in microseconds. You can run hundreds of thousands of them in a single process. Goroutines communicate via **channels**, which are typed conduits for sending values between goroutines safely.

```go
package main

import (
    "fmt"
    "time"
)

func sayHello(name string) {
    fmt.Println("Hello from", name)
}

func main() {
    // Start a goroutine with the `go` keyword
    go sayHello("Gopher 1")
    go sayHello("Gopher 2")

    // Wait a moment so the program does not exit before they print
    time.Sleep(time.Millisecond * 100)
}

// Channels — typed conduits for goroutine communication
ch := make(chan int)        // unbuffered
ch := make(chan int, 5)     // buffered with capacity 5

// Send and receive
ch <- 42       // send 42 into the channel
v := <-ch      // receive a value from the channel

// Close a channel (sender side only)
close(ch)

// Range over a channel until it is closed
for v := range ch {
    fmt.Println(v)
}

// Select — multiplex over multiple channel operations
select {
case v := <-ch1:
    fmt.Println("from ch1:", v)
case ch2 <- 42:
    fmt.Println("sent to ch2")
case <-time.After(time.Second):
    fmt.Println("timeout")
}
```

Concurrency is not formally part of this module — it deserves its own module. But every Go developer should recognize the syntax. The key insight is: **do not communicate by sharing memory; share memory by communicating.** Instead of locking a shared variable, send it through a channel from one goroutine to another, so only one goroutine owns it at a time. This is the Go philosophy, and it eliminates a huge class of concurrency bugs.

---

## 10. Common Idioms

```go
// 1. The "if err != nil" pattern — used everywhere
result, err := doSomething()
if err != nil {
    return fmt.Errorf("doSomething failed: %w", err)
}

// 2. Table-driven tests
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -2, -3},
        {"zero",     0, 0, 0},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            if got := Add(tt.a, tt.b); got != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d", tt.a, tt.b, got, tt.expected)
            }
        })
    }
}

// 3. Defer for cleanup — pair acquire with release
f, err := os.Open("file.txt")
if err != nil { return err }
defer f.Close()    // runs no matter how we return

mu.Lock()
defer mu.Unlock()  // runs no matter how we return

// 4. context.Context for cancellation and timeouts
func handler(ctx context.Context) error {
    select {
    case <-ctx.Done():
        return ctx.Err()
    case result := <-slowOperation():
        return result
    }
}

// 5. Functional options for configurable constructors
type Server struct { port int }
type Option func(*Server)
func WithPort(p int) Option { return func(s *Server) { s.port = p } }
func NewServer(opts ...Option) *Server {
    s := &Server{port: 8080}  // default
    for _, opt := range opts { opt(s) }
    return s
}
```

These five patterns appear in virtually every Go codebase of any size. Table-driven tests in particular are the standard way to write tests in Go — they make adding test cases trivial and keep the test logic separate from the test data.

---

## 11. Common Pitfalls

```go
// 1. Unused imports/vars are compile errors (not warnings)
import "os"          // never used → build fails
x := 5              // never read → build fails

// 2. Switch does NOT fall through by default
switch n {
case 1:
    fmt.Println("one")
case 2:
    fmt.Println("two")  // does NOT also print "two" if n==1
}

// 3. := only works inside functions
x := 5              // ERROR at package level
var x = 5           // correct at package level

// 4. String indexing returns bytes, not runes
s := "Hello, 世界"
fmt.Println(s[0])           // 72 (byte), NOT 'H'
fmt.Println(string(s[0]))   // "H"
runes := []rune(s)
fmt.Println(runes[7])       // 19990 (the rune for '世')

// 5. Map iteration is randomized
m := map[string]int{"a": 1, "b": 2, "c": 3}
for k, v := range m {       // order is NOT guaranteed
    fmt.Println(k, v)
}

// 6. nil map cannot be written to (but can be read)
var m map[string]int
m["a"] = 1                   // panics: assignment to entry in nil map
m = make(map[string]int)     // initialize first
m["a"] = 1                   // works

// 7. nil slice can be appended to (but nil map cannot be written)
var s []int
s = append(s, 1)             // works — append handles nil

// 8. == on structs only works if all fields are comparable
type Person struct {
    Name string
    Friends []string          // not comparable
}
// p1 == p2 would be a compile error

// 9. Loop variable capture (pre-Go 1.22)
for _, v := range items {
    go func() { fmt.Println(v) }()  // prints last value many times!
}
// Fix (pre-1.22): shadow the variable
for _, v := range items {
    v := v
    go func() { fmt.Println(v) }()
}
// In Go 1.22+, the loop variable is scoped per-iteration automatically.
```

These pitfalls are the source of most beginner bugs in Go. Print this list and keep it next to you while you write your first few hundred lines of Go.

---

## 12. The 10 Most-Used Standard Library Packages

Go's standard library is one of its biggest selling points. For most backend work you never need a third-party framework — the standard library has it all. Here are the ten packages you will reach for most often.

| Package | What it gives you |
|---------|-------------------|
| [`fmt`](https://pkg.go.dev/fmt) | `Println`, `Printf`, `Sprintf` — formatted I/O |
| [`os`](https://pkg.go.dev/os) | `os.Args`, `os.Open`, `os.Getenv`, `os.Exit` — operating system interface |
| [`io`](https://pkg.go.dev/io) | `io.Reader`, `io.Writer`, `io.Copy` — streaming I/O interfaces |
| [`strings`](https://pkg.go.dev/strings) | `strings.HasPrefix`, `strings.Split`, `strings.Replace` — string manipulation |
| [`strconv`](https://pkg.go.dev/strconv) | `strconv.Itoa`, `strconv.Atoi`, `strconv.ParseFloat` — string ↔ primitive conversion |
| [`errors`](https://pkg.go.dev/errors) | `errors.New`, `errors.Is`, `errors.As` — error creation and inspection |
| [`context`](https://pkg.go.dev/context) | `context.Context` — cancellation, timeouts, request-scoped values |
| [`net/http`](https://pkg.go.dev/net/http) | `http.Get`, `http.ListenAndServe`, `http.Handler` — HTTP client and server |
| [`encoding/json`](https://pkg.go.dev/encoding/json) | `json.Marshal`, `json.Unmarshal`, struct tags — JSON encoding |
| [`time`](https://pkg.go.dev/time) | `time.Now`, `time.Sleep`, `time.Duration` — time and duration |

A common Go pattern: read a JSON HTTP endpoint in five lines, no framework needed.

```go
package main

import (
    "encoding/json"
    "fmt"
    "net/http"
)

type User struct {
    Name  string `json:"name"`
    Email string `json:"email"`
}

func main() {
    resp, err := http.Get("https://api.example.com/user/1")
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close()

    var u User
    if err := json.NewDecoder(resp.Body).Decode(&u); err != nil {
        panic(err)
    }
    fmt.Println(u.Name, u.Email)
}
```

Notice the backtick struct tags (`json:"name"`): these tell `encoding/json` how to map JSON field names to Go field names. They are an example of Go's "struct tags" feature, which lets you attach metadata to fields that other packages can read via reflection.

---

## What's Next?

You have reached the end of the Go module. You now understand Go's design philosophy, its module and package system, its type system, control flow, functions, structs, interfaces, and generics — enough to read and write real Go programs. You also have a one-page cheatsheet you can keep next to you while you code.

Where do you go from here? A few suggestions for the next step in your journey:

1. **Build something.** The best way to lock in what you learned is to build a small project. Write a command-line tool that fetches the weather for your city (use `net/http` and `encoding/json`). Write a tiny HTTP server with `net/http` that returns `"Hello, World!"`. Write a CLI that converts JSON to YAML. Pick something small and ship it.

2. **Take [A Tour of Go](https://go.dev/tour/).** This interactive tutorial runs in your browser and reinforces the same concepts with different examples. It is also a great reference for the parts of Go we did not cover (like full goroutines and channels).

3. **Read [Effective Go](https://go.dev/doc/effective_go).** This document explains the idiomatic patterns experienced Go developers use — when to use pointers, when to use interfaces, how to organize a package. Reading it once you have the basics will dramatically improve your code style.

4. **Learn a Go web framework.** Go's standard library `net/http` is enough for many projects, but frameworks like [Gin](https://gin-gonic.com/), [Echo](https://echo.labstack.com/), and [Fiber](https://gofiber.io/) add conveniences (routing, middleware, validation) for larger applications. [Gorilla Mux](https://github.com/gorilla/mux) was the long-time standard but is now in maintenance mode.

5. **Move on to the next language: TypeScript.** In Module 16 you will learn TypeScript — a typed superset of JavaScript that brings Go-style type safety to the web frontend. Many of the concepts you learned in Go (interfaces, type inference, generics) have direct equivalents in TypeScript, and you will find the language familiar in surprising ways.

```text
            HTML  ─── CSS  ─── Python  ─── Rust  ─── Go  ─── TypeScript
  structure   style    general    systems   cloud    frontend
                                                      + types
```

Thank you for completing Module 15. Welcome to the Go community — pick a gopher avatar, install the Go extension in your editor, and start building. The next module is at [/home/z/my-project/learn-dev/16-typescript/](../16-typescript/).
