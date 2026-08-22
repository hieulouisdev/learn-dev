# Lesson 03: Basic Types

> Module: TypeScript · Lesson 3 of 7
> Estimated time: 30–45 minutes

Now that you have a working TypeScript project, it is time to learn the type system. TypeScript has a small set of primitive types (string, number, boolean, and a few others), a rich set of compound types (arrays, tuples, enums), and a few special types (`any`, `unknown`, `void`, `never`) that each have a specific purpose. By the end of this lesson you will know all of them, when to use each, and which ones to avoid.

If you completed the Rust or Go modules, the primitive types will feel familiar — TypeScript has the same `string`, `number`, and `boolean` you have seen before. The interesting parts are the JavaScript-specific quirks (no separate `int`/`float`, just `number`), the `null`/`undefined` split (JavaScript has both, and TypeScript models them separately), and the careful distinction between `any` and `unknown`.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use all of TypeScript's primitive types correctly: `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`.
2. Declare arrays with both syntaxes (`number[]` and `Array<number>`), tuples, and enums (both numeric and string).
3. Explain the difference between `any` and `unknown`, and why you should prefer `unknown`.
4. Explain the difference between `void` and `never`, and when each is appropriate.

---

## 1. Why Types?

Types are a **contract** between you and the compiler. When you write `let x: number = 5;`, you are promising the compiler that the variable `x` will always hold a number. The compiler's job is to check every place in your code that uses `x` and verify that the contract is honored. If you later write `x = "hello"`, the compiler refuses — that breaks the contract. The benefit is that whole classes of bugs become impossible: a string can never accidentally end up where a number was expected, an object can never be missing a property the code tries to access, a function can never be called with the wrong number of arguments.

The most important thing to understand about TypeScript types is that they are **erased at runtime**. When `tsc` compiles your `.ts` file to `.js`, it removes every type annotation, every interface, every generic parameter. The output JavaScript has no types at all. This means `typeof x` at runtime returns the JavaScript type (one of `"string"`, `"number"`, `"boolean"`, `"undefined"`, `"object"`, `"function"`, `"symbol"`, `"bigint"`) — not the TypeScript type you wrote. A variable declared `let p: Person = { name: "Hieu" };` will return `"object"` from `typeof p` at runtime, not `"Person"`. The `Person` type existed only at compile time.

This is why TypeScript is called "static typing with type erasure" — the opposite of languages like Java or C# where types are reified (preserved at runtime and queryable via reflection). TypeScript's approach has a big advantage: zero runtime overhead, and perfect compatibility with existing JavaScript. It also has a limitation: you cannot check `if (x instanceof Person)` to see if `x` has TypeScript type `Person`, because `Person` is not a runtime value. To check the type of an object at runtime, you must inspect its shape (`"name" in x`) or use a discriminated union (Lesson 06). We will revisit this distinction throughout the module.

---

## 2. Primitive Types

TypeScript has seven primitive types. They correspond directly to the seven primitive types in JavaScript — TypeScript did not invent any new ones.

```typescript
let s: string = "Hello";
let n: number = 42;
let b: boolean = true;
let nothing: null = null;
let undef: undefined = undefined;
let sym: symbol = Symbol("id");
let big: bigint = 100n;
```

Let's go through each one. **`string`** holds text — a sequence of Unicode characters. Strings in JavaScript (and TypeScript) are immutable: methods like `.toUpperCase()` return a new string rather than modifying the original. You can declare strings with single quotes (`'Hello'`), double quotes (`"Hello"`), or backticks (`` `Hello` `` — called **template literals** and covered below). Single and double quotes are equivalent in TypeScript; the convention is to pick one and stick with it (most projects use double quotes).

**`number`** holds all numeric values — there is no separate `int` and `float` type like in C, Java, or Rust. JavaScript has a single numeric type: a 64-bit IEEE 754 floating-point number, the same as `f64` in Rust or `float64` in Go. This means `5` and `5.0` are the same value, and `5 / 2` is `2.5` (not `2` as in integer-only languages). For integers, JavaScript can safely represent any integer between `-2^53 + 1` and `2^53 - 1` (about 9 quadrillion) without precision loss. Beyond that range, you need `bigint`. Hexadecimal (`0xff`), octal (`0o17`), and binary (`0b1010`) literals all produce `number` values.

**`boolean`** holds `true` or `false`. There is no truthiness coercion in TypeScript's type system (unlike JavaScript at runtime, where `0`, `""`, `null`, `undefined`, `NaN`, and `false` are all "falsy"). In a TypeScript `if` condition, you can pass any value (and JavaScript will apply its truthiness rules at runtime), but if `strict` is enabled and you write `if (someVariable)` where `someVariable` is typed as `number`, TypeScript will warn you that the condition will always evaluate to true (since `0` is the only falsy number). For type-safety, prefer explicit comparisons: `if (x !== null)` rather than `if (x)`.

**`null`** and **`undefined`** are two separate types representing two separate concepts in JavaScript. `undefined` means "this variable has no value assigned" or "this property does not exist on the object". `null` means "this variable was intentionally set to no value". They are different values: `null === undefined` is `false`, but `null == undefined` is `true` (the loose equality operator treats them as equal). With `strictNullChecks` enabled (part of `strict: true`), `null` and `undefined` are not assignable to other types — you must explicitly write `string | null` if you want to allow `null`.

**`symbol`** is a unique, immutable identifier created with `Symbol("description")`. Every `Symbol()` call produces a unique value: `Symbol("id") === Symbol("id")` is `false`. Symbols are used as object property keys when you want to ensure no other code can accidentally collide with your property. You will rarely declare a variable of type `symbol` directly; you are more likely to encounter symbols as keys of objects.

**`bigint`** holds integers of arbitrary precision — integers larger than `2^53 - 1`. You declare a bigint with the `n` suffix: `100n` is a bigint, `100` is a regular number. You cannot mix bigint and number in arithmetic operations (`100n + 1` is a type error) — you must convert one to the other first. Bigint is a relatively recent addition to JavaScript (ES2020) and is mainly used in cryptography and financial calculations where precision matters.

**Template literals** are a special form of string that supports embedded expressions. They use backticks:

```typescript
const name: string = "Hieu";
const age: number = 25;
const greeting: string = `Hello, ${name}. You are ${age} years old.`;
console.log(greeting);   // "Hello, Hieu. You are 25 years old."
```

The `${...}` syntax evaluates the expression inside and converts it to a string. This is the modern, readable alternative to string concatenation with `+`.

---

## 3. Arrays

Arrays in TypeScript are ordered lists of values. There are two equivalent syntaxes for declaring the type of an array. The first, and more common, is to add `[]` after the element type:

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];
const names: string[] = ["Hieu", "Anna", "Bob"];
const flags: boolean[] = [true, false, true];
```

The second syntax uses the `Array<T>` generic form:

```typescript
const numbers: Array<number> = [1, 2, 3, 4, 5];
const names: Array<string> = ["Hieu", "Anna", "Bob"];
```

These two forms are **identical** — `number[]` is just shorthand for `Array<number>`. The `[]` form is preferred for simple cases because it is shorter and easier to read. The `Array<T>` form is sometimes clearer in complex types: `Array<Array<number>>` (a 2D array) reads more cleanly than `number[][]` to some developers. Pick one style and stick with it for consistency — your project's ESLint config can enforce this.

For arrays that can hold multiple types, use a **union type** (Lesson 06 covers unions in depth):

```typescript
const mixed: (number | string)[] = [1, "two", 3, "four"];
```

The parentheses are important: `(number | string)[]` means "an array of (number or string)". Without the parentheses, `number | string[]` would mean "a number, or an array of strings" — a completely different type. Always use parentheses around unions inside arrays.

Arrays in JavaScript (and TypeScript) are dynamically sized — you can `push`, `pop`, `shift`, and `unshift` to add or remove elements. The type system checks that every element you add matches the element type: `numbers.push("six")` is a compile error because `"six"` is not a number.

---

## 4. Tuples

A **tuple** is an array with a fixed number of elements, where each element can have a different type. Tuples are useful when you have a small fixed structure of related values — coordinates, key/value pairs, return values from a function.

```typescript
const pair: [string, number] = ["Hieu", 25];
const triple: [string, number, boolean] = ["Hieu", 25, true];
```

Let's break this down. `[string, number]` is a tuple type with exactly two elements: the first must be a string, the second must be a number. `["Hieu", 25]` matches — `"Hieu"` is a string, `25` is a number. `["Hieu", "25"]` would not match (the second element would be a string, not a number). `[25, "Hieu"]` would also not match (the order matters: string first, then number).

You access tuple elements by index, just like arrays: `pair[0]` is `"Hieu"` (typed as `string`), `pair[1]` is `25` (typed as `number`). TypeScript knows the type of each index, so `pair[0].toUpperCase()` works (the compiler knows `pair[0]` is a string) but `pair[1].toUpperCase()` is a compile error (the compiler knows `pair[1]` is a number, and numbers do not have `toUpperCase`).

Tuples have a gotcha: by default, TypeScript lets you call array methods on them. `pair.push("extra")` compiles, even though it makes the tuple have three elements instead of two. To prevent this, enable `readonly` tuples: `const pair: readonly [string, number] = ["Hieu", 25];` — now `pair.push(...)` is a compile error. The `readonly` modifier is covered more in Lesson 04.

Tuples are most useful for return values from functions that need to return multiple values (an alternative to defining an interface). They are also used to model `Promise.all`'s argument list — `Promise.all([fetchUsers(), fetchPosts()])` returns a `Promise<[User[], Post[]]>` tuple, preserving the order and types.

---

## 5. Enums

An **enum** is a way to define a set of named constants. Enums come in two flavors: numeric (the default) and string.

```typescript
enum Color {
  Red,      // 0
  Green,    // 1
  Blue,     // 2
}

const c: Color = Color.Green;
console.log(c);   // 1
```

Let's break this down. `enum Color { Red, Green, Blue }` declares an enum named `Color` with three members. By default, the members are **auto-incremented numbers starting from 0**: `Color.Red` is `0`, `Color.Green` is `1`, `Color.Blue` is `2`. You can access members with the dot syntax: `Color.Green` evaluates to `1`. The type `Color` means "one of the values `Color.Red`, `Color.Green`, or `Color.Blue`".

You can also specify custom starting values, and TypeScript will auto-increment from there:

```typescript
enum HttpStatus {
  Ok = 200,
  NotFound = 404,
  InternalServerError = 500,
}
```

And you can mix and match — once you start assigning, you must assign to all subsequent members until the next auto-incremented one.

**String enums** are the recommended form for most use cases. They are more debuggable because the runtime value is meaningful:

```typescript
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}

const move: Direction = Direction.Up;
console.log(move);   // "UP"
```

String enums do not auto-increment — you must initialize every member. They produce more readable code in logs and debugger output: `Direction.Up` becomes `"UP"` at runtime, which is meaningful; `Color.Red` becomes `0`, which is opaque.

There is a third form: **`const enum`**. A `const enum` is fully erased at compile time — every usage is replaced with the literal value:

```typescript
const enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE",
}
const s: Status = Status.Active;   // compiles to: const s = "ACTIVE";
```

`const enum` produces smaller, faster code because the enum object is never created at runtime. However, `const enum` has compatibility issues across files when using isolated modules (a setting required by some bundlers), so it has fallen out of favor in modern TypeScript. The recommendation in 2024 is to use **string enums** for most cases, or **union types of string literals** (Lesson 06) which provide similar safety with no runtime overhead at all.

---

## 6. `any` and `unknown`

`any` and `unknown` are two types that both mean "this can hold any value" — but they are radically different in how the compiler treats them.

**`any`** is the **escape hatch**. When you annotate something as `any`, you are telling the compiler "I do not want type checking on this value." You can assign anything to it, and you can do anything with it:

```typescript
let data: any = 5;
data = "hello";           // OK
data = { name: "Hieu" };  // OK
data.toUpperCase();        // OK — compiler does not check
data.nonexistent.method(); // OK — compiler does not check (crashes at runtime!)
```

`any` is dangerous because it disables the type checker entirely. Every property access on an `any` value is allowed, every method call is allowed, every arithmetic is allowed. The compiler cannot help you, and bugs surface only at runtime. Use `any` only as a last resort — when migrating from JavaScript, when working with truly dynamic data, or when interfacing with a library that has no type definitions. Even then, isolate the `any` to the smallest possible scope and convert to a real type as soon as you can.

**`unknown`** is the **safe alternative**. You can assign anything to `unknown` (just like `any`), but the compiler **refuses to let you use the value until you have narrowed it to a more specific type**:

```typescript
let data: unknown = 5;
data = "hello";
data = { name: "Hieu" };

// data.toUpperCase();   // COMPILE ERROR — 'data' is of type 'unknown'
if (typeof data === "string") {
  console.log(data.toUpperCase());   // OK — narrowed to string
}
```

The difference is stark: `data.toUpperCase()` is allowed on `any` (and crashes at runtime if `data` is not a string) but is a compile error on `unknown` (forcing you to prove to the compiler that `data` is a string before using it as one). This is the difference between "I do not care about types" (`any`) and "I do not know the type yet, but I will figure it out" (`unknown`).

The rule is simple: **prefer `unknown` over `any`** whenever you genuinely cannot know the type. When you receive untyped data from `JSON.parse`, from a network response, or from a third-party API without types, type it as `unknown` and then narrow it with type guards (Lesson 06) before using it. This way the compiler helps you handle every possible case, rather than letting bugs through.

---

## 7. `void` and `never`

`void` and `never` are two types that both describe what a function does not return — but for different reasons.

**`void`** means "this function returns nothing." It is the return type of functions that do `console.log` or otherwise have side effects without producing a value:

```typescript
function logMessage(message: string): void {
  console.log(message);
}
```

The `: void` after the parameter list says the function does not return a meaningful value. If you try to `return "something";` from a `void` function, the compiler errors. If you write `const result = logMessage("hi")`, the type of `result` is `void` — and trying to use `result` for anything is a compile error. Note that `void` allows `return;` (with no value) and `return undefined;` and `return null;` (the latter only if `strictNullChecks` is off). In JavaScript, a function that "returns nothing" actually returns `undefined` — `void` is TypeScript's way of saying "ignore the return value."

**`never`** means "this function never returns at all" — because it either throws an exception or runs forever:

```typescript
function fail(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
    // do something forever
  }
}
```

`never` is a type with no values. A function with return type `never` must either throw (which exits the function via an exception, never reaching a normal return) or loop forever (which never reaches the end of the function). If a function declared `never` actually does return, the compiler errors.

`never` is more useful than it first appears. It is used in **exhaustiveness checking**: in a `switch` statement over a discriminated union, if you handle every case explicitly and then have a `default` branch that calls a function returning `never`, the compiler will error if you later add a new case to the union but forget to handle it in the switch. We will see this pattern in Lesson 06.

---

## 8. Type Annotations vs Inference

TypeScript can usually infer the type of a variable from its initializer — you do not always need to write the annotation explicitly. Both forms below produce the same type:

```typescript
// Explicit annotation
let x: number = 5;
const name: string = "Hieu";

// Inferred (TypeScript figures it out)
let x = 5;            // TypeScript infers: number
const name = "Hieu";   // TypeScript infers: string
```

For simple variable declarations, prefer **inference** — let the compiler figure it out. It is less typing, less visual noise, and less to maintain when types change. The compiler is smart: `let x = 5` infers `number`, `let s = "hello"` infers `string`, `let arr = [1, 2, 3]` infers `number[]`, `let pair = ["Hieu", 25]` infers `(string | number)[]` (notice this is a regular array, not a tuple — for tuples you must annotate explicitly).

For **function parameters and return types**, prefer explicit annotations. This is for two reasons. First, function parameters cannot be inferred from anything — the compiler has no way to know what type callers will pass, so you must annotate. Second, function signatures are part of your code's documentation — an explicit return type tells the reader what to expect without reading the implementation. If the function's body changes in a way that changes the return type, an explicit return type annotation will cause a compile error at the function definition rather than at every call site, making the change safer.

```typescript
// Function parameters: ALWAYS annotate
function greet(name: string): string {
  return "Hello, " + name;
}

// Variable declarations: prefer inference
const message = greet("Hieu");
const numbers = [1, 2, 3];
```

The convention is: **explicit for function signatures, inferred for local variables.** Modern TypeScript style guides like the one from the TypeScript team itself recommend this approach.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Declare Variables of Each Primitive Type

Create a file `src/primitives.ts` and declare one variable of each primitive type — `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`. Use both explicit annotations and inferred types. Then write `console.log(typeof x)` for each, run with `npx tsx src/primitives.ts`, and confirm the JavaScript `typeof` returns the expected value (`"string"`, `"number"`, `"boolean"`, `"object"` for `null`, `"undefined"`, `"symbol"`, `"bigint"`). Notice the surprise: `typeof null` returns `"object"` — a famous JavaScript bug from 1995 that cannot be fixed without breaking existing code.

### Exercise 2: Build an Array, Tuple, and Enum

In the same file, declare an array of strings, a tuple of `[string, number]` representing a person's name and age, and a string enum `Role` with values `Admin`, `Editor`, `Viewer`. Print all three. Try to call `pair.push("extra")` on the tuple — confirm it compiles (TypeScript's default behavior). Then change the tuple type to `readonly [string, number]` and confirm the push is now a compile error.

### Exercise 3: Compare `any` vs `unknown`

Create a function that takes a parameter of type `unknown` and uses `typeof` checks to safely handle three cases — string, number, and object — printing a different message for each. Then create a second function that takes `any` and does the same operations without narrowing. Confirm both compile, but the `any` version would crash at runtime if you passed something unexpected. The exercise is to feel the difference: `unknown` forces safety, `any` opts out of it.

---

## Common Mistakes

### Mistake 1: Using `any` to silence errors instead of fixing them

```typescript
// WRONG — using any to make the type checker shut up
function process(data: any) {
  return data.users[0].name.toUpperCase();
}
```

```typescript
// RIGHT — define the shape, let the compiler help you
interface UserList { users: { name: string }[]; }
function process(data: UserList): string {
  return data.users[0].name.toUpperCase();
}
```

When the compiler complains and you do not know how to fix it, the temptation is to slap `any` on the offending value and move on. This is the worst thing you can do — you have just turned off the type checker for that value, and any future bugs in that code will surface at runtime, not at compile time. The fix is always to figure out the actual shape of the data and write a proper interface for it (Lesson 04). If you genuinely cannot know the shape (truly dynamic data), use `unknown` and narrow it explicitly. Reserve `any` for migrations and quick prototypes — never for production code.

### Mistake 2: Treating `number[]` and `Array<number>` as different

```typescript
// WRONG — these compile as the same type, do not waste effort "converting"
const a: number[] = [1, 2, 3];
const b: Array<number> = a;   // OK — same type
```

```typescript
// RIGHT — pick one syntax and use it consistently
const a: number[] = [1, 2, 3];
const b: number[] = [4, 5, 6];
```

`number[]` and `Array<number>` are **two ways to write the same type**. They are completely interchangeable — assigning one to the other works without any conversion. There is no runtime difference either (both produce a plain JavaScript array). The only consideration is style: pick one and use it consistently in your project. Most TypeScript codebases prefer the `[]` syntax for simple types (`number[]`, `string[]`) and reserve `Array<T>` for complex generics where the angle brackets read more cleanly (`Array<Promise<User>>`).

### Mistake 3: Expecting `enum` values to be strings by default

```typescript
// WRONG — assuming Color.Red is the string "Red"
enum Color { Red, Green, Blue }
console.log(Color.Red);   // prints 0, not "Red"
```

```typescript
// RIGHT — use a string enum if you want string values
enum Color { Red = "RED", Green = "GREEN", Blue = "BLUE" }
console.log(Color.Red);   // prints "RED"
```

By default, TypeScript enums are **numeric and auto-incremented from 0**. `Color.Red` is `0`, `Color.Green` is `1`, and so on. This is rarely what you want — `0` is opaque in logs, hard to debug, and easily confused with other numbers. For most use cases, prefer **string enums** (where every member is explicitly a string) or **union types of string literals** (Lesson 06): `type Color = "red" | "green" | "blue";`. Both produce meaningful runtime values without the runtime overhead of an enum object.

### Mistake 4: Assuming `unknown` and `any` behave the same

```typescript
// WRONG — treating unknown as if it were any
function process(data: unknown) {
  return data.toUpperCase();   // COMPILE ERROR
}
```

```typescript
// RIGHT — narrow unknown before using it
function process(data: unknown): string {
  if (typeof data === "string") {
    return data.toUpperCase();
  }
  throw new Error("Expected a string");
}
```

`unknown` is not "any with a different name" — it is a strictly safer alternative that forces you to narrow before use. With `any`, you can do anything (and the compiler cannot help you); with `unknown`, you must prove to the compiler that the value is what you think it is before you can use it. The benefit is huge: every operation on an `unknown` value must be justified by a type guard, which means the compiler enforces that you handle every possible case. If you find yourself wishing `unknown` "behaved more like `any`", you are using the wrong type — either define an interface for the data (Lesson 04) or accept that you need to narrow it explicitly.

---

## Summary

- Types in TypeScript are a compile-time contract — they are erased at runtime, so `typeof x` returns the JavaScript type, not the TypeScript annotation.
- Primitive types: `string`, `number` (no separate `int`/`float`), `boolean`, `null`, `undefined`, `symbol`, `bigint`.
- Arrays: `number[]` (preferred) and `Array<number>` (equivalent) — both produce the same type.
- Tuples are fixed-length, fixed-type arrays: `[string, number]` — use `readonly` to prevent `push` and `pop`.
- Enums come in two flavors: numeric (auto-incremented from 0, opaque) and string (explicit, readable) — prefer string enums or string-literal union types.
- `any` turns off the type checker entirely — avoid it. `unknown` is the safe alternative: you can assign anything to it but must narrow before use.
- `void` means a function returns nothing; `never` means a function never returns (it throws or loops forever).
- Prefer type inference for local variables; explicit annotations for function parameters and return types.

You now know all of TypeScript's basic types. In Lesson 04 we will move on to object types — how to describe the shape of objects with `interface` and `type` aliases, when to use each, and how to express optional and readonly properties.

---

**Next:** [Lesson 04: Interfaces and Types →](./04-interfaces-and-types.md)
