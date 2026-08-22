# Lesson 06: Type Narrowing and Unions

> Module: TypeScript · Lesson 6 of 7
> Estimated time: 30–45 minutes

Real-world data is rarely one fixed shape. A user ID might be a number in some places and a string in others. A response from an API might be a success object or an error object. A function parameter might be a string, a number, or null depending on context. TypeScript's tools for handling this uncertainty are **union types** (a value can be one of several types), **intersection types** (a value has properties of multiple types), **literal types** (a value is one specific value), and **type narrowing** (the compiler tracks which of several possible types a value has at each point in the code). The crown jewel is the **discriminated union** pattern, which combines all of these into a way to model complex data with full type safety.

By the end of this lesson you will understand how the TypeScript compiler uses your code's control flow (if statements, typeof checks, instanceof checks, in checks) to narrow a value's type — and how to use this for safe, readable code that handles every possible case.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use union types (`|`), intersection types (`&`), and literal types to model values that can be one of several possibilities.
2. Use type narrowing through `typeof`, `instanceof`, `in`, truthiness, and custom type guards to safely use union-typed values.
3. Apply the discriminated union pattern with exhaustiveness checking using `never`.
4. Use the `as` type assertion and the `!` non-null assertion sparingly and correctly.

---

## 1. Union Types

A **union type** describes a value that can be one of several types. Use the `|` operator:

```typescript
let id: string | number;

id = "abc123";   // OK
id = 42;         // OK
// id = true;     // COMPILE ERROR — boolean not in the union
```

Let's break this down. `string | number` is a union type — `id` can hold either a string or a number at any time. You can assign a string to it (`id = "abc123"`), and you can assign a number to it (`id = 42`). You cannot assign a boolean — it is not in the union. Union types are useful when a value legitimately can be more than one type — IDs that come from different sources (sometimes numbers, sometimes strings), function parameters that accept multiple input formats, or properties on an object that vary based on the object's kind.

To use a union-typed value, you must **narrow** it — prove to the compiler which of the union's types the value currently has. We will cover narrowing in Section 4. For now, the key insight is: a union type `A | B` means "this value is A or B at this moment — figure out which before using it." The compiler refuses to let you do anything with a union-typed value that is not safe for *every* member of the union.

```typescript
function process(id: string | number) {
  // id.toUpperCase();   // COMPILE ERROR — number has no toUpperCase
  if (typeof id === "string") {
    console.log(id.toUpperCase());   // OK — narrowed to string
  } else {
    console.log(id.toFixed(2));      // OK — narrowed to number
  }
}
```

Without the `if (typeof id === "string")` check, TypeScript refuses to let you call `.toUpperCase()` because the value might be a number. After the check, TypeScript knows `id` is a string in the `if` branch, so `.toUpperCase()` is safe. This is **type narrowing**, and it is the most important concept in TypeScript.

---

## 2. Intersection Types

An **intersection type** combines multiple types into one — the resulting type has all the properties of each. Use the `&` operator:

```typescript
interface Named { name: string; }
interface Aged { age: number; }

type Person = Named & Aged;

const p: Person = {
  name: "Hieu",
  age: 25,
};
```

Let's break this down. `Named & Aged` is an intersection type — a value of this type must satisfy *both* `Named` and `Aged`. So `Person` has both `name` (from `Named`) and `age` (from `Aged`). The variable `p` must have both properties, or the compiler errors with "Property 'X' is missing".

Intersections are most useful for combining multiple smaller interfaces into one composite type. The same effect can be achieved with interface extension (`interface Person extends Named, Aged`), but `&` works with type aliases, function types, and any other type — not just interfaces. Intersections also compose with conditional types and mapped types (advanced features covered in larger TypeScript texts).

The main gotcha with intersections is that they can produce **never** when the types conflict:

```typescript
type A = string & number;   // never — no value is both string and number
type B = { x: string } & { x: number };   // never — x cannot be both string and number
```

`string & number` is `never` because no value is both a string and a number simultaneously. This is rarely what you want — it usually means a bug in your types. The compiler will sometimes produce `never` silently and you will wonder why no value can be assigned.

---

## 3. Literal Types

In TypeScript, a specific value can be a type. These are **literal types**:

```typescript
let direction: "left" | "right" | "up" | "down";

direction = "left";   // OK
direction = "right";  // OK
// direction = "sideways";   // COMPILE ERROR — "sideways" not in the union
```

Let's break this down. `"left"`, `"right"`, `"up"`, `"down"` are **string literal types** — types that consist of one specific string value. The union `"left" | "right" | "up" | "down"` says `direction` can be any one of those four specific strings — and no other string. This is far more precise than `direction: string`, which would accept any string at all. Literal types are how TypeScript models finite sets of values like directions, status codes, days of the week, or color names.

There are also **numeric literal types** (`1 | 2 | 3` for a dice roll) and **boolean literal types** (`true` or `false`, though these are usually written as the `boolean` type which is `true | false`).

Literal types combine beautifully with unions to form what other languages call **enums**:

```typescript
type HttpStatus = 200 | 404 | 500;
type Role = "admin" | "editor" | "viewer";
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
```

These are the modern, lightweight alternative to TypeScript's `enum` keyword (Lesson 03). They produce no runtime code at all — the type information is purely compile-time. They also give better autocomplete in your editor: when you type `direction = `, VS Code suggests the four valid values.

Literal types are inferred for `const` declarations:

```typescript
const x = "hello";   // type is "hello" (the literal), not string
let y = "hello";      // type is string — let variables can be reassigned
```

`const x = "hello"` infers the literal type `"hello"` because `const` variables cannot be reassigned — their value is fixed at the literal. `let y = "hello"` infers `string` because `let` variables can be reassigned to any string. This is why `const` is preferred over `let` — it gives TypeScript more precise type information.

---

## 4. Type Narrowing

**Type narrowing** is the process by which TypeScript tracks the type of a value through your code's control flow. Each `if`, `?` (ternary), `&&`, `||`, `typeof`, `instanceof`, and `in` check narrows (or widens) the type at each point. The result is that inside an `if` block, the compiler knows more about the value's type than it did before the `if`.

TypeScript supports several narrowing mechanisms:

### `typeof` checks (for primitives)

```typescript
function process(value: string | number) {
  if (typeof value === "string") {
    // Here, value is narrowed to string
    console.log(value.toUpperCase());   // OK
  } else {
    // Here, value is narrowed to number (the only other member of the union)
    console.log(value.toFixed(2));      // OK
  }
}
```

`typeof` works for primitive types: `"string"`, `"number"`, `"boolean"`, `"undefined"`, `"object"`, `"function"`, `"symbol"`, `"bigint"`. Note that `typeof null` returns `"object"` (a famous JavaScript bug), so `typeof` cannot distinguish `null` from other objects — use a truthiness check or strict equality (`value === null`) for that.

### `instanceof` checks (for classes)

```typescript
class Cat { meow(): void {} }
class Dog { bark(): void {} }

function speak(animal: Cat | Dog) {
  if (animal instanceof Cat) {
    animal.meow();   // OK — narrowed to Cat
  } else {
    animal.bark();   // OK — narrowed to Dog
  }
}
```

`instanceof` works for class instances and checks the prototype chain. It is the runtime-aware equivalent of `typeof` for object types. It only works when there is a runtime class to check against — it does not work for plain interfaces or type aliases, because those are erased at compile time.

### `in` checks (for properties)

```typescript
interface Cat { meow(): void; }
interface Dog { bark(): void; }

function speak(animal: Cat | Dog) {
  if ("meow" in animal) {
    animal.meow();   // OK — narrowed to Cat
  } else {
    animal.bark();   // OK — narrowed to Dog
  }
}
```

`"meow" in animal` checks whether the property `meow` exists on `animal` at runtime. If it does, TypeScript narrows the type to whichever member of the union has that property. This works for any object, not just class instances — perfect for interfaces and type aliases that have no runtime representation.

### Truthiness checks (for `null`, `undefined`, `""`, `0`, `false`, `NaN`)

```typescript
function process(value: string | null) {
  if (value) {
    // Here, value is narrowed to string (not null, not "")
    console.log(value.toUpperCase());
  } else {
    // Here, value is null or "" — TypeScript still types it as string | null
    console.log("No value");
  }
}
```

A bare `if (value)` check narrows away falsy values. For `string | null`, the truthy branch narrows to `string` (with the caveat that an empty string `""` is also falsy, so the truthy branch narrows to `string & { length: 1 }` semantically — TypeScript models this as just `string` for simplicity). For `null` and `undefined`, truthiness checks are the most idiomatic way to narrow.

### Equality checks (for specific values)

```typescript
function process(value: "a" | "b" | "c") {
  if (value === "a") {
    // Here, value is narrowed to "a"
    console.log("got a");
  } else {
    // Here, value is narrowed to "b" | "c"
    console.log("got b or c");
  }
}
```

A `===` (or `==`) check against a literal narrows to that specific literal type in the truthy branch, and to the rest of the union in the falsy branch.

These narrowing mechanisms work everywhere — `if`, `else if`, `else`, ternary expressions (`x ? a : b`), `&&` short-circuit (`x && x.toUpperCase()`), and even after a `return` (TypeScript knows code after `if (x === null) return;` cannot have `x === null`). The compiler tracks every narrowing through your code, and the result is that you usually do not have to write explicit type assertions — just write natural control flow and the compiler follows along.

---

## 5. Discriminated Unions

The **discriminated union** pattern is TypeScript's killer feature. It combines unions, literal types, and narrowing into a way to model complex data with full type safety. Here is the pattern:

```typescript
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; size: number }
  | { kind: "rectangle"; width: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.size ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

Let's break this down. `Shape` is a union of three object types, each with a `kind` property whose value is a specific string literal — `"circle"`, `"square"`, or `"rectangle"`. The `kind` property is the **discriminant** (sometimes called the "tag"). Each member of the union has a different value for the discriminant, plus its own specific properties (`radius` for circle, `size` for square, `width`/`height` for rectangle).

Inside `area`, the `switch (shape.kind)` statement narrows `shape` to one specific member of the union in each `case` branch. In `case "circle":`, TypeScript knows `shape.kind === "circle"`, so `shape` is narrowed to the first member of the union — and `shape.radius` is accessible. In `case "square":`, `shape` is narrowed to the second member, and `shape.size` is accessible. No assertions, no casts, no runtime checks beyond the `kind` field — the compiler tracks everything.

The magic is the **exhaustiveness check**. If you later add a new member to the union — `| { kind: "triangle"; base: number; height: number }` — but forget to add a `case "triangle":` to the `switch`, the compiler can warn you. The trick is to add a `default` branch that calls a function returning `never`:

```typescript
function assertNever(x: never): never {
  throw new Error(`Unexpected value: ${x}`);
}

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":    return Math.PI * shape.radius ** 2;
    case "square":    return shape.size ** 2;
    case "rectangle": return shape.width * shape.height;
    default:
      // If we add a new Shape member, this line becomes a compile error
      const _exhaustive: never = shape;
      return assertNever(shape);
  }
}
```

The line `const _exhaustive: never = shape;` is the exhaustiveness check. If all cases of `Shape` are handled, `shape` is narrowed to `never` in the `default` branch (because no value is left). Assigning `never` to a `never`-typed variable is fine. But if you add a new case to `Shape` and forget to handle it, `shape` in the `default` branch is no longer `never` — it is the new case's type. Assigning that to `never` is a compile error: `Type '"triangle"' is not assignable to type 'never'.` The compiler tells you exactly what case you forgot.

Discriminated unions are how TypeScript models real-world data that has multiple variants: API responses (success or error), UI component states (loading, success, error), AST nodes (FunctionDeclaration, IfStatement, WhileStatement), and many more. They are the closest TypeScript gets to algebraic data types from functional languages like Rust's `enum` or Haskell's data types.

---

## 6. Type Guards

A **type guard** is a custom function that narrows a value's type. Use the `x is T` return type (a **type predicate**):

```typescript
function isString(x: unknown): x is string {
  return typeof x === "string";
}

function process(value: unknown) {
  if (isString(value)) {
    // Here, value is narrowed to string — the type predicate did the work
    console.log(value.toUpperCase());
  } else {
    // Here, value is still unknown
    console.log("Not a string");
  }
}
```

Let's break this down. `function isString(x: unknown): x is string` declares a function that takes a value of type `unknown` and returns a boolean. The return type `x is string` is a **type predicate** — it tells the compiler that if the function returns `true`, the parameter `x` is of type `string`. Inside the function, you use a `typeof` check (or any other narrowing mechanism) to determine whether `x` is actually a string. The function returns `true` if it is, `false` otherwise.

After the `if (isString(value))` check, TypeScript narrows `value` to `string` in the truthy branch — exactly as if you had written `if (typeof value === "string")` inline. The benefit is that you can encapsulate complex narrowing logic in a function and reuse it. For example, you can write `isUser(x)`, `isApiError(x)`, `isPerson(x)` — each encapsulating the checks for that specific shape.

Type guards are especially useful for narrowing `unknown` values from `JSON.parse`, network responses, or other untrusted sources. Write a guard for each expected shape and use them to safely narrow the `unknown` value to a specific type before using it.

---

## 7. The `as` Operator (Type Assertion)

Sometimes you know more about a value's type than TypeScript can verify. The `as` operator lets you **assert** a specific type, overriding the compiler:

```typescript
const el = document.getElementById("myInput") as HTMLInputElement;
console.log(el.value);   // OK — el is asserted as HTMLInputElement
```

Let's break this down. `document.getElementById("myInput")` returns `HTMLElement | null` — the element might exist (and be some kind of `HTMLElement`) or might not exist (null). The `as HTMLInputElement` assertion tells TypeScript: "trust me, this element is specifically an `HTMLInputElement`, not just any `HTMLElement`." After the assertion, `el` has type `HTMLInputElement`, which has a `.value` property — so `el.value` is allowed.

The `as` operator is a **footgun** — it overrides the compiler's type checking. If you assert the wrong type, the compiler will not warn you, and the bug will surface at runtime. Use `as` only when:

1. You genuinely know more than the compiler (e.g., you just queried the DOM and you know the element's type from its HTML).
2. You are migrating from JavaScript and need to silence errors temporarily.
3. You are working with a library whose types are incomplete or incorrect.

If you find yourself using `as` to silence an error, ask: "Do I really know this is the right type, or am I just guessing?" If you are guessing, fix the underlying type issue instead. Prefer narrowing (with `typeof`, `in`, or a type guard) over asserting — narrowing proves the type, asserting just claims it.

The `as` operator has a less aggressive variant: `as unknown as T`. This is sometimes needed when TypeScript refuses an `as` because the two types are unrelated (`as string` from a `number` is rejected, because they are not compatible). `as unknown as T` works in two steps — first assert to `unknown` (always allowed), then assert from `unknown` to `T` (also always allowed, since `unknown` accepts anything). This double assertion is a stronger smell — it almost always means you should refactor the types.

---

## 8. Non-null Assertion `!`

The **non-null assertion operator** (`!` after a value) tells TypeScript "trust me, this is not null or undefined":

```typescript
const el = document.getElementById("myInput")!;
console.log(el.value);   // OK — el is HTMLElement (not null)
```

Without the `!`, `el` would be `HTMLElement | null`, and `el.value` would be a compile error (null has no `.value`). With the `!`, TypeScript strips the `null` and `undefined` from the type — `el` is now just `HTMLElement`, and `.value` is... still a compile error, because `HTMLElement` does not have `.value` either. So the `!` is often combined with `as`:

```typescript
const el = document.getElementById("myInput") as HTMLInputElement;
console.log(el.value);   // safer — asserts the type once
```

The non-null assertion is also a **footgun**. If the value is actually null at runtime, your code will crash with "Cannot read properties of null" — exactly the bug TypeScript is supposed to prevent. Use `!` only when you are certain the value is not null, and even then, prefer an explicit null check:

```typescript
const el = document.getElementById("myInput");
if (el === null) {
  throw new Error("Element not found");
}
// Now el is narrowed to HTMLElement
```

This explicit check is more code, but it produces a better error message at runtime and makes the assumption visible to other readers. Reserve `!` for cases where the null check is genuinely redundant — for example, when you just checked the value one line above.

---

## 9. Type Predicates and `in`

The `in` operator (Section 4) is so useful for narrowing that it deserves its own section. Combined with type predicates, you can write powerful guards that narrow complex types:

```typescript
type Animal = Cat | Dog;

interface Cat { meow(): void; purr(): void; }
interface Dog { bark(): void; wag(): void; }

function isCat(a: Animal): a is Cat {
  return "meow" in a;
}

function speak(a: Animal): string {
  if (isCat(a)) {
    a.meow();
    return "meow";
  }
  a.bark();
  return "woof";
}
```

The `in` check inside `isCat` checks whether the property `meow` exists on `a` at runtime. If it does, `a` is a `Cat` (per the type predicate). The type predicate then narrows `a` to `Cat` in the caller, making `a.meow()` and `a.purr()` accessible.

For objects with overlapping shapes, you can use a discriminant property instead:

```typescript
type Animal =
  | { kind: "cat"; lives: number }
  | { kind: "dog"; breed: string };

function describe(a: Animal): string {
  if (a.kind === "cat") {
    return `Cat with ${a.lives} lives`;
  }
  return `Dog of breed ${a.breed}`;
}
```

This is the discriminated union pattern from Section 5 — the `a.kind === "cat"` check narrows `a` to the first member of the union. Both `in` and discriminant checks work; the choice depends on whether the types have a natural discriminant property. If they do (and you control the type definitions), prefer a discriminant — it is faster (one property access vs `in` lookup) and more reliable (less likely to give false positives).

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Model API Responses with a Discriminated Union

Define `type ApiResponse = { status: "success"; data: User[] } | { status: "error"; message: string }` (define a `User` interface first). Write `function handle(res: ApiResponse): string` that returns the first user's name if successful, or the error message if not. Use a `switch (res.status)` for the narrowing. Try adding a new case `| { status: "loading" }` to the union without updating the function — confirm the exhaustiveness check (with `const _exhaustive: never = res;` in the default branch) catches it.

### Exercise 2: Write a Type Guard

Write `function isNumber(x: unknown): x is number` that returns `typeof x === "number"`. Then write `function sum(...nums: unknown[]): number` that filters out non-numbers using your guard and sums the rest. Test it with `sum(1, "two", 3, null, 4)` — should return `8`. Notice how the type guard lets you safely narrow `unknown` to `number` in the truthy branch of the filter.

### Exercise 3: Use the `as` Operator (Carefully)

Cast `JSON.parse('{"name":"Hieu","age":25}')` (which returns `any`) to a `User` interface you define. Then access `user.name` and `user.age`. Notice that TypeScript does not check whether the JSON actually has those properties — the `as` just claims it does. This is exactly the danger of `as`: the cast succeeds at compile time but the data might be wrong at runtime. For production code, you would use a runtime validation library like `zod` to validate the JSON shape before trusting it.

---

## Common Mistakes

### Mistake 1: Overusing `as` to silence real errors

```typescript
// WRONG — silencing a real type error with `as`
const data: unknown = JSON.parse('{"name":"Hieu"}');
const user = data as User;   // no validation — data might not be a User
console.log(user.age);       // OK at compile time, may be undefined at runtime
```

```typescript
// RIGHT — validate at runtime, then narrow
const data: unknown = JSON.parse('{"name":"Hieu"}');
if (typeof data === "object" && data !== null && "name" in data && "age" in data) {
  const user = data as User;
  console.log(user.age);   // safe — we checked the shape
}
```

The `as` operator overrides the compiler, not reality. If the data is not actually the asserted type, your code will fail at runtime — exactly the bug TypeScript was supposed to prevent. Use `as` only when you genuinely know the type. For data from untrusted sources (`JSON.parse`, network responses, user input), use a runtime validation library (zod, io-ts, valibot) or write a type guard that checks the shape before trusting it. The `as` should be the last step, after validation, not the first.

### Mistake 2: Forgetting that `unknown` must be narrowed before use

```typescript
// WRONG — using unknown directly
function process(data: unknown) {
  return data.toUpperCase();   // COMPILE ERROR — data is unknown
}
```

```typescript
// RIGHT — narrow first, then use
function process(data: unknown): string {
  if (typeof data === "string") {
    return data.toUpperCase();
  }
  throw new Error("Expected string");
}
```

`unknown` is not "any with a different name" — it is a value that the compiler will not let you use until you narrow it. The compiler enforces this: every property access, method call, and operation on an `unknown` value is a compile error. The fix is always to narrow first (`typeof`, `in`, a type guard, or a runtime validation library). This is the point of `unknown` — it forces you to handle uncertainty rather than letting it slip through.

### Mistake 3: Assuming `string | number` means "either one anywhere"

```typescript
// WRONG — assuming a union-typed value can be treated as either type
function process(id: string | number) {
  return id.toUpperCase();   // COMPILE ERROR — number has no toUpperCase
}
```

```typescript
// RIGHT — narrow to a specific member of the union before using it
function process(id: string | number) {
  if (typeof id === "string") {
    return id.toUpperCase();   // OK — narrowed to string
  }
  return id.toFixed(2);   // OK — narrowed to number
}
```

`string | number` means "this value is one of string or number *at this moment* — figure out which before using it." The compiler does not know which one — it only knows it could be either. So you cannot use methods that exist on only one of the types — you must narrow first. The compiler's strictness here is the value of union types: it forces you to handle every possible case rather than forgetting one.

### Mistake 4: Confusing `&` (intersection) with `|` (union)

```typescript
// WRONG — using & when you meant |
type ID = string & number;   // never — nothing is both
```

```typescript
// RIGHT — use | for "either one"
type ID = string | number;   // OK — string or number
```

`|` is union — "this value is one of these types." `&` is intersection — "this value is all of these types simultaneously." They are opposite operations and cannot be substituted. `string | number` is meaningful (an ID that can be either a string or a number). `string & number` is `never` (nothing is both a string and a number at the same time). The mnemonic: `|` reads as "or" (A or B), `&` reads as "and" (A and B). For "either of several types," always use `|`.

---

## Summary

- Union types (`A | B`) describe a value that can be one of several types — the caller's choice per use.
- Intersection types (`A & B`) combine multiple types into one that has all properties of each — useful for composition.
- Literal types (`"left"`, `42`, `true`) let a specific value be a type — combine with unions to model finite sets.
- Type narrowing is how TypeScript tracks a value's type through control flow: `typeof`, `instanceof`, `in`, truthiness, equality, and type guards.
- Type guards (`function f(x: unknown): x is T`) encapsulate narrowing logic in a reusable function — the `x is T` return type is a type predicate.
- Discriminated unions (a union of object types sharing a common discriminant property) are TypeScript's killer feature for modeling variants with exhaustiveness checking via `never`.
- The `as` operator asserts a type, overriding the compiler — use sparingly, prefer narrowing.
- The `!` non-null assertion strips `null` and `undefined` from a type — use sparingly, prefer explicit null checks.

You now know how to handle uncertainty in TypeScript. In Lesson 07 — the cheatsheet — we will bring everything together into a single printable reference: all the types, all the patterns, all the utility types, and all the common idioms in one place.

---

**Next:** [Lesson 07: TypeScript Cheatsheet →](./07-typescript-cheatsheet.md)
