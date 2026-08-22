# Lesson 04: Interfaces and Types

> Module: TypeScript · Lesson 4 of 7
> Estimated time: 30–45 minutes

Most real-world data in TypeScript is not a primitive — it is an object with multiple properties. A user has a name, age, and email; an order has an id, items, and a total; an HTTP response has a status code, headers, and a body. To describe the shape of these objects, TypeScript gives you two tools: the `interface` keyword and the `type` alias. Both can describe object shapes, and they overlap heavily — but they have important differences, and knowing when to use which is one of the most common sources of confusion for new TypeScript developers.

This lesson covers both, the differences between them, and the features they share: optional properties, readonly properties, index signatures, and extension. By the end you will be able to model any JSON-like data structure in TypeScript and know which keyword to reach for in each situation.

---

## Learning Objectives

After this lesson, you will be able to:

1. Describe object shapes using inline types, `interface`, and `type` aliases.
2. Choose between `interface` and `type` correctly based on the situation.
3. Use optional (`?`) and `readonly` modifiers to express partial and immutable properties.
4. Extend interfaces, use index signatures for arbitrary-key objects, and avoid the common pitfalls.

---

## 1. Object Types

The simplest way to describe an object's shape is to write the shape **inline** as a type annotation. This is called an **object type literal**:

```typescript
const p: { name: string; age: number } = {
  name: "Hieu",
  age: 25,
};
```

Let's break this down. The type `{ name: string; age: number }` is an object type that says "an object with a `name` property of type `string` and an `age` property of type `number`". The semicolons separate property declarations — you can also use commas or newlines, but semicolons are conventional. The variable `p` is assigned an object literal that matches this shape exactly.

Inline object types are convenient for one-off cases — a parameter to a function, a return type — but they get verbose when you need to reuse the same shape in multiple places. That is where `interface` and `type` aliases come in: they let you give a name to an object shape so you can refer to it by name rather than repeating the inline type everywhere.

TypeScript uses **structural typing** for object types — also called "duck typing". This means two object types are considered compatible if they have the same shape, regardless of whether they were declared with the same name. This is different from Java or C# which use **nominal typing** — two types are compatible only if they are explicitly related through inheritance. We will revisit this in Section 4.

---

## 2. `interface`

An **interface** is a named description of an object's shape. It is the classic TypeScript way to describe object types and the one most tutorials teach first:

```typescript
interface Person {
  name: string;
  age: number;
}

const p: Person = {
  name: "Hieu",
  age: 25,
};

function greet(person: Person): string {
  return `Hello, ${person.name}`;
}
```

Let's break this down. `interface Person { ... }` declares an interface named `Person`. Inside the braces, each line declares one property: `name: string` means "the `name` property is a string". Semicolons or commas separate properties; you can also use newlines. Once declared, you can use `Person` as a type annotation anywhere — for variables, function parameters, return types, and so on.

Interfaces have two superpowers that `type` aliases do not. First, **declaration merging**: if you declare two interfaces with the same name in the same scope, TypeScript merges them into one. This is how the standard library extends built-in types — for example, you can extend `Window` to add a custom property:

```typescript
interface Window {
  myCustomProperty: string;
}
// Now window.myCustomProperty is typed as string everywhere
```

Second, interfaces can be **extended** with the `extends` keyword to create new interfaces that add properties:

```typescript
interface Employee extends Person {
  title: string;
  salary: number;
}

const e: Employee = {
  name: "Hieu",
  age: 25,
  title: "Engineer",
  salary: 100000,
};
```

`Employee` extends `Person`, so every `Employee` has all of `Person`'s properties (`name`, `age`) plus its own (`title`, `salary`). You can extend multiple interfaces by separating them with commas: `interface Manager extends Employee, Leader { ... }`.

Interfaces can also be implemented by classes (we will cover classes in a later module) — `class User implements Person { ... }` requires the `User` class to have all properties declared by `Person`. This makes interfaces useful for defining contracts that classes must satisfy.

For describing the shape of an object — especially a public API shape, or a shape that other developers might extend — `interface` is the recommended choice. It has slightly better error messages than `type` aliases, it can be extended and implemented, and declaration merging lets you augment existing interfaces when you need to. The TypeScript team's style guide recommends preferring `interface` for object shapes.

---

## 3. `type` Aliases

A **type alias** (declared with the `type` keyword) is a name for any type — not just objects. You can alias primitives, unions, tuples, function signatures, and more:

```typescript
type Person = {
  name: string;
  age: number;
};

type ID = string | number;

type Pair = [string, number];

type Callback = (data: string) => void;
```

Let's break this down. `type Person = { ... }` declares a type alias named `Person` for the object shape on the right side. The syntax `{ name: string; age: number }` is identical to the inline object type from Section 1 — `type` just gives it a name. Once declared, you can use `Person` anywhere you would use the inline type.

`type ID = string | number` shows the first major advantage of `type` over `interface`: type aliases can describe **union types** (Lesson 06). An interface cannot be a union — interfaces are always object shapes. If you need to give a name to "either a string or a number", you must use `type`.

`type Pair = [string, number]` shows the second advantage: type aliases can describe **tuples**. Interfaces cannot describe tuples.

`type Callback = (data: string) => void` shows the third: type aliases can describe **function signatures**. You can write `interface Callback { (data: string): void; }` (a "call signature" in an interface) but the `type` form is shorter and more readable.

For object shapes, `type` aliases and `interface` are almost identical in capability. Both can be used as type annotations, both can be combined with other types, both can describe objects with optional and readonly properties (covered below). The differences are subtle: interfaces can be extended with `extends` and merged via declaration; type aliases can be combined with intersections (`type A = B & C`). For most object shapes, both work equally well.

---

## 4. `interface` vs `type`

So which should you use? Here is a side-by-side comparison:

| Feature | `interface` | `type` |
|---|---|---|
| Object shapes | ✅ | ✅ |
| Primitives (`type ID = string`) | ❌ | ✅ |
| Unions (`type X = A \| B`) | ❌ | ✅ |
| Tuples (`type Pair = [A, B]`) | ❌ | ✅ |
| Function signatures | ✅ (call signature) | ✅ (cleaner syntax) |
| Extending | `interface B extends A` | `type B = A & C` |
| Declaration merging | ✅ | ❌ |
| Implementing in classes | ✅ | ✅ (if it is an object shape) |
| Better error messages | ✅ | ❌ |

**When to use `interface`:** prefer `interface` for describing object shapes that may be extended or implemented. This includes public API shapes (the shape of an HTTP response, the shape of a config object), class contracts (a class implements an interface), and any object shape that other developers might want to extend. Interfaces give slightly better error messages and support declaration merging, which can be useful when augmenting third-party types.

**When to use `type`:** prefer `type` for unions, tuples, function types, primitives, and any composition that involves those. `type ID = string | number` cannot be an interface. `type Pair = [string, number]` cannot be an interface. `type Callback = (x: string) => void` is cleaner as a type alias than as an interface.

In practice, many TypeScript codebases use both — `interface` for object shapes, `type` for everything else. The TypeScript team's own style guide recommends this approach, and it is what most modern TypeScript projects do. The rule of thumb: **`interface` for object shapes, `type` for everything else.**

It is worth mentioning **structural typing** again here. TypeScript compares types by their shape, not by their name. This means:

```typescript
interface Person { name: string; age: number; }
interface Employee { name: string; age: number; salary: number; }

const e: Employee = { name: "Hieu", age: 25, salary: 100000 };
const p: Person = e;   // OK — Employee has all of Person's properties
```

This assignment works because `Employee` has all the properties that `Person` requires (and more). TypeScript considers `Employee` compatible with `Person` because the *shape* is compatible, regardless of the fact that the names are different. This is called **structural subtyping** and is one of TypeScript's most distinctive features compared to nominal-typed languages like Java or C#.

---

## 5. Optional and Readonly Properties

Real-world objects often have properties that are sometimes present and sometimes not — a user might have an optional middle name, an order might or might not have a discount code. TypeScript lets you mark properties as **optional** with the `?` modifier:

```typescript
interface Person {
  name: string;
  age: number;
  middleName?: string;   // optional — may or may not be present
}

const p1: Person = { name: "Hieu", age: 25 };                 // OK
const p2: Person = { name: "Anna", age: 30, middleName: "M" }; // OK
```

Let's break this down. `middleName?: string` declares `middleName` as an optional property of type string. The `?` after the property name is the modifier. The type of `middleName` is `string | undefined` — when you access `p.middleName`, the type is `string | undefined`, so you must narrow it before using it as a string (Lesson 06 covers narrowing). Without the `?`, every `Person` object must have a `middleName` property or the compiler errors with "Property 'middleName' is missing in type".

The **`readonly`** modifier marks a property as immutable — it can be set only during object initialization, never reassigned afterward:

```typescript
interface Person {
  readonly id: number;
  name: string;
  age: number;
}

const p: Person = { id: 1, name: "Hieu", age: 25 };
p.name = "Anna";   // OK — name is mutable
p.id = 2;          // COMPILE ERROR — id is readonly
```

`readonly id: number` declares `id` as a readonly number. After the object is created, you cannot reassign `p.id`. This is enforced at compile time only — at runtime, JavaScript has no concept of readonly, and the property is just a normal writable property. The `readonly` modifier is a compile-time contract.

You can combine `readonly` and `?`: `readonly nickname?: string` is an optional readonly string. The order is always `readonly` first, then the property name, then `?`, then the type.

Important: `readonly` is **shallow** — it applies only to the top-level property. If a `readonly` property holds an object, you can still mutate that object's inner properties:

```typescript
interface Person {
  readonly address: { city: string; zip: string };
}

const p: Person = { address: { city: "Seattle", zip: "98101" } };
p.address = { city: "Portland", zip: "97201" };  // COMPILE ERROR — address is readonly
p.address.city = "Portland";                       // OK — only the top level is readonly
```

This is a common gotcha. To make an entire object deeply readonly, use the `Readonly<T>` utility type (Lesson 07) or the `as const` assertion.

---

## 6. Index Signatures

Sometimes you have an object whose keys are not known in advance — a dictionary, a map, a cache. TypeScript lets you describe such objects with an **index signature**:

```typescript
interface StringMap {
  [key: string]: string;
}

const colors: StringMap = {
  red: "#ff0000",
  green: "#00ff00",
  blue: "#0000ff",
};

console.log(colors.red);   // "#ff0000"
```

Let's break this down. `[key: string]: string` is an index signature — it says "this object can have any number of string keys, and the value at each key is a string". The name `key` is just a placeholder — you can call it anything (`[k: string]`, `[index: string]`). The important part is the type of the key (`string`, `number`, or `symbol`) and the type of the value (`string` here).

Index signatures are useful for caches, lookup tables, and configuration objects with arbitrary keys. They have one important limitation: every named property must be compatible with the index signature. For example:

```typescript
interface GoodExample {
  [key: string]: string;
  prefix: string;   // OK — string is compatible with [key: string]: string
}

interface BadExample {
  [key: string]: string;
  count: number;    // COMPILE ERROR — number is not assignable to string
}
```

If you have an index signature `[key: string]: string` and also a named property `count: number`, the compiler errors because `count` (a number) is not assignable to `string` (the index signature's value type). The fix is either to widen the index signature's value type to `string | number`, or to remove the named property.

You can also use `number` as an index key type. Numeric keys are interesting because JavaScript objects coerce them to strings — `obj[0]` and `obj["0"]` are the same. TypeScript models this: `[index: number]: string` allows numeric indexing but the keys are still strings at runtime.

---

## 7. Extending Interfaces

Interfaces can be extended with the `extends` keyword to create new interfaces that add properties. We saw a brief example in Section 2 — let's look at the full pattern:

```typescript
interface Animal {
  name: string;
  age: number;
}

interface Dog extends Animal {
  breed: string;
  bark(): void;
}

interface Cat extends Animal {
  indoor: boolean;
}

const d: Dog = {
  name: "Rex",
  age: 3,
  breed: "Labrador",
  bark() { console.log("Woof!"); },
};

const c: Cat = {
  name: "Whiskers",
  age: 5,
  indoor: true,
};
```

Let's break this down. `interface Animal { ... }` declares a base interface with `name` and `age`. `interface Dog extends Animal { ... }` declares `Dog` as a subtype of `Animal` — every `Dog` has all of `Animal`'s properties (`name`, `age`) plus its own (`breed`, `bark`). The same for `Cat`. You can now use `Dog` and `Cat` anywhere an `Animal` is expected, because they are structural subtypes — a `Dog` has all the properties an `Animal` needs.

You can extend multiple interfaces by separating them with commas:

```typescript
interface Pet extends Animal, Trainable {
  owner: string;
}

interface Trainable {
  tricks: string[];
}
```

Now `Pet` has the properties of both `Animal` and `Trainable`, plus its own `owner`.

Type aliases can achieve a similar effect with **intersection types** (Lesson 06 covers these in depth):

```typescript
type Animal = { name: string; age: number };
type Dog = Animal & { breed: string; bark(): void };
```

The `&` operator intersects two types — the result has the properties of both. This is functionally equivalent to interface extension for object types. The choice between `extends` (interface) and `&` (type) is mostly stylistic: use `extends` when working with interfaces, use `&` when working with type aliases or when combining non-object types.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Describe a User

Create an `interface User` with these properties: `id: number`, `name: string`, `email: string`, `age?: number` (optional), `readonly createdAt: Date`. Then declare a `const` of type `User` with all the required properties. Try to reassign `user.createdAt` — confirm the compiler refuses. Try to access `user.age?.toFixed(0)` — notice the `?.` (optional chaining) which is needed because `age` could be `undefined`.

### Exercise 2: Use an Index Signature

Create an `interface Dictionary { [word: string]: string }` and fill it with three English-to-Vietnamese word pairs. Write a `function translate(word: string, dict: Dictionary): string` that returns the translation or `"not found"` if the word is missing. Test it with `console.log(translate("hello", dict))`. Try adding a numeric property to the dictionary — confirm it is a compile error.

### Exercise 3: Extend an Interface

Create `interface Shape { area(): number }` and two sub-interfaces: `interface Rectangle extends Shape { width: number; height: number }` and `interface Circle extends Shape { radius: number }`. Implement both shapes with object literals and write a `function totalArea(shapes: Shape[]): number` that sums the areas. Pass an array of mixed rectangles and circles — TypeScript should accept it because both extend `Shape`.

---

## Common Mistakes

### Mistake 1: Using `type` when you should use `interface` for class-implementable shapes

```typescript
// WRONG — this works, but interface is clearer for a class contract
type Animal = {
  name: string;
  makeSound(): void;
}
class Dog implements Animal { ... }
```

```typescript
// RIGHT — interface is the conventional choice for class contracts
interface Animal {
  name: string;
  makeSound(): void;
}
class Dog implements Animal { ... }
```

Both `type` aliases and `interface` can be implemented by classes (as long as they describe an object shape), but `interface` is the conventional choice. The reason is partly historical (interfaces were the only option before TypeScript 1.5 added type aliases), partly practical (interfaces have better error messages for class implementations). The TypeScript style guide recommends using `interface` for any object shape that a class will implement, and reserving `type` for non-object shapes (unions, tuples, function signatures) and for type-level metaprogramming (Lesson 07's utility types).

### Mistake 2: Forgetting `?` for optional fields, then getting "missing property" errors

```typescript
// WRONG — every Person must have a middleName, even if you don't have one
interface Person {
  name: string;
  middleName: string;
}
const p: Person = { name: "Hieu" };
// COMPILE ERROR: Property 'middleName' is missing in type '{ name: string; }'
```

```typescript
// RIGHT — mark optional fields with ?
interface Person {
  name: string;
  middleName?: string;
}
const p: Person = { name: "Hieu" };   // OK
```

When a property might legitimately be absent from an object, you must mark it with `?` in the interface. Without the `?`, TypeScript treats every property as required — the object must have it. The error message ("Property 'X' is missing") confuses beginners because they think "but I do not have a middleName, so I just did not include it" — TypeScript's view is "the interface requires middleName, so you must provide it; if you do not always have one, mark it optional with `?`".

### Mistake 3: Expecting `readonly` to deeply freeze — it only checks the top level

```typescript
// WRONG — assuming readonly makes the whole object immutable
interface Person {
  readonly address: { city: string; zip: string };
}
const p: Person = { address: { city: "Seattle", zip: "98101" } };
p.address.city = "Portland";   // OK! readonly is shallow
```

```typescript
// RIGHT — use Readonly<T> for deep immutability (covered in Lesson 07)
interface Person {
  readonly address: Readonly<{ city: string; zip: string }>;
}
const p: Person = { address: { city: "Seattle", zip: "98101" } };
p.address.city = "Portland";   // COMPILE ERROR — city is readonly
```

`readonly` applies only to the top-level property. If a `readonly` property holds an object, the object's inner properties are still mutable. This is because `readonly` is a compile-time check on the property access itself — `p.address = ...` is forbidden (you are assigning to `address`), but `p.address.city = ...` is allowed (you are assigning to `city`, which is not marked readonly). For deep immutability, use `Readonly<T>` recursively or use `as const` to make an entire object deeply readonly.

### Mistake 4: Trying to implement a union type with `implements`

```typescript
// WRONG — you cannot implement a union type
type Animal = Dog | Cat;
class MyAnimal implements Animal { ... }
// COMPILE ERROR: A class can only implement an object type or intersection of
// object types with statically known members.
```

```typescript
// RIGHT — implement an interface, then union the classes if needed
interface Animal {
  name: string;
}
class Dog implements Animal { name = "Rex"; }
class Cat implements Animal { name = "Whiskers"; }
type Pet = Dog | Cat;
```

A class can implement an `interface` (an object shape), but it cannot implement a `type` alias that is a union. This is because implementing a union would mean the class is *either* of the union's members — but the class is one specific thing. The fix is always the same: have the class implement an interface that represents the *common* shape of the union members, then union the classes (or instances) separately if you need a discriminated union. Lesson 06 covers discriminated unions in depth.

---

## Summary

- Inline object types (`{ name: string; age: number }`) work for one-off cases; `interface` and `type` aliases give them reusable names.
- `interface` is the classic way to describe object shapes — it supports `extends`, declaration merging, and class `implements`.
- `type` aliases can describe anything — primitives, unions, tuples, function signatures — and use `&` for intersection instead of `extends`.
- Prefer `interface` for object shapes (better error messages, extensible, class-implementable); prefer `type` for unions, tuples, and complex compositions.
- TypeScript uses **structural typing** — two types are compatible if their shapes match, regardless of their names (different from Java/C#'s nominal typing).
- Optional properties use `?` (`middleName?: string`); readonly properties use `readonly` (`readonly id: number`).
- `readonly` is shallow — it only applies to the top-level property, not nested objects.
- Index signatures (`[key: string]: string`) describe objects with arbitrary keys; every named property must be compatible with the index signature's value type.

You now know how to describe object shapes with both `interface` and `type` aliases. In Lesson 05 we will move on to functions — function types, function declarations, arrow functions, optional/default/rest parameters, function overloads, and finally generics, the feature that lets you write reusable type-safe code.

---

**Next:** [Lesson 05: Functions and Generics →](./05-functions-and-generics.md)
