# Lesson 06: Structs and Interfaces

> Module: Go · Lesson 6 of 7
> Estimated time: 30–45 minutes

In Lesson 05 you learned Go's function model. Now we reach the most important lesson in the module: **structs and interfaces**. Go has no classes, no inheritance, and no `implements` keyword. Instead, it has **structs** (for data) and **interfaces** (for behavior), and the relationship between them is implicit — a struct satisfies an interface just by having the right methods, with no declaration anywhere.

This is the single biggest mental shift for developers coming from Java, Python, or C#. Take your time with this lesson — every Go program you ever write will use structs and interfaces, so the concepts need to be solid.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define **structs** with `type Person struct { ... }` and create instances with the field-name syntax `Person{Name: "...", Age: 25}`.
2. Define **methods** on structs using receivers — both value receivers `(p Person)` and pointer receivers `(p *Person)`.
3. Use **embedding** for composition — Go's replacement for inheritance.
4. Define **interfaces** and understand that satisfaction is **implicit** (no `implements` keyword).
5. Use the empty interface `interface{}` (now aliased as `any`) and **type assertions** / **type switches**.
6. Read and write basic **generics** (Go 1.18+) with type parameters.

---

## 1. Structs

Go has **no classes**. Instead, it has **structs** — a way to group named fields of different types into a single value. A struct is similar to a Python `dataclass` or a C `struct`: it is just data, no behavior. Behavior is added separately via methods (Section 2).

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    p := Person{
        Name: "Hieu",
        Age:  25,
    }
    fmt.Println(p.Name, p.Age) // Hieu 25
    p.Age = 26
    fmt.Println(p) // {Hieu 26}
}
```

- `type Person struct { ... }` — declares a new **named type** called `Person` whose underlying type is a struct. The `type` keyword is how Go introduces named types — you will see it again for interfaces.
- `Name string` and `Age int` — two **fields**. Field names are capitalized here because we want them **exported** (public — accessible from other packages). Lowercase field names would be unexported (private to the package).
- `p := Person{ Name: "Hieu", Age: 25 }` — create a `Person` value using the **field-name syntax**. This is the most common form, because it is self-documenting and order-independent.
- `p.Name` — access a field with the dot operator. You can read and (if the struct is mutable) write to fields this way.

You can also create a struct without naming the fields, in which case the values are assigned in declaration order:

```go
p := Person{"Hieu", 25} // positional — works but discouraged for readability
```

The positional form is shorter but fragile: if someone reorders the fields in the struct definition, this code silently breaks. The field-name form is preferred.

A struct with no fields is valid and occasionally useful as a "tag" type:

```go
type Empty struct{}
e := Empty{}
```

`Empty{}` consumes zero bytes at runtime — it is used to make sets (a `map[string]struct{}` is the idiomatic Go set) and to mark types as "having behavior but no state."

---

## 2. Methods on Structs

A **method** is a function with a special **receiver** argument, written between `func` and the method name. The receiver is the value the method is called on — Go's equivalent of `this` in Java or `self` in Python.

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

// Value receiver: works on a copy of p
func (p Person) Greet() string {
    return "Hi, I'm " + p.Name
}

// Pointer receiver: works on the original p, can modify it
func (p *Person) HaveBirthday() {
    p.Age++
}

func main() {
    p := Person{Name: "Hieu", Age: 25}
    fmt.Println(p.Greet()) // Hi, I'm Hieu

    p.HaveBirthday()       // works on &p (Go auto-takes the address)
    fmt.Println(p.Age)     // 26 — the original was modified
}
```

- `func (p Person) Greet() string` — the receiver `(p Person)` appears between `func` and the method name. This means `Greet` is a method on `Person`. Inside the method, `p` refers to the value the method was called on.
- `func (p *Person) HaveBirthday()` — the receiver is `(p *Person)`, a **pointer**. This is called a pointer receiver. Methods with pointer receivers can **modify** the original struct, because `p` is a reference to it.
- `p.Greet()` — call the value-receiver method. Go copies `p` and calls `Greet` on the copy.
- `p.HaveBirthday()` — call the pointer-receiver method. Even though `p` is a value (not a pointer), Go automatically takes its address (`(&p).HaveBirthday()`) so the method can modify it.

**The rule of thumb:** use a pointer receiver when the method needs to **modify** the struct, or when the struct is **large** (so copying it would be expensive). Use a value receiver when the method only **reads** the struct and the struct is small. Be consistent within a type — if one method has a pointer receiver, all methods on that type should usually have a pointer receiver, to avoid confusion about when copies happen.

```text
                  can modify original?     copies the struct?
(p Person)        no                       yes
(p *Person)       yes                      no
```

---

## 3. Embedding (Composition over Inheritance)

Go has **no inheritance**. There is no `extends`, no superclass, no virtual methods. Instead, Go uses **embedding** — a struct can be embedded inside another struct, and the embedded struct's fields and methods are "promoted" to the outer struct. This is Go's version of composition.

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func (p Person) Greet() string {
    return "Hi, I'm " + p.Name
}

type Employee struct {
    Person        // embedded — no field name, just the type
    Title string
}

func main() {
    e := Employee{
        Person: Person{Name: "Hieu", Age: 25},
        Title:  "Engineer",
    }
    fmt.Println(e.Name)       // Hieu — Person.Name promoted
    fmt.Println(e.Greet())    // Hi, I'm Hieu — Person.Greet promoted
    fmt.Println(e.Title)      // Engineer
}
```

- `type Employee struct { Person; Title string }` — `Employee` embeds `Person`. Notice we write only the type name (`Person`), with no field name. The field name is implicitly `Person`.
- `e.Name` — the `Person.Name` field is **promoted** to `Employee`. You can write `e.Name` directly instead of `e.Person.Name`.
- `e.Greet()` — the `Person.Greet()` method is **promoted** to `Employee`. You can call it on `e` as if `Employee` had defined it.
- `e.Title` — `Title` is a normal field of `Employee`.

Embedding looks like inheritance at first glance — `Employee` "is a" `Person`, and you can call `Person` methods on it. But it is **composition**, not inheritance: there is no polymorphism, no method overriding (you can shadow a method by defining one with the same name on the outer type, but there is no `super.method()` to call the embedded version), and no class hierarchy. The Go team's view is that **inheritance hierarchies become rigid over time** — they are hard to refactor once they grow deep, and small changes at the top of the hierarchy ripple unpredictably through all subclasses. Embedding is more flexible: you can add or remove an embedded type without affecting anything else.

A practical example: every Go HTTP handler has the form:

```go
type UserHandler struct {
    *sql.DB            // embedded — gives UserHandler direct access to DB methods
    Logger *log.Logger // normal field
}

func (h *UserHandler) GetUser(w http.ResponseWriter, r *http.Request) {
    // h.Exec(...)  — promoted from *sql.DB, so we can call DB methods directly
}
```

The `*sql.DB` is embedded, so `UserHandler` has direct access to all of `sql.DB`'s methods (`Query`, `Exec`, `Begin`, etc.) without going through a field name. This is the Go idiom for "delegating to a dependency."

---

## 4. Interfaces

An **interface** is a set of method signatures. A type **satisfies** an interface if it has all the methods the interface requires. The remarkable thing about Go is that satisfaction is **implicit**: you never write `implements` anywhere. The struct just happens to have the right methods, and the compiler figures out the rest.

```go
package main

import "fmt"

type Stringer interface {
    String() string
}

type Person struct {
    Name string
    Age  int
}

// Person satisfies Stringer because it has a String() string method
func (p Person) String() string {
    return fmt.Sprintf("%s (%d)", p.Name, p.Age)
}

func describe(s Stringer) {
    fmt.Println(s.String())
}

func main() {
    p := Person{Name: "Hieu", Age: 25}
    describe(p) // Hieu (25)
}
```

- `type Stringer interface { String() string }` — declares an interface with one method. Any type that has a `String() string` method satisfies `Stringer`.
- `func (p Person) String() string` — defines the `String` method on `Person`. This is just a normal method definition; nothing about it references `Stringer`.
- `func describe(s Stringer)` — takes a `Stringer` as argument. We can pass any value that satisfies `Stringer` — including `Person`, but also any other type we ever define that has a `String() string` method.
- `describe(p)` — `p` is a `Person`, and `Person` satisfies `Stringer`, so we can pass it where `Stringer` is expected. The compiler checks this for us.

**Implicit satisfaction is the key Go insight.** In Java, you write `class Person implements Stringer { ... }` — the relationship is declared at the type definition. In Go, you can define an interface in one package and satisfy it with a type defined in another package, **without either side knowing about the other**. This is incredibly powerful for writing decoupled code: a library that defines `Stringer` never needs to be modified when you write a new type that happens to satisfy it.

Here is a more realistic example with two types satisfying the same interface:

```go
package main

import "fmt"

type Shape interface {
    Area() float64
}

type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return 3.14159 * c.Radius * c.Radius
}

func printArea(s Shape) {
    fmt.Printf("area: %.2f\n", s.Area())
}

func main() {
    printArea(Rectangle{Width: 3, Height: 4}) // area: 12.00
    printArea(Circle{Radius: 5})              // area: 78.54
}
```

`Rectangle` and `Circle` both satisfy `Shape` because both have an `Area() float64` method. The `printArea` function takes a `Shape` and works for any type that satisfies it. Notice that we never wrote `Rectangle implements Shape` anywhere — the compiler verifies it for us at the call site.

The Go standard library uses interfaces everywhere. The most famous are `io.Reader` (with method `Read(p []byte) (n int, err error)`) and `io.Writer` (with method `Write(p []byte) (n int, err error)`). Files, network connections, in-memory buffers, and encrypted streams all satisfy these interfaces, which is why a single function like `io.Copy(dst, src)` can copy from any source to any destination. The size of the standard library's `io` package is tiny — under 1,000 lines — because it just defines interfaces and a few helper functions. The power comes from how many types satisfy those interfaces.

---

## 5. The Empty Interface `interface{}` (now `any`)

Go has one special interface: the **empty interface**, declared as `interface{}` with no methods. Since it requires no methods, **every type satisfies it**. Before generics were added in Go 1.18, the empty interface was Go's only way to write code that works with "any type" — the equivalent of `Object` in Java or `any` in TypeScript.

```go
package main

import "fmt"

func printAnything(v interface{}) {
    fmt.Println(v)
}

func main() {
    printAnything(42)
    printAnything("hello")
    printAnything([]int{1, 2, 3})
}
```

In Go 1.18+, the empty interface got a friendlier alias: **`any`**. The two are identical — `any` is just `interface{}` renamed for readability. Modern Go code uses `any`:

```go
func printAnything(v any) {
    fmt.Println(v)
}
```

`any` is great for generic containers (`map[string]any` for untyped JSON, function arguments that truly accept anything), but it is also **a code smell** in production Go. The reason: once you accept `any`, the compiler can no longer help you. You have to do **type assertions** (Section 6) to recover the underlying value, and a typo in the type name will not be caught until runtime. Use `any` only when you genuinely cannot use a more specific type or a generic.

---

## 6. Type Assertions and Type Switches

When you have an interface value (especially an `any`), you sometimes need to get the underlying concrete type back. This is called a **type assertion**:

```go
package main

import "fmt"

func main() {
    var i any = "hello"

    s, ok := i.(string) // type assertion: "i is a string"
    fmt.Println(s, ok)  // hello true

    n, ok := i.(int)    // i is NOT an int
    fmt.Println(n, ok)  // 0 false (zero value, false)
}
```

- `s, ok := i.(string)` — the **type assertion** form with the comma-ok pattern. If `i` actually holds a `string`, `s` gets the string value and `ok` is `true`. If not, `s` gets the zero value of the asserted type (`""`) and `ok` is `false`.
- `n, ok := i.(int)` — `i` is a string, not an int, so `n` is `0` (the zero value of `int`) and `ok` is `false`.

Without the comma-ok pattern, a failed assertion **panics**:

```go
s := i.(string) // panics if i is not a string
```

Use the comma-ok form whenever you are not 100% sure of the type. Use the single-value form only when a wrong type would be a programming bug worth crashing for.

When you need to check multiple types, use a **type switch** — a cleaner form of the chained if/else type assertion:

```go
package main

import "fmt"

func describe(i any) {
    switch v := i.(type) {
    case string:
        fmt.Printf("string of length %d: %q\n", len(v), v)
    case int:
        fmt.Printf("int: %d (squared: %d)\n", v, v*v)
    case []int:
        fmt.Printf("slice of %d ints: %v\n", len(v), v)
    default:
        fmt.Printf("unknown type: %T\n", v)
    }
}

func main() {
    describe("hello")
    describe(7)
    describe([]int{1, 2, 3})
    describe(3.14)
}
```

- `switch v := i.(type)` — special form of `switch` for type checking. The variable `v` is assigned the value of `i` with its concrete type inside each case.
- `case string:` — if `i` is a `string`, `v` is a `string` inside this case body.
- `case int:` — if `i` is an `int`, `v` is an `int` here.
- `default:` — fallback for types we did not list.

Type switches are the idiomatic way to handle a value of unknown type. They are common in code that processes JSON (where the type of a field is not known in advance) and in plugin systems where different plugins have different concrete types but share an interface.

---

## 7. Generics (Go 1.18+)

For the first 14 years of Go's life, there were no generics. If you wanted a `Map` function that worked on `[]int` and `[]string`, you wrote two functions, or you used `interface{}` and lost type safety. Generics were finally added in **Go 1.18 (March 2022)** — the biggest language change since 1.0.

A generic function has **type parameters** in square brackets:

```go
package main

import (
    "fmt"
    "strings"
)

func Map[T, U any](in []T, f func(T) U) []U {
    out := make([]U, len(in))
    for i, v := range in {
        out[i] = f(v)
    }
    return out
}

func main() {
    nums := []int{1, 2, 3, 4}
    doubled := Map(nums, func(n int) int { return n * 2 })
    fmt.Println(doubled) // [2 4 6 8]

    words := []string{"go", "rust", "python"}
    uppercased := Map(words, func(s string) string {
        return strings.ToUpper(s)
    })
    fmt.Println(uppercased) // [GO RUST PYTHON]
}
```

- `func Map[T, U any](in []T, f func(T) U) []U` — `Map` is generic over two types, `T` and `U`. The `[T, U any]` is the **type parameter list** — `any` is the constraint, meaning `T` and `U` can be any type.
- `in []T` — input slice of `T`.
- `f func(T) U` — a function that takes a `T` and returns a `U`.
- `[]U` — return type: slice of `U`.
- `Map(nums, func(n int) int { return n * 2 })` — the compiler infers `T=int, U=int` from the arguments. No need to write `Map[int, int](nums, ...)`.
- `Map(words, func(s string) string { ... })` — different call, different types — `T=string, U=string`. Same function, type-safe at compile time.

Generics are most useful for **data structures** (generic stacks, queues, trees) and **algorithm functions** (filter, map, reduce, sort). The Go team added them conservatively — the syntax is minimal, and the standard library has not been retrofitted to use generics everywhere. For most application code, you will still use concrete types and the `any` interface when you need flexibility.

A constraint is an interface that limits which types can be used. The standard library provides a `constraints` package (now in `cmp`) with constraints like `Ordered` (types that support `<`, `>`, `<=`, `>=`):

```go
func Max[T cmp.Ordered](xs []T) T {
    var m T
    for i, v := range xs {
        if i == 0 || v > m {
            m = v
        }
    }
    return m
}
```

`cmp.Ordered` is satisfied by all types that support ordering comparisons — `int`, `float64`, `string`, and so on. This lets you write `Max([]int{...})` and `Max([]string{...})` with the same function, type-safely.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Build a Struct with Methods

Define a `Rectangle` struct with `Width` and `Height` (both `float64`). Add two methods: `Area()` (returns `Width * Height`) and `Perimeter()` (returns `2 * (Width + Height)`). Use a **value receiver** for both — they only read the struct. Create a `Rectangle{Width: 3, Height: 4}` and print its area and perimeter. The area should be 12 and the perimeter should be 14.

### Exercise 2: Define an Interface and Two Implementations

Define an `Animal` interface with one method: `Sound() string`. Implement it with two struct types: `Dog` (which prints "Woof") and `Cat` (which prints "Meow"). Write a function `makeSound(a Animal)` that calls `a.Sound()` and prints the result. Call it with both `Dog{}` and `Cat{}` — note that you never write `implements Animal` anywhere; the compiler figures it out from the method sets.

### Exercise 3: Write a Generic Function

Write a generic function `Filter[T any](xs []T, pred func(T) bool) []T` that returns a new slice containing only the elements of `xs` for which `pred` returns `true`. Use it to filter even numbers out of `[]int{1, 2, 3, 4, 5, 6}` (the predicate is `func(n int) bool { return n%2 == 0 }`). Then use it to filter short strings out of `[]string{"go", "rust", "c", "python"}` (predicate: `func(s string) bool { return len(s) <= 2 }`). Confirm that the function is type-safe — passing a string predicate to an int slice is a compile error, not a runtime error.

---

## Common Mistakes

### Mistake 1: Using a value receiver when you need to mutate

```go
// WRONG — value receiver means HaveBirthday operates on a copy
package main

import "fmt"

type Counter struct {
    N int
}

func (c Counter) Increment() {
    c.N++  // increments the copy, original is untouched
}

func main() {
    c := Counter{}
    c.Increment()
    fmt.Println(c.N) // 0 — original was not modified
}
```

```go
// RIGHT — pointer receiver means HaveBirthday operates on the original
package main

import "fmt"

type Counter struct {
    N int
}

func (c *Counter) Increment() {
    c.N++  // increments the original
}

func main() {
    c := Counter{}
    c.Increment()
    fmt.Println(c.N) // 1 — original was modified
}
```

A method with a **value receiver** `(c Counter)` receives a **copy** of the struct. Any modifications inside the method are lost when the method returns. If you need to mutate the original, use a **pointer receiver** `(c *Pointer)` — the method receives a pointer to the original, and modifications persist. The rule of thumb: methods that need to modify the struct use pointer receivers; methods that only read can use either. Be consistent within a type — mix-and-match leads to bugs and is forbidden by some linters.

### Mistake 2: Trying to inherit from a struct

```go
// WRONG — Go has no `extends` keyword
package main

import "fmt"

type Animal struct {
    Name string
}

type Dog extends Animal {  // compile error: Go has no `extends`
    Breed string
}
```

```go
// RIGHT — use embedding (composition)
package main

import "fmt"

type Animal struct {
    Name string
}

type Dog struct {
    Animal      // embedded — Animal's fields and methods are promoted
    Breed string
}

func main() {
    d := Dog{
        Animal: Animal{Name: "Rex"},
        Breed:  "Labrador",
    }
    fmt.Println(d.Name)  // Rex — promoted from Animal
    fmt.Println(d.Breed) // Labrador
}
```

Go has **no inheritance**. There is no `extends` keyword, no superclass concept, no virtual methods. If you write `type Dog extends Animal`, the compiler stops with `syntax error`. The Go replacement is **embedding** — write `Animal` (with no field name) inside `Dog`'s struct definition. The embedded type's fields and methods are promoted to the outer type, so `d.Name` works even though `Name` was declared on `Animal`. This is composition, not inheritance: there is no class hierarchy, no `super`, no overriding. If you want to "override" a method, you define one with the same name on the outer type, and the outer method takes precedence — but the embedded method is still accessible via `d.Animal.MethodName()`.

### Mistake 3: Forgetting that interface satisfaction is implicit

```go
// SUBTLE — you might think the file is wrong, but it is correct
package main

type Greeter interface {
    Greet() string
}

// Person satisfies Greeter because it has a Greet() string method.
// There is no declaration of the relationship anywhere.
type Person struct {
    Name string
}

func (p Person) Greet() string {
    return "Hi, I'm " + p.Name
}
```

```go
// RIGHT — but easy to break during refactoring
package main

import "fmt"

type Greeter interface {
    Greet() string
}

type Person struct {
    Name string
}

// If you rename this to Greeting (or change the signature), Person
// silently stops satisfying Greeter, and the compile error appears
// at the call site, not here.
func (p Person) Greet() string {
    return "Hi, I'm " + p.Name
}

func sayHi(g Greeter) {
    fmt.Println(g.Greet())
}
```

In Java, the type explicitly declares `implements Greeter`, so if you remove the method the compiler points at the type definition. In Go, the relationship is implicit — the compiler verifies it only at the call site. This means **refactoring can break interface satisfaction in ways the compiler does not flag at the definition site**. If you rename `Greet` to `Greeting` on `Person`, every place that expects a `Greeter` will fail to compile (which is good), but the error message points at those call sites, not at `Person`. The fix is to either keep the method name, or to use a compile-time assertion like `var _ Greeter = Person{}` at the package level — this fails fast if `Person` ever stops satisfying `Greeter`.

### Mistake 4: Comparing structs with `==` when fields are not comparable

```go
// WRONG — struct with a slice field is not comparable
package main

import "fmt"

type Person struct {
    Name    string
    Friends []string // slices are not comparable
}

func main() {
    p1 := Person{Name: "Hieu", Friends: []string{"A", "B"}}
    p2 := Person{Name: "Hieu", Friends: []string{"A", "B"}}
    fmt.Println(p1 == p2) // compile error: struct containing []string cannot be compared
}
```

```go
// RIGHT — compare field by field, or use reflect.DeepEqual
package main

import (
    "fmt"
    "reflect"
)

type Person struct {
    Name    string
    Friends []string
}

func main() {
    p1 := Person{Name: "Hieu", Friends: []string{"A", "B"}}
    p2 := Person{Name: "Hieu", Friends: []string{"A", "B"}}
    fmt.Println(reflect.DeepEqual(p1, p2)) // true
}
```

Go allows `==` on structs **only if all their fields are comparable**. `int`, `string`, `bool`, pointers, and arrays of comparable types are comparable. Slices, maps, and functions are **not** comparable (their `==` would have to compare contents, which Go decided not to do automatically because it can be expensive or ambiguous). If your struct has a slice or map field, `==` on the struct is a compile error. To compare two such structs for value equality, use `reflect.DeepEqual(p1, p2)`, which compares recursively. The trade-off: `reflect.DeepEqual` is slower than `==` and is not constant-time, so it is unsuitable for cryptographic comparisons.

---

## Summary

- Go has **no classes** — use **structs** (`type Person struct { Name string; Age int }`) to group data.
- **Methods** are functions with a receiver (`func (p Person) Greet() string`); use a value receiver `(p Person)` for read-only methods, a pointer receiver `(p *Person)` for methods that modify the struct.
- Go has **no inheritance** — use **embedding** (a struct field with no name, just a type) for composition. Embedded fields and methods are promoted.
- **Interfaces** are sets of method signatures (`type Shape interface { Area() float64 }`); a type satisfies an interface if it has all the required methods.
- Satisfaction is **implicit** — there is no `implements` keyword. This makes Go interfaces extremely flexible but can surprise you during refactoring.
- The empty interface `interface{}` (alias `any` in Go 1.18+) is satisfied by every type; use it sparingly because it loses type safety.
- **Type assertions** (`v, ok := i.(string)`) recover the concrete type from an interface; **type switches** (`switch v := i.(type)`) handle multiple cases.
- **Generics** (Go 1.18+) use type parameters in square brackets (`func Map[T, U any](...)`) for type-safe reusable code.
- `==` on structs works **only if all fields are comparable** — slices, maps, and functions are not. Use `reflect.DeepEqual` for deep comparison.

You now understand Go's approach to data and behavior. In Lesson 07 we will summarize everything you have learned in a printable cheatsheet you can keep next to you while you write Go code.

---

**Next:** [Lesson 07: Go Cheatsheet →](./07-go-cheatsheet.md)
