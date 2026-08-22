# Lesson 05: Functions and Generics

> Module: TypeScript · Lesson 5 of 7
> Estimated time: 30–45 minutes

Functions are the building blocks of any program, and TypeScript gives you precise tools for describing them: function types for signatures, optional and default parameters for flexibility, rest parameters for variadic functions, function overloads for multiple call signatures, and generics for writing reusable, type-safe code that works with any type. By the end of this lesson you will be fluent in all of them.

If you completed the Python or Rust modules, function declarations will feel familiar — parameters with type annotations, return type after the parameter list. The TypeScript-specific parts are function types (a way to describe the *type* of a function as a value), overloads (multiple signatures for one function), and generics (the headline feature that lets you write `Array<T>`, `Promise<T>`, `Map<K, V>`).

---

## Learning Objectives

After this lesson, you will be able to:

1. Write function types, function declarations, and arrow functions with proper TypeScript annotations.
2. Use optional, default, and rest parameters correctly (and in the right order).
3. Write function overloads for functions with multiple call signatures.
4. Write generic functions, constrain generics with `extends`, and provide default type arguments.

---

## 1. Function Types

A **function type** describes the type of a function as a value — its parameter types and return type. Function types are useful when you want to type a variable, parameter, or property that holds a function:

```typescript
let greet: (name: string) => string;

greet = function (name: string) {
  return "Hello, " + name;
};

greet = (name: string) => "Hi, " + name;   // also OK

console.log(greet("Hieu"));   // "Hi, Hieu"
```

Let's break this down. Line 1 declares a variable `greet` whose type is `(name: string) => string`. The syntax `(name: string) => string` is a **function type literal** — it says "a function that takes one parameter named `name` of type `string` and returns a `string`". The parameter name (`name`) is documentation only — TypeScript does not enforce that callers use that name. The arrow `=>` separates the parameter list from the return type.

Once `greet` has this type, you can assign any function to it that matches the signature. Line 3 assigns a regular function declaration — its parameter is `string`, its return is `string`, so it matches. Line 6 assigns an arrow function — same signature, so it matches. Either way, calling `greet("Hieu")` returns a string.

Function types are most useful when you want to describe the shape of a callback. For example, the `Array.prototype.map` method takes a function type `(value: T, index: number, array: T[]) => U`. When you write `numbers.map(n => n * 2)`, TypeScript knows that `n` is a number because `map`'s callback parameter is typed `(value: number) => number` (the array's element type is `number`).

Function types can also be aliased with `type` for reuse:

```typescript
type GreetFn = (name: string) => string;
type Callback = (data: string) => void;

function run(cb: Callback) {
  cb("hello");
}
```

The `type GreetFn = (name: string) => string` form lets you reuse the function type in multiple places without repeating the full signature.

---

## 2. Function Declarations

A **function declaration** is the classic way to define a named function. TypeScript adds parameter and return type annotations:

```typescript
function add(a: number, b: number): number {
  return a + b;
}

const result: number = add(2, 3);
console.log(result);   // 5
```

Let's break this down. `function add` declares a function named `add`. `(a: number, b: number)` declares two parameters: `a` of type `number` and `b` of type `number`. The `: number` after the closing parenthesis is the **return type annotation** — the function returns a `number`. The body `{ return a + b; }` returns the sum.

When the parameter types are the same, you can use the shared-type shortcut (also seen in Go):

```typescript
function add(a: number, b: number): number { ... }   // verbose
function add(a, b: number): number { ... }            // WRONG — a has implicit any
function add(a: number, b: number): number { ... }    // CORRECT (Go-style shortcut doesn't apply)
```

Note: unlike Go, TypeScript does *not* have a shared-type shortcut. In Go, `func add(a, b int)` works — both `a` and `b` are `int`. In TypeScript, you must annotate each parameter separately: `(a: number, b: number)`. The `(a, b: number)` form would give `a` the type `any` (with `strict: true`, that is a compile error).

The return type annotation is optional — TypeScript can infer it from the body. But explicit return types are recommended for public functions because they make the function's contract obvious and protect you from accidentally changing the return type during a refactor.

---

## 3. Arrow Functions

Arrow functions (also called "fat arrow functions" or "lambdas") are a more concise way to write function expressions. They have the same type annotations as regular functions:

```typescript
const add = (a: number, b: number): number => {
  return a + b;
};

const double = (x: number): number => x * 2;   // implicit return

const greet = (name: string): string => `Hello, ${name}`;   // template literal return
```

Let's break this down. `(a: number, b: number)` is the parameter list — same syntax as function declarations. `: number` after the closing paren is the return type (optional but recommended). `=>` separates the parameter list from the body. The body can be either a block `{ return ...; }` or a single expression `x * 2` (the latter is an "expression body" with implicit return).

Arrow functions differ from regular functions in one important way: they do not have their own `this` binding. Inside a regular function, `this` refers to the object the function was called on (which can change depending on the call site). Inside an arrow function, `this` refers to the enclosing scope's `this` (lexically captured). This matters mainly when writing methods on classes or objects — we will revisit it in a later module. For most function-type declarations, the choice between `function` and arrow is stylistic.

For one-liner functions, the expression body is concise:

```typescript
const isEven = (n: number): boolean => n % 2 === 0;
const square = (x: number): number => x * x;
const greet = (name: string): string => `Hello, ${name}`;
```

---

## 4. Optional and Default Parameters

Function parameters can be **optional** (marked with `?`) or have **default values**:

```typescript
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}`;
}

console.log(greet("Hieu"));              // "Hello, Hieu" — greeting defaults to "Hello"
console.log(greet("Hieu", "Hi"));        // "Hi, Hieu"
console.log(greet("Hieu", undefined));    // "Hello, Hieu" — explicit undefined uses default
```

Let's break this down. `greeting: string = "Hello"` declares a parameter named `greeting` with type `string` and default value `"Hello"`. If the caller does not provide `greeting` (or explicitly passes `undefined`), the parameter takes the default value. The type of `greeting` is `string` (not `string | undefined`) because the default guarantees a value.

Optional parameters use the `?` modifier:

```typescript
function greet(name: string, title?: string): string {
  if (title) {
    return `Hello, ${title} ${name}`;
  }
  return `Hello, ${name}`;
}
```

`title?: string` declares `title` as an optional string. The type of `title` inside the function is `string | undefined` — you must narrow it (with an `if (title)` check, as above) before using it as a string.

**Required parameters must come before optional and default parameters.** This is enforced by the compiler:

```typescript
function f(a: string, b?: number, c: boolean) { ... }   // COMPILE ERROR
function f(a: string, b: number, c?: boolean) { ... }    // OK
```

The reason is positional: if `c` is required but `b` is optional, calling `f("x", 5)` would be ambiguous — does `5` go to `b` or `c`? TypeScript avoids this by requiring required parameters first, then optional/default ones.

---

## 5. Rest Parameters

A **rest parameter** lets a function accept any number of arguments, collected into an array. Use the `...` prefix:

```typescript
function sum(...nums: number[]): number {
  return nums.reduce((total, n) => total + n, 0);
}

console.log(sum(1, 2, 3));           // 6
console.log(sum(1, 2, 3, 4, 5));     // 15
console.log(sum());                   // 0 — no arguments is OK
```

Let's break this down. `...nums: number[]` declares a rest parameter named `nums` of type `number[]`. Inside the function, `nums` is a regular array — you can call `.reduce`, `.map`, `.filter`, and any array method on it. Every argument passed after the previous parameters is collected into `nums`. `sum(1, 2, 3)` makes `nums` equal to `[1, 2, 3]`. `sum()` makes `nums` an empty array.

Rest parameters must be the **last** parameter in the function:

```typescript
function f(...nums: number[], last: string) { ... }   // COMPILE ERROR
function f(last: string, ...nums: number[]) { ... }    // OK
```

Rest parameters pair well with the spread operator at the call site:

```typescript
const nums = [1, 2, 3, 4, 5];
console.log(sum(...nums));   // 15 — spread the array into individual arguments
```

The `...nums` at the call site "spreads" the array into individual arguments, which are then collected back into `nums` by the rest parameter. This is a no-op semantically but the round trip through spread/rest is a common pattern.

---

## 6. Function Overloads

Some functions can be called with different parameter types and return different types depending on what was passed. **Function overloads** let you describe each call signature separately:

```typescript
function parse(input: string): string;
function parse(input: number): number;
function parse(input: string | number): string | number {
  return input;
}

const s: string = parse("hello");   // returns string
const n: number = parse(42);        // returns number
```

Let's break this down carefully because the syntax is unusual. The first two lines are **overload signatures** — they describe how the function can be called. `function parse(input: string): string;` says "when called with a string, returns a string". `function parse(input: number): number;` says "when called with a number, returns a number". These are signatures only — no body.

The third line is the **implementation signature** — `function parse(input: string | number): string | number { return input; }`. This is the actual function. It must be compatible with all overload signatures: every input the overloads accept (string OR number, so `string | number`) must be accepted, and every output the overloads produce must be a possible output (`string | number` covers it).

The implementation signature is **not visible to callers**. Callers see only the two overload signatures. So `parse(true)` (boolean) is a compile error — neither overload accepts a boolean. And `const x: string = parse(42)` is a compile error — when called with a number, the return type is `number`, not `string`.

Overloads are most useful for utility functions that have meaningful behavior differences based on input types. A common example is `document.getElementById` — it returns `HTMLElement` by default, but if you do `document.getElementById("x") as HTMLInputElement`, you get a more specific type. Overloads let you encode this in the function's type without runtime assertions.

Without overloads, you would have to write `function parse(input: string | number): string | number` — but then the return type is always `string | number`, which is less precise. Callers would have to narrow the return value themselves. Overloads move that narrowing into the function signature, where the compiler can do it for you.

---

## 7. Generics

**Generics** are TypeScript's headline feature. They let you write functions, classes, and types that work with **any type** while preserving type safety — the same code works for `number`, `string`, `Person`, or anything else, with the compiler tracking the specific type at each call site.

The classic example is the **identity function** — a function that returns whatever you pass in:

```typescript
function identity<T>(x: T): T {
  return x;
}

const n: number = identity(42);          // T is inferred as number
const s: string = identity("hello");     // T is inferred as string
const p: Person = identity(person);      // T is inferred as Person
```

Let's break this down. `<T>` after the function name declares a **type parameter** named `T`. Type parameters are placeholders for actual types that will be filled in when the function is called. The function takes a parameter `x: T` and returns `T`. When you call `identity(42)`, TypeScript infers `T = number`, and the function effectively becomes `function identity(x: number): number`. When you call `identity("hello")`, `T = string` and the function effectively becomes `function identity(x: string): string`. The same source code works for any type, with the compiler tracking the specific type at each call site.

Generics are how built-in types like `Array<T>`, `Promise<T>`, `Map<K, V>`, and `Set<T>` work. Each is a generic type with one or more type parameters that the caller fills in. `Array<number>` means "an array of numbers"; `Array<string>` means "an array of strings". The same `Array` class works for both — `T` is the placeholder.

A more practical example is a `first` function that returns the first element of an array, preserving the element type:

```typescript
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const n: number | undefined = first([1, 2, 3]);        // T is number
const s: string | undefined = first(["a", "b"]);        // T is string
const p: Person | undefined = first([person1, person2]); // T is Person
```

Without generics, you would have to write either `function first(arr: any[]): any` (loses type safety — the return type is `any`) or one `first` function per type (`firstNumber`, `firstString`, `firstPerson` — verbose and not reusable). Generics give you one function that works for any type with full type safety.

You can have multiple type parameters:

```typescript
function pair<K, V>(key: K, value: V): [K, V] {
  return [key, value];
}

const p: [string, number] = pair("age", 25);   // K is string, V is number
```

`<K, V>` declares two type parameters. The function takes a key of type `K` and a value of type `V`, and returns a tuple `[K, V]`. The return type is constructed from the type parameters — this is how generics compose.

---

## 8. Generic Constraints

Sometimes you need to constrain a generic type parameter to a subset of types — for example, "any type that has a `length` property". Use the `extends` keyword in the type parameter declaration:

```typescript
function len<T extends { length: number }>(x: T): number {
  return x.length;
}

console.log(len("hello"));       // 5 — string has .length
console.log(len([1, 2, 3]));     // 3 — array has .length
console.log(len({ length: 10 })); // 10 — object with length property
// console.log(len(42));          // COMPILE ERROR — number has no .length
```

Let's break this down. `<T extends { length: number }>` declares a type parameter `T` that must be a subtype of `{ length: number }` — i.e., it must have a `length` property of type `number`. Inside the function, you can safely access `x.length` because the constraint guarantees `T` has that property. When you call `len("hello")`, TypeScript checks: does `string` satisfy `{ length: number }`? Yes, strings have a `.length` property of type `number`. When you call `len(42)`, TypeScript checks: does `number` satisfy `{ length: number }`? No — numbers do not have a `.length` property. Compile error.

Constraints let you write generic functions that use specific properties of their type parameters. Without a constraint, you cannot access any property of `T` — the compiler does not know what `T` has.

You can also constrain one type parameter to another:

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "Hieu", age: 25 };
console.log(getProperty(person, "name"));   // "Hieu" — return type is string
console.log(getProperty(person, "age"));    // 25 — return type is number
// console.log(getProperty(person, "email"));  // COMPILE ERROR — "email" is not a key of person
```

`<T, K extends keyof T>` declares two type parameters where `K` must be one of the keys of `T`. The return type `T[K]` is the type of the property at key `K` in `T` — a **indexed access type**. This pattern is how `Object.keys` and similar utility functions are typed.

---

## 9. Generic Defaults

Type parameters can have default values, just like regular parameters:

```typescript
function create<T = string>(): T[] {
  return [];
}

const strings: string[] = create();          // T defaults to string
const numbers: number[] = create<number>();  // T is explicitly number
```

`<T = string>` declares a type parameter `T` with default `string`. If the caller does not provide a type argument, `T` is `string`. If the caller provides one explicitly (`<number>`), `T` is that type. Generic defaults are useful when a type parameter has a "natural" default that most callers will use.

Multiple type parameters can have defaults, but only if all type parameters to their right also have defaults (just like regular default parameters):

```typescript
function f<T = string, U = number>(): [T, U] { ... }   // OK
function f<T, U = number>(): [T, U] { ... }              // OK — T has no default, U does
// function f<T = string, U>(): [T, U] { ... }              // COMPILE ERROR — U must have a default
```

Generic defaults are common in third-party libraries where a type parameter has multiple possible shapes but most callers want the standard one.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Write a Function Type

Declare a `type Comparator<T> = (a: T, b: T) => number` (a function that takes two values of type `T` and returns a number indicating order, like the sort callback). Then write `function sort<T>(arr: T[], cmp: Comparator<T>): T[]` that uses `arr.sort(cmp)` and returns the sorted array. Test it with `sort([3, 1, 2], (a, b) => a - b)` and `sort(["c", "a", "b"], (a, b) => a.localeCompare(b))`. Confirm TypeScript accepts both calls and infers the element type correctly.

### Exercise 2: Use Optional, Default, and Rest Together

Write `function formatNames(prefix: string, suffix: string = "...", ...names: string[]): string` that returns `${prefix}${name1}${suffix}${name2}${suffix}...`. Test it with `formatNames(">", " | ", "Hieu", "Anna", "Bob")` (should return `>Hieu | Anna | Bob`) and `formatNames(">", undefined, "Solo")` (should return `>Solo...`). Confirm that all three parameter kinds work together in the right order.

### Exercise 3: Write a Generic Function with a Constraint

Write `function getLongest<T extends { length: number }>(a: T, b: T): T` that returns whichever of `a` or `b` has the longer `.length`. Test it with two strings (`getLongest("hello", "hi")` returns `"hello"`), two arrays (`getLongest([1, 2, 3], [4, 5])` returns `[1, 2, 3]`), and try calling it with a number — confirm TypeScript rejects the call. Notice that the constraint lets you safely use `.length` inside the function without any null checks.

---

## Common Mistakes

### Mistake 1: Forgetting to provide type arguments when they cannot be inferred

```typescript
// WRONG — T cannot be inferred from an empty array
function createArray<T>(): T[] { return []; }
const arr = createArray();
// TypeScript infers T as unknown, and arr is unknown[]
arr.push(5);   // COMPILE ERROR
```

```typescript
// RIGHT — provide the type argument explicitly
const arr = createArray<number>();
arr.push(5);   // OK
```

TypeScript tries to infer type parameters from the function's arguments, but if there are no arguments (or none that constrain the type parameter), inference fails and `T` defaults to `unknown`. The fix is to provide the type argument explicitly at the call site: `createArray<number>()`. The angle brackets right after the function name (before the parentheses) provide the type argument. Always check that your generic function calls actually infer what you expect — hover over the call in VS Code to see what TypeScript inferred.

### Mistake 2: Using the `Function` type

```typescript
// WRONG — Function is too loose; accepts any function, loses parameter info
function run(fn: Function) {
  fn(1, 2, 3);   // OK at compile time, may crash at runtime
}
```

```typescript
// RIGHT — use a specific function type
function run(fn: (a: number, b: number, c: number) => void) {
  fn(1, 2, 3);   // OK — compiler verifies the arguments match
}
```

TypeScript has a `Function` type that means "any function at all". Avoid it — it is the function equivalent of `any`, and for the same reasons: it disables type checking on the function's parameters and return. Use a specific function type instead (`(a: number) => string`), or a named type alias (`type Callback = (a: number) => string`). If you really need to accept any function, use `(...args: any[]) => unknown` — at least that constrains the call shape.

### Mistake 3: Mixing parameter order — optional must come after required

```typescript
// WRONG — required parameter after optional
function f(a?: string, b: number) { ... }
// COMPILE ERROR: A required parameter cannot follow an optional parameter
```

```typescript
// RIGHT — required first, then optional
function f(b: number, a?: string) { ... }
```

TypeScript enforces that required parameters come before optional and default parameters, just like in most other languages. The reason is positional: if `b` is required and `a` is optional, calling `f("x")` would be ambiguous — does `"x"` go to `a` (which is optional) or to `b` (which is required and missing)? The rule prevents this ambiguity. If you find yourself wanting a required parameter after an optional one, the fix is usually to reorder the parameters or to use an object parameter: `function f(opts: { a?: string; b: number })` (an "options object" pattern, very common in modern TypeScript).

### Mistake 4: Overload signatures don't match the implementation

```typescript
// WRONG — implementation signature is too narrow
function f(x: string): string;
function f(x: number): number;
function f(x: string): string {   // COMPILE ERROR — implementation must accept all overloads
  return x;
}
```

```typescript
// RIGHT — implementation accepts the union of all overload inputs
function f(x: string): string;
function f(x: number): number;
function f(x: string | number): string | number {
  return x;
}
```

The implementation signature of an overloaded function must be compatible with every overload signature — it must accept every input the overloads accept and produce every output the overloads produce. If `f(string): string` and `f(number): number` are the overloads, the implementation must accept `string | number` (the union of the inputs) and return `string | number` (the union of the outputs). If the implementation only accepts `string`, TypeScript errors because the `f(x: number)` overload would lie — it claims to accept numbers, but the implementation cannot handle them. The error message looks like `Overload signature is not compatible with its implementation signature.`

---

## Summary

- Function types (`(name: string) => string`) describe the type of a function as a value — useful for callbacks and function-typed variables.
- Function declarations and arrow functions have the same type annotations — `function add(a: number, b: number): number` and `const add = (a: number, b: number): number => ...`.
- Optional parameters use `?` (`name?: string`); default parameters use `=` (`greeting: string = "Hello"`); required must come before optional/default.
- Rest parameters (`...nums: number[]`) collect variadic arguments into an array — must be the last parameter.
- Function overloads let you describe multiple call signatures for one function; the implementation signature must be compatible with all overloads.
- Generics (`<T>`) let you write reusable code that works with any type, with the compiler tracking the specific type at each call site.
- Generic constraints (`<T extends { length: number }>`) restrict a type parameter to types that satisfy a specific shape.
- Generic defaults (`<T = string>`) provide a default type argument for callers who do not specify one.

You now know how to write typed functions and generics. In Lesson 06 we will explore TypeScript's most powerful feature for handling uncertainty: union types, intersection types, literal types, type narrowing through control flow, and the discriminated union pattern that makes complex data modeling safe.

---

**Next:** [Lesson 06: Type Narrowing and Unions →](./06-type-narrowing-and-unions.md)
