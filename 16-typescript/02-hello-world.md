# Lesson 02: Hello, World!

> Module: TypeScript · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you installed Node.js and TypeScript, wrote a one-file program, and compiled it with `tsc hello.ts`. That workflow is fine for tiny experiments, but real TypeScript projects need more: a project folder, a `package.json` file to track dependencies, a `tsconfig.json` file to configure the compiler, a `src/` folder for source code, and a `dist/` folder for compiled output. This lesson shows you how to set all of that up properly, then walks you through the three different ways to run TypeScript code, and ends with the single most important setting in your project: `strict: true`.

You will write a more substantial program in this lesson than in Lesson 01 — multiple files, imports, exports, comments, and proper logging. By the end you will have a reusable project skeleton that you can copy for every TypeScript exercise in the rest of this module.

---

## Learning Objectives

After this lesson, you will be able to:

1. Initialize a TypeScript project with `npm init`, install dependencies, and generate a `tsconfig.json` file.
2. Explain the purpose of the key `tsconfig.json` options: `target`, `module`, `strict`, `outDir`, `rootDir`.
3. Run TypeScript code three different ways: compile-then-run, `tsx`, and `ts-node`.
4. Explain why `strict: true` is the most important compiler setting and what specific bugs it catches.

---

## 1. Setting Up a Project

For anything more than a one-file experiment, you want a real TypeScript project structure. The structure has four files: `package.json` (tracks dependencies and scripts), `tsconfig.json` (configures the compiler), `src/index.ts` (your entry point), and `dist/` (the output folder, created automatically when you compile). Let's set it up step by step.

Open a terminal and create a new folder for your project. Then initialize a `package.json` file, install TypeScript and the Node type definitions, and generate a `tsconfig.json`:

```bash
mkdir hello-ts
cd hello-ts
npm init -y
npm install -D typescript @types/node
npx tsc --init
```

Let's break down each command. `mkdir hello-ts` creates the folder. `cd hello-ts` moves into it. `npm init -y` creates a `package.json` file — the `-y` flag accepts all the defaults (project name, version, entry point) without prompting. The `package.json` file tracks your project's dependencies, scripts, and metadata, similar to `Cargo.toml` in Rust or `go.mod` in Go.

`npm install -D typescript @types/node` installs two packages. The `-D` flag means "development dependency" — these packages are needed only during development, not at runtime (when your code runs as plain JavaScript, TypeScript is not involved). `typescript` is the compiler. `@types/node` is a special package: it provides TypeScript type definitions for Node.js's built-in modules like `fs`, `path`, `http`, and `process`. Without `@types/node`, TypeScript does not know the types of Node's APIs and shows errors when you import them.

Finally, `npx tsc --init` creates a `tsconfig.json` file. `npx` is a tool that ships with npm — it runs a command from a locally-installed package without needing it to be globally installed. Since you installed `typescript` locally (with `-D`), `tsc` is not on your PATH directly. `npx tsc` finds it in your project's `node_modules/.bin/` folder and runs it. The `--init` flag tells `tsc` to generate a default `tsconfig.json` file. You should now have a `tsconfig.json` file in your project folder.

Open `tsconfig.json` in your editor. It will be long and full of commented-out options. The important ones to set are `target`, `module`, `strict`, `outDir`, and `rootDir`. Edit the file so the active (uncommented) lines look like this:

```jsonc
{
  "compilerOptions": {
    "target": "ES2022",          // which JS version to emit
    "module": "ESNext",          // module system (ESNext = modern ESM)
    "moduleResolution": "bundler", // how to resolve imports
    "strict": true,              // turn on all strict checks
    "outDir": "./dist",           // where to put compiled .js files
    "rootDir": "./src",           // where your .ts files live
    "esModuleInterop": true,     // makes CommonJS imports work nicely
    "skipLibCheck": true,        // skip type-checking of .d.ts files (faster)
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

Let's explain each option. `target` tells the compiler which version of JavaScript to produce. `ES2022` means your `.js` files can use modern features like top-level `await`, private class fields, and `??=` (logical assignment). If you are targeting older browsers or old Node versions, set this lower (e.g. `ES2017`); for modern Node 18+, `ES2022` is correct. `module` controls how imports and exports are emitted — `ESNext` produces modern ESM `import`/`export` statements, while `CommonJS` produces `require()`/`module.exports` (the older Node.js standard). `moduleResolution: "bundler"` tells TypeScript to resolve imports the way modern bundlers (Vite, esbuild, webpack) do — it is the most permissive and modern setting.

`strict: true` is the most important option. It turns on a bundle of strict type-checking rules: no implicit `any`, strict null checks, strict function types, strict property initialization, and more. We will revisit it in Section 6 below. `outDir` is where compiled JavaScript goes — `./dist` is the convention, short for "distribution". `rootDir` is where TypeScript looks for `.ts` source files — `./src` is the convention. `esModuleInterop: true` makes CommonJS imports work cleanly with ESM-style `import` syntax. `skipLibCheck: true` speeds up compilation by skipping type-checking of third-party `.d.ts` files (you trust them). `forceConsistentCasingInFileNames: true` prevents bugs on case-insensitive filesystems (Mac, Windows) where `import "./User"` and `import "./user"` are treated as the same file.

The `"include": ["src"]` at the bottom tells TypeScript to only compile files inside the `src/` folder. This is important — without it, `tsc` might try to compile files in `node_modules/` or other folders, which would be slow and produce errors.

Now create the `src/` folder and put a file in it:

```bash
mkdir src
```

Create `src/index.ts` in your editor with the contents you will write in Section 3.

---

## 2. Anatomy of a TypeScript File

A TypeScript file typically has four kinds of content, in this order: imports at the top, top-level `const`/`let` declarations, function and class definitions, and exports at the bottom. Here is a small but complete example that demonstrates the structure:

```typescript
import { readFileSync } from "node:fs";

const filename: string = "hello.txt";

function readGreeting(path: string): string {
  return readFileSync(path, "utf8").trim();
}

export { readGreeting, filename };
```

Let's break this down line by line. Line 1 is an `import` statement — it imports the `readFileSync` function from Node's built-in `fs` (filesystem) module. The `node:fs` prefix is the modern way to import Node built-ins (the `node:` prefix is unambiguous and works in both ESM and CommonJS). Lines like this go at the top of the file, in alphabetical order by convention.

Line 3 is a top-level `const` declaration. `filename` is a string constant set to `"hello.txt"`. The `: string` annotation tells the compiler the type. Top-level declarations like this can be referenced from anywhere else in the file — they have module scope (sometimes called "file scope").

Lines 5–7 are a function declaration. The function `readGreeting` takes a parameter `path: string` and returns a string. The body calls `readFileSync` with the path and the encoding `"utf8"` (which tells Node to return a string rather than a Buffer), then calls `.trim()` to remove leading/trailing whitespace. The `return` type annotation `: string` after the closing parenthesis tells the compiler what the function returns.

Line 9 is an `export` statement. The `export { readGreeting, filename }` syntax makes those two names available to any other file that imports from this one. Without `export`, the names are private to this file. TypeScript uses the same module system as modern JavaScript — ESM with `import` and `export`.

Notice there is no `main()` function. Unlike C, Java, Rust, or Go, JavaScript and TypeScript do not have a designated entry point. Code runs top-to-bottom: when you run `node dist/index.js`, Node executes the file from top to bottom, defining the imports, evaluating the `const` declarations, defining the function, and then running whatever statements are at the top level. To make this file actually do something when run, we need to add a top-level call:

```typescript
import { readFileSync } from "node:fs";

const filename: string = "hello.txt";

function readGreeting(path: string): string {
  return readFileSync(path, "utf8").trim();
}

console.log(readGreeting(filename));
```

Now when you run this file, it will print the contents of `hello.txt` to the terminal. We removed the `export` since this file is meant to be run, not imported.

---

## 3. Running Your Code

There are three main ways to run TypeScript code, each useful in different situations. Let's cover all three, then set up `package.json` scripts to make them easy to use.

### Method 1: Compile, then run (the explicit way)

This is the method you used in Lesson 01. Compile the `.ts` file to `.js`, then run the `.js` file with Node:

```bash
npx tsc
node dist/index.js
```

Note that we run `npx tsc` with no arguments — when called with no arguments, `tsc` reads `tsconfig.json` and compiles every `.ts` file in `src/`, outputting `.js` files to `dist/`. This is the standard build step. Then `node dist/index.js` runs the compiled entry point. This two-step workflow is what production builds use, because the compiled `.js` files are what you ship to servers or upload to a CDN.

The downside is the two-step nature — you have to remember to recompile after every change. For development, you usually want something faster.

### Method 2: `tsx` (the modern fast way)

`tsx` is a fast TypeScript runner that compiles your code in memory and runs it immediately, without producing any `.js` files. Install it as a development dependency:

```bash
npm install -D tsx
```

Then run a TypeScript file directly:

```bash
npx tsx src/index.ts
```

Notice there is no separate compile step — `tsx` does it all in one command. This is the recommended workflow for development in 2024. `tsx` uses `esbuild` under the hood (a Go-based bundler that is one to two orders of magnitude faster than `tsc`), so it starts essentially instantly. It does not do full type checking — that is `tsc`'s job — but for running code quickly during development, it is unbeatable.

`tsx` also has a watch mode that re-runs your code automatically whenever you save:

```bash
npx tsx watch src/index.ts
```

Save the file in your editor and the program re-runs immediately. This is the modern equivalent of `nodemon`, but for TypeScript.

### Method 3: `ts-node` (the classic way)

`ts-node` is the older TypeScript runner, used widely before `tsx` existed. It works the same way — compile in memory, then run — but is slower than `tsx` because it uses the `tsc` compiler internally:

```bash
npm install -D ts-node
npx ts-node src/index.ts
```

You will see `ts-node` in many tutorials and older projects. It is still maintained and still works fine, but for new projects in 2024, prefer `tsx`. Pick one runner for your project and stick with it — mixing `tsx` and `ts-node` in the same project can cause subtle issues with how types and module resolution are configured.

### Setting up `package.json` scripts

Rather than typing long `npx` commands every time, put them in the `scripts` section of `package.json`. Open `package.json` and edit the `scripts` section to look like this:

```jsonc
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsx watch src/index.ts"
  }
}
```

Now you can run:

- `npm run build` — compiles all TypeScript to `dist/`.
- `npm start` — runs the compiled entry point.
- `npm run dev` — starts `tsx` in watch mode, re-running on every save.

These three scripts cover 90% of what you will do during development.

---

## 4. Console Output

The most common way to produce output in TypeScript (and JavaScript) is the `console` object. It has three methods you will use constantly:

```typescript
console.log("Hello, World!");              // standard output
console.error("Something went wrong");    // standard error (stderr)
console.table([
  { name: "Hieu", age: 25 },
  { name: "Anna", age: 30 },
]);
```

Let's break each one down. `console.log` is the everyday logging function — it prints its arguments to stdout (standard output) followed by a newline. You can pass multiple arguments separated by commas, and they will be space-separated in the output: `console.log("x =", 5)` prints `x = 5`.

`console.error` is identical to `console.log` except it writes to stderr (standard error) instead of stdout. This distinction matters when you redirect output: `node index.js > output.txt` sends `console.log` to the file but `console.error` still appears in the terminal. Use `console.error` for error messages, warnings, and diagnostic output that should not be mixed with normal program output.

`console.table` is a hidden gem: it takes an array of objects (or an object of objects) and prints them as a nicely formatted ASCII table. The example above would print:

```text
┌─────────┬───────┬─────┐
│ (index) │ name  │ age │
├─────────┼───────┼─────┤
│ 0       │ 'Hieu' │ 25 │
│ 1       │ 'Anna' │ 30 │
└─────────┴───────┴─────┘
```

This is incredibly useful for debugging arrays of objects — you can see at a glance what data you are working with.

---

## 5. Comments

TypeScript has three kinds of comments. The first two are the same as in JavaScript; the third — JSDoc — is where TypeScript adds special value.

```typescript
// This is a single-line comment. It goes to the end of the line.

/* This is a block comment.
   It can span multiple lines.
   Block comments are rarely used in TypeScript codebases —
   prefer // for everything. */

/**
 * This is a JSDoc comment. The leading * on each line is required.
 * TypeScript reads these and uses them for editor tooltips.
 *
 * @param name - the name of the person to greet
 * @returns a greeting string
 */
function greet(name: string): string {
  return "Hello, " + name;
}
```

Line comments start with `//` and go to the end of the line. They are the most common kind. Block comments start with `/*` and end with `*/`, and can span multiple lines. In practice, TypeScript developers almost never use block comments — `//` is preferred even for multi-line comments (just put `//` at the start of each line).

JSDoc comments start with `/**` (note the double asterisk) and end with `*/`. They are special because TypeScript reads them and uses them to provide editor tooltips. When you hover over the `greet` function in VS Code, you will see the JSDoc comment rendered as a tooltip, including the `@param` and `@returns` descriptions. This is how you document your code in a way that other developers (and your future self) can discover through the IDE. JSDoc is also how you can add types to plain JavaScript — but in TypeScript, you usually use real type annotations instead.

---

## 6. Strict Mode

The single most important setting in your `tsconfig.json` is `"strict": true`. Without it, TypeScript is little more than a fancy JavaScript linter — it lets through the very bugs you adopted TypeScript to catch. With it, TypeScript becomes a real type system that catches the most common classes of bugs.

`strict: true` is a shorthand for turning on eight individual strict checks at once. Let's cover the most important four. **`noImplicitAny`** makes the compiler error if it has to infer `any` for a parameter or variable. Without this, a function declared `function f(x) { return x + 1; }` would have its `x` parameter silently typed as `any`, defeating the point of TypeScript. With it, you get an error: `Parameter 'x' implicitly has an 'any' type.` Either add an annotation (`x: number`) or, if you genuinely cannot know the type, write `x: unknown` (which forces narrowing before use).

**`strictNullChecks`** is the second most important. In JavaScript, the values `null` and `undefined` can sneak into any variable, and accessing a property of `null` (`null.foo`) throws a runtime error. In default TypeScript (without strict mode), the type `string` includes `null` and `undefined` — you can do `const s: string = null;` and TypeScript does not complain. With `strictNullChecks`, `string` no longer includes `null` or `undefined` — you must explicitly write `string | null` if you want to allow them. This catches the most common JavaScript crash: accessing a property of something that turned out to be `null` or `undefined`.

**`strictFunctionTypes`** checks function parameter types bivariantly instead of covariantly — the practical effect is that callbacks with narrower parameter types than expected will now be rejected. **`strictPropertyInitialization`** requires that class properties are initialized either at declaration or in the constructor — preventing the classic JavaScript bug of accessing a property that was never set. Together, these checks (and the others enabled by `strict: true`) catch a significant fraction of the bugs that would otherwise reach production. **Always enable `strict: true` in new projects.** The TypeScript team's official guidance is that `strict` should be the default, and they are gradually moving toward making it impossible to disable.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Set Up the Project

Follow the steps in Section 1 to create a `hello-ts` folder with `package.json`, `tsconfig.json`, `src/`, and the three `package.json` scripts. Then create `src/index.ts` with this content:

```typescript
const message: string = "Hello from my TypeScript project!";
console.log(message);
```

Run `npm run dev` and confirm you see the message printed. Then run `npm run build` and check that a `dist/index.js` file was created. Open `dist/index.js` in your editor — notice the `: string` annotation is gone (it was erased during compilation). Run `npm start` to execute the compiled version.

### Exercise 2: Try All Three Run Methods

Using the same `src/index.ts`, run the same program three different ways:

1. `npx tsc && node dist/index.js` (Method 1: compile, then run)
2. `npx tsx src/index.ts` (Method 2: tsx — install with `npm install -D tsx` first if you have not already)
3. `npx ts-node src/index.ts` (Method 3: ts-node — install with `npm install -D ts-node` first)

Confirm all three produce the same output. Notice that Method 2 (`tsx`) is fastest. Delete the `dist/` folder, then run Method 2 again — confirm that `tsx` produces no `dist/` folder (it compiles in memory only).

### Exercise 3: Use `console.table`

Create a new file `src/table.ts` with this content:

```typescript
const users = [
  { name: "Hieu", age: 25, role: "engineer" },
  { name: "Anna", age: 30, role: "designer" },
  { name: "Bob", age: 28, role: "manager" },
];
console.table(users);
console.log(`Total users: ${users.length}`);
```

Run it with `npx tsx src/table.ts`. Confirm you see a nicely formatted ASCII table. The `${users.length}` syntax is a **template literal** — a string with embedded expressions, surrounded by backticks instead of quotes. We will cover template literals in Lesson 03.

---

## Common Mistakes

### Mistake 1: Forgetting `strict: true`

```jsonc
// WRONG — strict mode is off, so TypeScript will let many bugs through
{
  "compilerOptions": {
    "target": "ES2022",
    "outDir": "./dist"
  }
}
```

```jsonc
// RIGHT — always enable strict mode in new projects
{
  "compilerOptions": {
    "target": "ES2022",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

Without `strict: true`, TypeScript lets through the very bugs you adopted TypeScript to catch. Implicit `any` is allowed, `null` and `undefined` are assignable to any type, function parameters are not strictly checked. The default `tsconfig.json` generated by `tsc --init` has `"strict": true` already enabled by default as of TypeScript 5.0 — leave it on. The TypeScript team's official guidance is that all new projects should use strict mode, and they are gradually moving toward making it impossible to disable. If you find yourself wanting to turn `strict` off, you are usually trying to migrate legacy JavaScript — better to enable `strict` and fix the resulting errors one at a time.

### Mistake 2: Mixing `ts-node` and `tsx` in the same project

```bash
# WRONG — both runners configured in package.json, conflicting settings
npm install -D ts-node tsx
```

```bash
# RIGHT — pick one runner and use it consistently
npm install -D tsx
```

`ts-node` and `tsx` are both TypeScript runners, but they configure the compiler differently. `ts-node` uses the `tsc` compiler with whatever `tsconfig.json` options you have set. `tsx` uses `esbuild`, which is faster but ignores some `tsconfig.json` options (like `strict` — esbuild does not do type checking at all). If you install both, you will get different behavior depending on which one you run, and the differences are subtle and confusing. Pick one — `tsx` is the recommendation for 2024 — and uninstall the other. If you need type checking, run `tsc --noEmit` separately.

### Mistake 3: Wrong `target` version

```jsonc
// WRONG — target ES5 produces old, verbose JavaScript
{
  "compilerOptions": {
    "target": "ES5"
  }
}
```

```jsonc
// RIGHT — target ES2022 produces modern, clean JavaScript for Node 18+
{
  "compilerOptions": {
    "target": "ES2022"
  }
}
```

The `target` option tells TypeScript which version of JavaScript to produce. `ES5` (the old default) is JavaScript from 2009 — it does not have `let`/`const`, arrow functions, classes, async/await, or any modern feature. Targeting `ES5` makes TypeScript emit verbose polyfills for all those features (turning `const x = 5` into `var x = 5`, arrow functions into regular functions, etc.). For Node 18+ or modern browsers, this is wasted bytes and slower code. Use `ES2022` (or even `ESNext`) for any modern target. Only use `ES5` if you genuinely need to support ancient browsers — Internet Explorer 11 was the last holdout, and it was officially retired in 2022.

### Mistake 4: Running `tsc` without `--init` first

```bash
# WRONG — running tsc with no tsconfig.json file
tsc src/index.ts
```

```bash
# RIGHT — create tsconfig.json first, then run tsc with no args
tsc --init
tsc
```

When you run `tsc src/index.ts` (with a file argument), TypeScript ignores any `tsconfig.json` file entirely and uses the default options — which means no `strict`, no `outDir`, no `rootDir`, and emitting the `.js` file right next to the `.ts` file. This is almost never what you want. The correct workflow is to first run `tsc --init` once (creating `tsconfig.json`), configure it, and then run `tsc` with no arguments — TypeScript reads `tsconfig.json` and uses all your settings. The rule: if you have a `tsconfig.json`, never pass a file argument to `tsc`. Just run `tsc`.

---

## Summary

- Set up a TypeScript project with `npm init -y`, `npm install -D typescript @types/node`, and `npx tsc --init`.
- Configure `tsconfig.json` with `target: ES2022`, `module: ESNext`, `strict: true`, `outDir: ./dist`, `rootDir: ./src`, and `"include": ["src"]`.
- A TypeScript file has four sections: imports, top-level declarations, function/class definitions, and exports.
- Three ways to run TypeScript: `tsc && node dist/index.js` (explicit compile + run), `tsx src/index.ts` (modern, fast, no compile step), `ts-node src/index.ts` (classic).
- Set up `package.json` scripts: `"build": "tsc"`, `"start": "node dist/index.js"`, `"dev": "tsx watch src/index.ts"`.
- Use `console.log` for stdout, `console.error` for stderr, `console.table` for nicely formatted array output.
- Comments: `//` for lines, `/* */` for blocks (rare), `/** */` for JSDoc (TypeScript reads these for editor tooltips).
- `strict: true` is the most important compiler setting — it enables `noImplicitAny`, `strictNullChecks`, `strictFunctionTypes`, `strictPropertyInitialization`, and more.

You now have a reusable TypeScript project skeleton and know how to run code three different ways. In Lesson 03 we will explore TypeScript's type system in depth: primitive types, arrays, tuples, enums, and the important distinction between `any`, `unknown`, `void`, and `never`.

---

**Next:** [Lesson 03: Basic Types →](./03-basic-types.md)
