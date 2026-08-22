# Lesson 01: What is TypeScript?

> Module: TypeScript · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first TypeScript lesson. In the next 30 minutes, you will learn what TypeScript is, where it came from, how to install it on your computer, and how to write and run your first TypeScript program. By the end, you will have typed `const message: string = "Hello, World!";` into a real TypeScript source file and watched the compiler turn it into running JavaScript.

You do not need to be a JavaScript expert for this lesson. If you completed any of the Python, Rust, or Go modules, you already understand variables, functions, and control flow — and that is more than enough. TypeScript is a superset of JavaScript, so JavaScript syntax will look familiar. The new parts are the type annotations, which work like type systems you have already seen in Rust and Go. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what TypeScript is, where it came from, and why it was created.
2. Install Node.js and the TypeScript compiler on Windows, Mac, or Linux and verify they work.
3. Distinguish between TypeScript (the language) and `tsc` (the compiler) — and explain what "types are erased at runtime" means.
4. Write your first TypeScript program (`const message: string = "Hello, World!";`) and understand the compile-then-run workflow.

---

## 1. What is TypeScript, Really?

TypeScript is a **statically-typed superset of JavaScript that compiles to plain JavaScript**. Let's unpack that carefully, because every word matters. "**Superset of JavaScript**" means every valid JavaScript program is also a valid TypeScript program — TypeScript does not break JavaScript, it adds to it. If you take a `.js` file and rename it to `.ts`, the TypeScript compiler will accept it as-is and produce the same JavaScript back. You can adopt TypeScript one file at a time, without rewriting anything. "**Statically-typed**" means every variable, parameter, and return value has a type known to the compiler at compile time — the same idea as in Rust and Go, where type mismatches are caught before the program runs. "**Compiles to plain JavaScript**" means TypeScript does not run directly: the `.ts` files you write are translated into ordinary `.js` files, which then run on Node.js or in a browser.

The language was created at **Microsoft** by a team led by **Anders Hejlsberg**. The name is essential to the story. Hejlsberg is one of the most influential language designers alive today. He started his career at Borland in the 1980s, where he wrote the compiler for **Turbo Pascal** — a product so fast it felt instant, which changed developers' expectations of what a compiler could be. He then led the design of **Delphi**, Borland's rapid application development tool. In 1996 he moved to Microsoft, where he was the chief architect of **C#** (pronounced "see sharp") — the language that powers the .NET framework, Unity game engine, and countless Windows applications. When Microsoft needed someone to bring type safety to JavaScript, they turned to the same person who had already brought type safety to C and Pascal.

The motivation was clear. JavaScript was originally designed in 1995 by Brendan Eich at Netscape in just ten days, intended for small browser scripts — form validation, button clicks, simple animations. By the late 2000s, JavaScript was being used to build applications with hundreds of thousands of lines: full-blown single-page apps, browser-based office suites, and complex backend services running on Node.js. JavaScript was never designed for code at that scale. It has dynamic typing (no compiler catches type errors), confusing type coercion (`"5" + 3` is `"53"`), and limited tooling. Hejlsberg's team started an internal project around 2010 to add static types to JavaScript — originally code-named "Strada" and used to build parts of the Bing search engine — and publicly announced it as **TypeScript in October 2012**. Version 1.0 shipped in **2014**. Since then, TypeScript has had steady yearly releases (5.0 shipped in March 2023, 5.3 in November 2023) and has become the standard for new web projects.

---

## 2. JavaScript vs TypeScript

The best way to understand what TypeScript adds is to see the same program in both languages, side by side. Here is a small JavaScript function that greets a user by name, plus a call to it:

```typescript
// This is plain JavaScript — note: no type annotations
function greet(name) {
  return "Hello, " + name;
}
console.log(greet("Hieu"));   // prints "Hello, Hieu"
console.log(greet(42));        // prints "Hello, 42" — silent bug!
```

JavaScript does not know that `name` is supposed to be a string. The function works fine when called with `"Hieu"`, but it also "works" when called with `42` — it just coerces the number into a string and produces `"Hello, 42"`. This is not an error in JavaScript; it is silent coercion. In a small program you might notice. In a 50,000-line codebase with hundreds of function calls, you might not, and the bug ships to production.

Here is the same program in TypeScript:

```typescript
function greet(name: string): string {
  return "Hello, " + name;
}
console.log(greet("Hieu"));   // prints "Hello, Hieu"
console.log(greet(42));        // COMPILE ERROR — 42 is not assignable to string
```

Let's break down what changed. After `name`, we added `: string`. This is a **type annotation**: it tells the compiler that the `name` parameter must be a string. After the closing parenthesis, we added `: string` again — this is the **return type annotation**, telling the compiler the function returns a string. Now look at the second call: `greet(42)`. The TypeScript compiler sees `42` (a `number`) being passed to a parameter declared as `string`, and it refuses to compile the program. The error message looks something like `Argument of type 'number' is not assignable to parameter of type 'string'`. The bug is caught before the program runs — at compile time, not at runtime.

Notice that the function body is unchanged. TypeScript did not change what the function does — it only added a contract that the compiler checks. And when TypeScript compiles this file to JavaScript, the type annotations are **erased**: the output `greet` function in the `.js` file looks exactly like the JavaScript version above. Types are a compile-time concept only; they do not exist at runtime.

---

## 3. Installing TypeScript

Before you can write TypeScript, you must install the TypeScript compiler — the tool that translates `.ts` files into `.js` files. The compiler is a small command-line program called `tsc` (short for "TypeScript Compiler"). It is distributed as an npm package, so you first need Node.js and npm installed.

### Step 1: Install Node.js LTS

Go to **[nodejs.org](https://nodejs.org/)** and download the **LTS version** (Long Term Support — the most stable release). On Windows, run the MSI installer; on Mac, run the PKG installer or use Homebrew (`brew install node`); on Linux, use your distribution's package manager or [nvm](https://github.com/nvm-sh/nvm) (Node Version Manager — recommended for Linux because it lets you install multiple Node versions side by side). When the installer finishes, open a fresh terminal and verify both tools are available:

```bash
node --version
npm --version
```

You should see version banners like `v20.11.0` and `10.2.4`. If you see `command not found`, close every terminal window and open a new one so the PATH change takes effect. Any Node version 18 or newer will work for this entire module. Node ships with `npm`, the Node Package Manager, which is how you will install TypeScript and any other JavaScript libraries.

### Step 2: Install the TypeScript Compiler

With Node installed, you can now install TypeScript globally so the `tsc` command is available from any folder. Run this single command:

```bash
npm install -g typescript
```

Let's break this down. `npm install` is the command that downloads a package from the npm registry. The `-g` flag means "global" — install the package into a system-wide location so its command (`tsc`) is available from any folder, not just inside the current project. `typescript` is the name of the npm package that contains the compiler. When installation finishes, verify by checking the version:

```bash
tsc --version
```

You should see something like `Version 5.3.3`. If you see `command not found`, the global npm bin directory is not on your PATH. Fix this by adding it — on Mac/Linux, run `npm config get prefix` to find where global packages are installed, then add the `bin` subfolder of that directory to your PATH in `~/.bashrc` or `~/.zshrc`. On Windows, restart your terminal.

There are modern alternatives to the global `tsc` workflow. **`tsx`** is a faster runner that compiles and runs `.ts` files in one step with no separate compile — install it with `npm install -g tsx` and then run `tsx hello.ts` directly. **`bun`** is a newer JavaScript runtime (written in Zig) that runs TypeScript natively without compilation — install it from [bun.sh](https://bun.sh/). For this module we will use `tsc` for the educational value of seeing the compile step explicitly, then introduce `tsx` in Lesson 02 for everyday development.

---

## 4. Your First Program

It is finally time to write TypeScript. Open a terminal, create a folder for your TypeScript experiments (call it `ts-playground` or whatever you like), and inside it create a file called `hello.ts` with your code editor. Type this exact code into the file:

```typescript
const message: string = "Hello, World!";
console.log(message);
```

Save the file. Then compile it with `tsc`:

```bash
tsc hello.ts
```

This command reads `hello.ts`, type-checks it, and writes a new file called `hello.js` in the same folder. Now run that JavaScript file with Node:

```bash
node hello.js
```

You should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, type-checked, compiled, and ran your first TypeScript program.** Let's break the program down character by character, because every part matters.

- `const` — the keyword that declares a **constant variable**. A `const` cannot be reassigned to a new value after initialization. This is the modern (ES6+) replacement for the old `var` keyword, which has confusing scoping rules and should be avoided. Use `const` by default; use `let` only if you genuinely need to reassign the variable later.
- `message` — the name of the variable. JavaScript and TypeScript use camelCase by convention — lowercase first word, capital first letter of each subsequent word (`firstName`, `totalPrice`, `isLoggedIn`). Snake_case is uncommon in JS/TS codebases.
- `: string` — the **type annotation**. The colon is followed by the type name. This tells the compiler that `message` will always hold a string value. If you later write `message = 42;`, the compiler refuses with `Type 'number' is not assignable to type 'string'`.
- `=` — the assignment operator that assigns the value on the right to the variable on the left.
- `"Hello, World!"` — a **string literal**. Anything between matching double quotes (or single quotes — TypeScript accepts both) is text data. TypeScript also supports backticks for template literals (covered in Lesson 03).
- `;` — the semicolon that ends the statement. Unlike Python (which uses newlines) but like C, Java, Rust, and Go, TypeScript uses semicolons. Modern formatters like Prettier will insert them for you automatically.
- `console.log(message)` — the statement that prints `message` to the terminal. `console.log` is JavaScript's equivalent of Python's `print()` or Rust's `println!`. It accepts any number of arguments.
- `;` — ends the second statement.

If you open `hello.js` in your editor now, you will see this (plain JavaScript, no types):

```typescript
const message = "Hello, World!";
console.log(message);
```

Notice that the `: string` annotation is gone. The compiler erased it. This is a key point: TypeScript's type system exists only at compile time. At runtime, your code is plain JavaScript — no types, no annotations, no interfaces, no generics. They are all stripped out by `tsc` before the code runs. This is why TypeScript is fully compatible with JavaScript: the output is just JavaScript.

---

## 5. TypeScript's Big Promise

Now that you have a working installation, let's talk about why TypeScript is worth your time. TypeScript makes one big promise, and the rest of this module is essentially a tour of how that promise is kept.

**Type errors at compile time instead of runtime.** The promise is this: if a type error exists in your code, the TypeScript compiler will catch it before your program ever runs — usually while you are still typing, because VS Code runs the compiler in the background and underlines errors in red as you type. This is the same value proposition as Rust and Go, applied to the JavaScript ecosystem. The result is fewer bugs in production, faster development cycles (because you fix mistakes immediately, not after a full test run), and code that documents itself through its types.

Here is a famous example. Suppose you have a function that greets a user:

```typescript
function greet(name) {
  return "Hello, " + name;
}
greet(42);   // returns "Hello, 42"
```

In JavaScript, this runs without error. `greet(42)` returns `"Hello, 42"`, which is probably not what the developer intended. The bug may not surface until the user sees `"Hello, 42"` in production, perhaps as part of a personalized email or a UI greeting. At that point, debugging requires tracing where `42` came from — possibly from a database column, an API response, or a misconfigured form field. Now consider the TypeScript version with a type annotation:

```typescript
function greet(name: string): string {
  return "Hello, " + name;
}
greet(42);   // COMPILE ERROR
```

The TypeScript compiler stops with: `Argument of type 'number' is not assignable to parameter of type 'string'.` The developer sees this error in their editor the moment they save the file — before they commit, before they push, before any user sees anything. The fix is obvious: either pass a string (`greet("Hieu")`) or change the function signature to accept numbers. Either way, the bug never leaves the developer's machine.

This is the entire value proposition of TypeScript. The compile-time checks catch the most common classes of JavaScript bugs — undefined property access, wrong argument types, missing object properties, incorrect API response shapes — before they ever run. Studies of codebases migrated from JavaScript to TypeScript report roughly a 15% reduction in bugs that reach production. The rest of this module teaches you how to use the type system to get those benefits in your own code.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `node --version`, `npm --version`, and `tsc --version`. Write down all three version numbers. Then open a fresh file in VS Code, save it as `version.ts`, and type `const versions: string = "Node + TS installed";` followed by `console.log(versions);`. Compile with `tsc version.ts` and run with `node version.js`. If you see `Node + TS installed` printed, your installation is complete and ready for the rest of the module.

### Exercise 2: Catch a Type Error

Create a new file called `bug.ts` and type this code:

```typescript
function double(x: number): number {
  return x * 2;
}
console.log(double(21));
console.log(double("21"));
```

Save the file and run `tsc bug.ts`. You should see a compiler error pointing at `double("21")` saying that `string` is not assignable to `number`. This is TypeScript catching a bug before runtime. Now change `"21"` to `21` (without quotes) and recompile — the error disappears and a `bug.js` file is produced. Run `node bug.js` to see `42` printed twice. Notice that you fixed the bug by changing the call site, not the function — the function was correct all along.

### Exercise 3: Try the Playground

Open the **[TypeScript Playground](https://www.typescriptlang.org/play)** in your browser. Paste the `bug.ts` code from Exercise 2 into the left panel. As you type, watch the right panel show the compiled JavaScript, and watch red squiggly lines appear under the errors. Hover over the `double` function name — a tooltip appears showing its full signature `function double(x: number): number`. This hover-and-see-types behavior is what makes TypeScript so pleasant to work with in VS Code. The Playground is a great place to experiment without installing anything.

---

## Common Mistakes

### Mistake 1: Forgetting to compile the `.ts` file

```bash
# WRONG — node cannot run .ts files directly
node hello.ts
```

```bash
# RIGHT — compile first, then run the .js
tsc hello.ts && node hello.js
```

Node.js does not understand TypeScript syntax — it only runs JavaScript. If you try to run a `.ts` file with `node hello.ts`, you will get a `SyntaxError: Unexpected token ':'` pointing at the first type annotation. The fix is to compile first with `tsc`, which produces a `.js` file, then run that. (In Lesson 02 you will learn about `tsx` and `ts-node`, which hide this two-step workflow by compiling in memory before running.)

### Mistake 2: Using `var` instead of `let`/`const`

```typescript
// WRONG — var has confusing function-scoping and hoisting
var x = 5;
if (true) {
  var x = 10;   // same x! reassigns the outer one
}
console.log(x);   // prints 10 — surprising!
```

```typescript
// RIGHT — const by default, let if you need to reassign
const x = 5;
if (true) {
  const x = 10;   // a different x, scoped to the if block
}
console.log(x);   // prints 5 — predictable
```

`var` is the old (pre-2015) way to declare variables. It has two notorious problems: it is **function-scoped** (not block-scoped, so it leaks out of `if` and `for` blocks) and it is **hoisted** (the declaration moves to the top of the scope, which produces confusing behavior with closures). Modern JavaScript and TypeScript use `const` by default and `let` when reassignment is genuinely needed. Never use `var` in new code. Linters like ESLint will flag it as an error by default.

### Mistake 3: Expecting types to exist at runtime

```typescript
// WRONG — typeof returns the JS type, not the TS annotation
const x: number = 5;
console.log(typeof x);   // prints "number" — but only because JS knows numbers
console.log(x instanceof number);   // ReferenceError: number is not defined
```

```typescript
// RIGHT — TS types are erased; use typeof for primitives
const x: number = 5;
console.log(typeof x === "number");   // true
```

TypeScript's type annotations (`: number`, `: string`, `: Person`) exist only at compile time. When `tsc` compiles your code to JavaScript, those annotations are stripped out entirely. At runtime, the value `x` is just a JavaScript number — there is no "TypeScript" anything attached to it. This is why `typeof x` returns `"number"` (the JavaScript type), and why you cannot write `if (x instanceof number)` — `number` is not a runtime value, it is a TypeScript type. This is also why you cannot do `if (typeof x === "Person")` — `Person` does not exist at runtime, only at compile time. To check the type of an object at runtime, you must inspect its shape (`"name" in obj`) or use a discriminated union (covered in Lesson 06).

### Mistake 4: Treating `any` as a real type

```typescript
// WRONG — any turns off the type checker entirely
function process(data: any) {
  return data.users[0].name.toUpperCase();
}
process({ user: "Hieu" });   // crashes at runtime — "users" is undefined
```

```typescript
// RIGHT — use unknown and narrow, or use a proper interface
interface UserData { users: { name: string }[]; }
function process(data: UserData) {
  return data.users[0].name.toUpperCase();
}
process({ users: [{ name: "Hieu" }] });   // works
```

`any` is not a type — it is an **escape hatch** that tells the compiler "don't check this." When you annotate something as `any`, you turn off type checking for that value entirely. The compiler will happily let you call `.users[0].name.toUpperCase()` on something that is actually `{ user: "Hieu" }`, and the bug only surfaces at runtime when your code crashes with `Cannot read properties of undefined`. `any` is sometimes necessary (when migrating from JavaScript, when working with truly dynamic data), but it should be a last resort. Lesson 03 introduces `unknown`, the safe alternative — you can assign anything to `unknown`, but you must narrow it before using.

---

## Summary

- TypeScript is a statically-typed superset of JavaScript that compiles to plain JavaScript — every `.js` file is also a valid `.ts` file.
- It was created at Microsoft by Anders Hejlsberg (designer of Turbo Pascal, Delphi, and C#), used internally since 2010, publicly announced in October 2012, and shipped v1.0 in 2014.
- TypeScript exists because JavaScript was designed in 1995 for small browser scripts but is now used for applications with hundreds of thousands of lines — it needed type safety to scale.
- TypeScript adds type annotations (`: string`, `: number`), interfaces, generics, and better tooling — then erases all of those when compiling to JavaScript.
- Install Node.js LTS from [nodejs.org](https://nodejs.org/), then install TypeScript globally with `npm install -g typescript`; verify with `tsc --version`.
- Your first program is `const message: string = "Hello, World!";` — compile with `tsc hello.ts`, run with `node hello.js`.
- TypeScript's big promise: type errors are caught at compile time, not at runtime — usually in your editor before you save.
- Types are erased at runtime — `typeof x` returns the JavaScript type, not the TypeScript annotation.

You wrote, compiled, and ran your first TypeScript program. The hardest step — getting the toolchain working — is done. In Lesson 02 we will set up a real TypeScript project with a `tsconfig.json` file, learn the three different ways to run TypeScript code, and explore why `strict: true` is the single most important setting in your project.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
