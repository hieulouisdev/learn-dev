# Lesson 07: TypeScript Cheatsheet

> Module: TypeScript · Lesson 7 of 7
> Estimated time: 30–45 minutes

This is the TypeScript cheatsheet — a single-page reference you can keep open while you code. It covers everything from the Hello World template through generics, narrowing, and the most-used utility types. Use it as a quick lookup when you forget a syntax detail, or print it out and tape it next to your monitor.

This lesson does not introduce new concepts — it consolidates what you learned in Lessons 01–06 into one place. Skim the whole thing once, then bookmark it for reference. Every code snippet is valid TypeScript 5.0+ and ready to copy into your editor.

---

## Learning Objectives

After this lesson, you will be able to:

1. Quickly look up any TypeScript syntax — primitives, compound types, object types, functions, generics, narrowing — without re-reading a full lesson.
2. Identify the most-used TypeScript compiler commands and `tsconfig.json` options.
3. Use the 10 most common utility types (`Partial`, `Readonly`, `Pick`, `Omit`, `Record`, etc.) to transform types.
4. Avoid the common TypeScript pitfalls that trip up beginners and intermediate developers alike.

---

## 1. Hello, World!

The minimal TypeScript program:

```typescript
const message: string = "Hello, World!";
console.log(message);
```

Save as `hello.ts`, compile with `tsc hello.ts`, run with `node hello.js`. Or run directly with `npx tsx hello.ts`.

---

## 2. Compiler Commands

| Command | What it does |
|---|---|
| `tsc` | Compile all files in `tsconfig.json` |
| `tsc file.ts` | Compile one file (ignores `tsconfig.json`) |
| `tsc --init` | Generate a default `tsconfig.json` |
| `tsc -w` or `--watch` | Recompile on file change |
| `tsc --noEmit` | Type-check only, do not produce `.js` files |
| `tsc --strict` | Type-check with strict mode (one-off) |
| `tsc --version` | Print the TypeScript version |
| `tsc --noEmitOnError` | Do not emit `.js` if there are type errors |
| `tsc --listFiles` | List all files TypeScript reads (for debugging) |
| `npx tsc` | Run locally-installed `tsc` (preferred over global) |

Key `tsconfig.json` options:

```jsonc
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,           // most important option
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

---

## 3. Primitive Types

| Type | Example | Notes |
|---|---|---|
| `string` | `"hello"` | Single or double quotes, or backticks for template literals |
| `number` | `42`, `3.14`, `0xff` | No separate int/float — IEEE 754 64-bit |
| `boolean` | `true`, `false` | No truthiness coercion in type system |
| `null` | `null` | Intentional absence of value |
| `undefined` | `undefined` | Variable has no value assigned |
| `symbol` | `Symbol("id")` | Unique, immutable identifier |
| `bigint` | `100n` | Arbitrary-precision integer (ES2020) |

```typescript
let s: string = "hello";
let n: number = 42;
let b: boolean = true;
let nothing: null = null;
let undef: undefined = undefined;
let sym: symbol = Symbol("id");
let big: bigint = 100n;
```

---

## 4. Compound Types

```typescript
// Arrays — two equivalent syntaxes
const numbers: number[] = [1, 2, 3];
const numbers2: Array<number> = [1, 2, 3];

// Multi-type arrays (use parentheses!)
const mixed: (number | string)[] = [1, "two", 3];

// Tuples — fixed length, fixed types
const pair: [string, number] = ["Hieu", 25];
const triple: [string, number, boolean] = ["Hieu", 25, true];
const readonlyPair: readonly [string, number] = ["Hieu", 25];  // cannot push

// Enums — numeric (default) or string
enum Color { Red, Green, Blue }              // 0, 1, 2
enum Direction { Up = "UP", Down = "DOWN" } // string enum
const enum Status { Active = "ACTIVE" }     // erased at compile time

// Special types
let anything: any = ...;        // escape hatch — avoid
let unknown: unknown = ...;     // safe — must narrow before use
function noop(): void {}        // returns nothing
function fail(): never { throw new Error(); }  // never returns

// Object type
let obj: object = {};  // any non-primitive — rarely useful
```

---

## 5. Object Types

Three ways to describe object shapes:

```typescript
// Inline (one-off)
const p: { name: string; age: number } = { name: "Hieu", age: 25 };

// Interface (preferred for object shapes)
interface Person {
  name: string;
  age: number;
  middleName?: string;      // optional
  readonly id: number;     // immutable
}

// Type alias (preferred for unions, tuples, complex)
type Person2 = {
  name: string;
  age: number;
};
```

Comparison:

| Feature | `interface` | `type` |
|---|---|---|
| Object shapes | ✅ | ✅ |
| Primitives | ❌ | ✅ |
| Unions | ❌ | ✅ |
| Tuples | ❌ | ✅ |
| Function types | ✅ (verbose) | ✅ (clean) |
| `extends` | ✅ | ❌ (use `&`) |
| Declaration merging | ✅ | ❌ |
| Better error messages | ✅ | ❌ |

**Rule of thumb:** `interface` for object shapes, `type` for everything else.

---

## 6. Functions

```typescript
// Function type
type GreetFn = (name: string) => string;

// Function declaration
function add(a: number, b: number): number {
  return a + b;
}

// Arrow function
const double = (x: number): number => x * 2;

// Optional parameter (must come after required)
function greet(name: string, title?: string): string { ... }

// Default parameter
function greet(name: string, greeting: string = "Hello"): string { ... }

// Rest parameter (must be last)
function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0);
}

// Function overloads
function parse(input: string): string;
function parse(input: number): number;
function parse(input: string | number): string | number {
  return input;
}
```

---

## 7. Generics

```typescript
// Single type parameter
function identity<T>(x: T): T { return x; }
const n: number = identity(42);

// Multiple type parameters
function pair<K, V>(key: K, value: V): [K, V] { return [key, value]; }

// Constraint — T must have a length property
function len<T extends { length: number }>(x: T): number {
  return x.length;
}

// Constraint to keys of another type
function get<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Default type parameter
function create<T = string>(): T[] { return []; }
const arr = create();          // T = string
const nums = create<number>(); // T = number
```

Generic types (built-in):

```typescript
const arr: Array<number> = [1, 2, 3];
const p: Promise<string> = Promise.resolve("hi");
const m: Map<string, number> = new Map();
const s: Set<number> = new Set();
```

---

## 8. Unions and Intersections

```typescript
// Union — value is one of these types
type ID = string | number;
let id: ID = "abc";
id = 42;

// Intersection — value has all of these properties
type Named = { name: string };
type Aged = { age: number };
type Person = Named & Aged;   // has both name and age

// Literal types — specific values as types
type Direction = "left" | "right" | "up" | "down";
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
type True = true;

// Literal inference
const x = "hello";   // type: "hello" (literal)
let y = "hello";     // type: string

// Discriminated unions — the killer pattern
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; size: number }
  | { kind: "rectangle"; width: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":    return Math.PI * shape.radius ** 2;
    case "square":    return shape.size ** 2;
    case "rectangle": return shape.width * shape.height;
    default:
      const _exhaustive: never = shape;   // exhaustiveness check
      throw new Error(`Unhandled: ${_exhaustive}`);
  }
}
```

---

## 9. Narrowing

```typescript
// typeof — for primitives
if (typeof x === "string") { x.toUpperCase(); }

// instanceof — for classes
if (x instanceof Date) { x.getTime(); }

// in — for object properties
if ("name" in obj) { obj.name; }

// Truthiness — for null/undefined
if (x) { /* x is not null/undefined/""/0/false */ }

// Equality — for literal types
if (x === "left") { /* x is "left" */ }

// Type guards — custom narrowing functions
function isString(x: unknown): x is string {
  return typeof x === "string";
}
if (isString(x)) { x.toUpperCase(); }   // narrowed

// as — type assertion (use sparingly)
const el = document.getElementById("x") as HTMLInputElement;

// ! — non-null assertion (use sparingly)
const el2 = document.getElementById("x")!;
```

---

## 10. Common Idioms

```typescript
// Record<K, V> — map of K to V
const users: Record<string, User> = {};
users["abc"] = { name: "Hieu" };

// Partial<T> — all properties optional
function updateUser(user: User, patch: Partial<User>): User {
  return { ...user, ...patch };
}

// Readonly<T> — all properties readonly
const frozen: Readonly<User> = { ... };
// frozen.name = "x";   // COMPILE ERROR

// Pick<T, K> — pick specific properties
type UserPreview = Pick<User, "id" | "name">;

// Omit<T, K> — omit specific properties
type UserWithoutId = Omit<User, "id">;

// ReturnType<F> — return type of a function
type R = ReturnType<typeof JSON.parse>;   // any

// Parameters<F> — parameter types as a tuple
type P = Parameters<typeof setTimeout>;   // [handler: TimerHandler, timeout?: number]

// Awaited<Promise<T>> — unwrap a Promise
type Data = Awaited<Promise<string>>;     // string

// typeof — get the type of a value
const config = { port: 3000, host: "localhost" };
type Config = typeof config;   // { port: number; host: string }

// keyof — get keys of a type as a union
type UserKeys = keyof User;   // "id" | "name" | "email"
```

---

## 11. Common Pitfalls

- **Types are erased at runtime.** `typeof x` returns the JavaScript type, not the TypeScript annotation. You cannot `instanceof` an interface — interfaces are not runtime values.
- **`any` propagates.** Anywhere you use `any`, the type checker is off. Use `unknown` instead, and narrow.
- **`unknown` requires narrowing.** You cannot use an `unknown` value directly — the compiler forces you to narrow it first. This is the point of `unknown`.
- **`as` is a footgun.** It overrides the compiler. If you assert the wrong type, the bug surfaces at runtime, not compile time. Prefer narrowing.
- **`!` is a footgun.** The non-null assertion strips `null` and `undefined` from a type, but if the value is actually null, your code crashes. Prefer explicit null checks.
- **`readonly` is shallow.** It applies only to the top-level property. For deep immutability, use `Readonly<T>` recursively or `as const`.
- **Optional parameters must come after required ones.** TypeScript enforces this to avoid ambiguity at call sites.
- **Function overloads' implementation must accept all overload inputs.** Otherwise you get "Overload signature is not compatible with its implementation signature."
- **`number[]` and `Array<number>` are identical.** Do not "convert" between them — they are the same type.
- **`interface` and `type` overlap for object shapes.** Use `interface` for object shapes (better error messages, extensible); use `type` for unions, tuples, and complex compositions.

---

## 12. The 10 Most-Used Utility Types

| Utility | What it does | Example |
|---|---|---|
| `Partial<T>` | All properties optional | `Partial<User>` → `{ id?: number; name?: string; ... }` |
| `Required<T>` | All properties required | `Required<Partial<User>>` → `User` |
| `Readonly<T>` | All properties readonly | `Readonly<User>` → cannot reassign any property |
| `Record<K, V>` | Object with keys K, values V | `Record<string, number>` → `{ [k: string]: number }` |
| `Pick<T, K>` | Pick specific properties | `Pick<User, "id" \| "name">` → `{ id: number; name: string }` |
| `Omit<T, K>` | Omit specific properties | `Omit<User, "id">` → `{ name: string; email: string }` |
| `Exclude<T, U>` | Exclude types from a union | `Exclude<"a" \| "b" \| "c", "a">` → `"b" \| "c"` |
| `Extract<T, U>` | Extract types from a union | `Extract<"a" \| 1, string>` → `"a"` |
| `ReturnType<F>` | Return type of a function | `ReturnType<typeof fetch>` → `Promise<Response>` |
| `Parameters<F>` | Parameter types as a tuple | `Parameters<typeof fetch>` → `[input: RequestInfo, init?: RequestInit]` |

Bonus: `Awaited<Promise<T>>` unwraps a nested Promise — `Awaited<Promise<Promise<string>>>` is `string`.

---

## What's Next?

Congratulations — you have completed the TypeScript module. You now know enough TypeScript to read any modern web codebase, write type-safe functions and components, model complex data with discriminated unions, and use generics to write reusable code. The rest of your TypeScript journey is practice: build something real.

Three excellent next projects:

1. **A small API client.** Pick a public API (the GitHub API, the JSONPlaceholder API, or a weather API) and write a typed client for it. Use `interface` for the response shapes, generics for the request wrapper, and discriminated unions for success/error responses. This is exactly the kind of code TypeScript was designed for, and you will internalize the type system through use.

2. **A small web app with a framework.** Pick Next.js (React-based, the most popular in 2024), Nuxt (Vue-based), or Astro. Build a to-do list, a blog, or a small dashboard. The framework handles the build setup; you focus on writing TypeScript. You will be amazed at how much the type checker catches.

3. **A small backend with NestJS or Hono.** Build a JSON API with typed routes and typed request/response shapes. NestJS is Angular-style (heavy, opinionated, classes everywhere); Hono is modern and lightweight (functions everywhere, runs on any JS runtime). Both are excellent and let you apply your TypeScript skills on the server.

Two essential libraries to learn next:

- **`zod`** ([zod.dev](https://zod.dev)) — runtime validation library. Lets you define a schema once and use it for both runtime validation and TypeScript type inference. The right way to handle untrusted data from `JSON.parse`, network responses, and form inputs.

- **`effect`** or **`fp-ts`** — functional programming libraries that take TypeScript's type system to its full potential. Optional, but eye-opening if you want to see how far the type system can go.

Two essential references:

- The **[TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)** — the official, comprehensive reference. Bookmark it.
- The **[TypeScript Playground](https://www.typescriptlang.org/play)** — try TypeScript in your browser, share snippets with colleagues, and see the compiled JavaScript side by side with your source.

When you are ready for the next module, head to **[/home/z/my-project/learn-dev/17-java/](/home/z/my-project/learn-dev/17-java/)** — Java, the language that influenced C# (and therefore TypeScript) and still runs much of the enterprise software world. You will find Java's class-based object model and its explicit type system a comfortable fit after TypeScript, with new concepts like checked exceptions, the JVM, and Maven/Gradle build systems to explore.

Welcome to the world of safe, scalable JavaScript development. You are ready.
