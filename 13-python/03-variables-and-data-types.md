# Lesson 03: Variables and Data Types

> Module: Python · Lesson 3 of 7
> Estimated time: 30–45 minutes

Every program you will ever write manipulates data: numbers, text, true/false values, and the special "nothing" value called `None`. In this lesson, we will learn how to store data in **variables**, how Python understands the **type** of each value, how to convert between types, and how to build readable strings with **f-strings** — Python's most beloved feature.

By the end of this lesson, you will be able to read a small Python program and explain every variable, predict its type, and produce a nicely formatted output. These skills are the foundation of everything else in the module — control flow, functions, and collections all assume you understand variables and types.

---

## Learning Objectives

After this lesson, you will be able to:

1. Assign values to variables, reassign them, and follow Python's naming rules and conventions.
2. Identify the five core data types: `int`, `float`, `str`, `bool`, and `NoneType`.
3. Use the `type()` function to inspect a value's type at runtime.
4. Convert between types with `int()`, `float()`, `str()`, and `bool()`, and apply Python's truthiness rules.
5. Format strings with f-strings, including expressions and number formatting.

---

## 1. Variables

A variable is a **named box** that holds a value. In Python, you create a variable simply by assigning a value to a name with the `=` operator. There is no `let`, `var`, `const`, or type declaration — Python figures out the type from the value.

```python
name = "Hieu"
age = 25
height_m = 1.75
is_student = True
```

Each line says: "create a variable with this name, and store this value in it." Python automatically infers that `name` is a string, `age` is an integer, `height_m` is a float, and `is_student` is a boolean. You can reassign a variable to a different value, even of a different type — Python handles it without complaint.

```python
x = 10          # x is an int
print(type(x))  # <class 'int'>
x = "ten"       # now x is a string
print(type(x))  # <class 'str'>
```

This flexibility is what "dynamically typed" means. It is convenient, but it means you must be careful — if a function expects `x` to be an integer and you reassign it to a string somewhere else, you will get a runtime `TypeError`. Large Python codebases use **type hints** (like `x: int = 10`) to catch these mistakes early, but type hints are optional and we will not cover them in this module.

### Naming rules and conventions

Python variable names must follow a few rules. A name can contain **letters, digits, and underscores**, but it **cannot start with a digit**. Names are **case-sensitive** (`age` and `Age` are different variables). Names cannot be Python keywords (`if`, `for`, `def`, `class`, `import`, etc.). By convention, Python uses **snake_case** — all lowercase, words separated by underscores, like `first_name` or `total_price`. This is the style you will see in every well-written Python library, so adopt it from day one.

```python
# Valid names
user_name = "alice"
_total = 100
PI = 3.14159          # UPPER_CASE is convention for constants
camelCase = "allowed but not Pythonic"

# Invalid names
# 2nd_place = "bob"   # ← can't start with a digit
# my-name = "carol"   # ← hyphens not allowed
# for = 5             # ← 'for' is a keyword
```

---

## 2. Core Data Types

Python has many data types, but as a beginner you need to master five. Everything else is built on these.

### `int` — integers

```python
age = 25
population = 8_000_000_000      # underscores ignored — for readability
negative = -42
```

An `int` is a whole number with no decimal point. Python 3 integers have **unlimited precision** — you can compute `2 ** 1000` (2 to the power of 1000) and get a 302-digit number with no overflow error, no special "BigInteger" class, no fuss. The underscores in `8_000_000_000` are a Python 3.6+ feature purely for human readability — Python ignores them. Use them for large numbers in financial or scientific code.

### `float` — floating-point numbers

```python
pi = 3.14159
temperature = -12.5
scientific = 6.022e23          # Avogadro's number
```

A `float` is a number with a decimal point. Internally, Python uses IEEE 754 double-precision (64-bit), the same as JavaScript and Java. This means floats have limited precision: `0.1 + 0.2` equals `0.30000000000000004`, not `0.3`. This is not a Python bug — it is how all floating-point hardware works. For money or other exact decimal values, use the `decimal` module from the standard library instead of `float`.

### `str` — strings

```python
greeting = "Hello, World!"
name = 'Hieu'
multi_line = """This is
a multi-line string."""
```

A `str` (string) is text. Strings in Python 3 are **Unicode** — they can hold characters from any language, emoji, and symbols without any special configuration. Strings are **immutable**: once created, a string cannot be changed. You can build a new string from an old one, but the original stays as it was. Single quotes `'...'` and double quotes `"..."` are equivalent in Python — pick one and be consistent. Multi-line strings use triple quotes `"""..."""` or `'''...'''`.

### `bool` — booleans

```python
is_active = True
is_admin = False
```

A `bool` is a `True` or `False` value. **The capital letters matter** — `True` and `False` are Python keywords, and `true`/`false` (lowercase, like JavaScript) will raise `NameError`. Booleans are technically a subclass of `int`: `True` is `1` and `False` is `0` under the hood. This means `True + True == 2` works, but you should never rely on it in real code — be explicit.

### `NoneType` — the absence of a value

```python
result = None
print(result is None)      # True
```

`None` is Python's "nothing here" value, similar to `null` in JavaScript or `nil` in Ruby. It is used as a default for functions that have nothing to return, as a placeholder for "no value yet," and as a sentinel in many library APIs. Always compare to `None` with `is None` or `is not None` — never `== None`. We will explain why in the next lesson.

---

## 3. The `type()` Function

Python has a built-in function called `type()` that tells you what type a value is. This is invaluable when debugging — if a variable is not behaving the way you expect, the first question to ask is "what type is it?"

```python
print(type(42))            # <class 'int'>
print(type(3.14))          # <class 'float'>
print(type("hello"))       # <class 'str'>
print(type(True))          # <class 'bool'>
print(type(None))          # <class 'NoneType'>
```

The output `<class 'int'>` looks intimidating, but it just means "this value is an instance of the `int` class." For now, you can read it as "type: int." Note that `True`'s type is `bool`, not `int` — even though `bool` is technically a subclass of `int`, `type()` reports the most specific type.

You can also use `isinstance()` to check whether a value is of a particular type, which is the safer way to do type checks in real code:

```python
x = 5
print(isinstance(x, int))      # True
print(isinstance(x, str))      # False
print(isinstance(x, (int, float)))  # True — accepts a tuple of types
```

---

## 4. Type Conversion

Often you have data in one type and need it in another. Python provides built-in functions to convert (also called **cast**) between types.

```python
# String to int
count_str = "42"
count = int(count_str)
print(count + 8)            # 50

# String to float
price_str = "19.99"
price = float(price_str)
print(price * 2)            # 39.98

# Number to string
age = 25
message = "I am " + str(age) + " years old."
print(message)              # I am 25 years old.

# Number to bool
print(bool(0))              # False
print(bool(42))             # True
print(bool(""))             # False — empty string
print(bool("hello"))        # True — non-empty string
print(bool(None))           # False
print(bool([]))             # False — empty list
print(bool([1, 2]))         # True — non-empty list
```

### Truthiness rules

When you pass a value to `bool()`, Python applies rules called **truthiness**. The following values are considered "falsy" (they convert to `False`):

- The number `0` and `0.0`
- The empty string `""`
- `None`
- Empty collections: `[]`, `{}`, `()`, `set()` (we will meet these in Lesson 06)

**Everything else is truthy** (converts to `True`). This is incredibly useful in `if` statements: instead of writing `if len(my_list) > 0:`, you can write `if my_list:` — both mean "if the list is non-empty." Once you internalize the truthiness rules, your code becomes much more idiomatic.

### Invalid conversions

Not every conversion is allowed. `int("hello")` raises `ValueError: invalid literal for int() with base 10: 'hello'` because Python cannot turn "hello" into a number. `int("3.14")` also raises `ValueError` — to parse a string that looks like a float, first convert to float, then to int: `int(float("3.14"))` gives `3`. Always wrap user input in a `try`/`except` block (Lesson 04 territory) if you cannot guarantee the input is a valid number.

---

## 5. f-strings

Python 3.6 introduced **f-strings** — a way to embed expressions inside string literals using curly braces `{}`. They are the cleanest, fastest, most readable way to build strings, and they are what every modern Python developer uses. To write an f-string, put the letter `f` (or `F`) right before the opening quote.

```python
name = "Hieu"
age = 25
print(f"Hello, {name}! You are {age} years old.")
# Output: Hello, Hieu! You are 25 years old.
```

The `f` prefix tells Python: "when you see `{...}` inside this string, evaluate the expression inside and substitute the result." You can put any Python expression inside the braces, not just variable names — including arithmetic, function calls, and even dictionary lookups.

```python
print(f"2 + 2 = {2 + 2}")              # 2 + 2 = 4
print(f"Name upper: {name.upper()}")   # Name upper: HIEU
print(f"Length of name: {len(name)}")  # Length of name: 4
```

### Number formatting

f-strings support a **format specifier** after a colon `:` inside the braces. The most common one is `.2f` — "format as a float with 2 decimal places." This is essential for money, percentages, and any value where you want fixed precision.

```python
price = 19.99
tax_rate = 0.08
total = price * (1 + tax_rate)
print(f"Total: ${total:.2f}")          # Total: $21.59
```

Without `.2f`, you might see `Total: $21.5892` — ugly. With `.2f`, you get `21.59`. Other useful format specifiers: `,.2f` adds thousands separators (`1,234,567.89`), `:>10` right-aligns in a 10-character width, `:.1%` formats a number as a percentage (`0.857` → `85.7%`), and `:e` formats in scientific notation.

```python
big_number = 1234567.891
ratio = 0.857
print(f"Big: {big_number:,.2f}")       # Big: 1,234,567.89
print(f"Ratio: {ratio:.1%}")          # Ratio: 85.7%
```

Once you start using f-strings, you will never go back to the old `%` formatting or `.format()` method. They are one of the most loved features in modern Python, and you will use them in every script you write.

---

## Try It Yourself

### Exercise 1: Personal Profile

Create a script called `profile.py`. Define variables for your `name` (string), `age` (int), `height_m` (float), and `is_student` (bool). Print a single sentence using an f-string that includes all four values. Example output: `Hieu is 25 years old, 1.75m tall, and it is True that they are a student.` Run the script and confirm the output.

### Exercise 2: Type Inspection

In the same script, add four `print(type(...))` calls — one for each of your variables. Run it and confirm the types match what you expect (`<class 'str'>`, `<class 'int'>`, `<class 'float'>`, `<class 'bool'>`). Then try reassigning `age` to the string `"twenty-five"` and run again. Notice how Python lets you change a variable's type without complaint — this is dynamic typing in action.

### Exercise 3: Receipt Calculator

Create a script called `receipt.py`. Define three variables: `item = "Coffee"`, `price = 4.50`, `quantity = 3`. Compute `subtotal = price * quantity`, `tax = subtotal * 0.08`, and `total = subtotal + tax`. Print a nicely formatted receipt using f-strings with `.2f` for all money values. The output should look like:

```text
Receipt:
  3 x Coffee @ $4.50 = $13.50
  Tax (8%): $1.08
  Total: $14.58
```

---

## Common Mistakes

### Mistake 1: Using `true` instead of `True`

```python
# WRONG — lowercase true (JavaScript style)
is_admin = true
```

```python
# RIGHT — capitalized True
is_admin = True
```

Python's boolean literals are `True` and `False` (capitalized). If you type lowercase `true` or `false`, Python raises `NameError: name 'true' is not defined`. This is the single most common mistake for developers coming from JavaScript, Java, or C++. Memorize: capital T, capital F, no exceptions.

### Mistake 2: Forgetting case sensitivity

```python
# WRONG — Name and name are different variables
Name = "Hieu"
print(name)              # NameError: name 'name' is not defined
```

```python
# RIGHT — same case everywhere
name = "Hieu"
print(name)              # Hieu
```

Python is **strictly case-sensitive**. `Age`, `age`, and `AGE` are three different variables with no relationship. Convention: use lowercase `snake_case` for normal variables, and `UPPER_CASE` for constants (values that never change, like `MAX_RETRIES = 5`). Pick a style and stick to it — inconsistent casing is one of the most common sources of bugs in beginner code.

### Mistake 3: Using a variable before assigning it

```python
# WRONG — total is used before it is defined
print(total)
total = 100
```

```python
# RIGHT — define first, then use
total = 100
print(total)             # 100
```

Python executes statements **top to bottom**, in order. You cannot use a variable before it has been assigned — doing so raises `NameError: name 'total' is not defined`. This is different from JavaScript, where `var` declarations are "hoisted" to the top of the function. In Python, what you see is what you get: a variable exists only after the assignment statement has run.

### Mistake 4: Mixing `+` with numbers and strings

```python
# WRONG — cannot concatenate int to str
age = 25
message = "I am " + age + " years old."
# TypeError: can only concatenate str (not "int") to str
```

```python
# RIGHT — convert with str() or use an f-string
message1 = "I am " + str(age) + " years old."
message2 = f"I am {age} years old."
```

Python does not implicitly convert numbers to strings when you use `+`. `"I am " + 25` raises `TypeError`. You must either explicitly convert with `str()` or — almost always the better choice — use an f-string. The `+` operator is overloaded for strings (string concatenation) and for numbers (addition), and Python refuses to guess which one you meant when the types disagree.

---

## Summary

- Variables are created with `name = value`; no declaration, no type annotation needed.
- Python uses `snake_case` for variable names; names cannot start with a digit and cannot be keywords.
- The five core types are `int`, `float`, `str`, `bool`, and `NoneType`.
- Python 3 `int` has unlimited precision; `float` is IEEE 754 double-precision.
- Strings are immutable and Unicode; booleans are `True`/`False` (capitalized).
- Use `type(x)` to inspect a value's type at runtime.
- Convert types with `int()`, `float()`, `str()`, `bool()`; learn the truthiness rules.
- f-strings (`f"Hello, {name}!"`) are the modern way to embed values and format numbers.

You now know how to store and shape data in Python. In the next lesson, we will make our programs *make decisions* with `if`/`elif`/`else` and *repeat work* with `for` and `while` loops — control flow is where programming starts to feel powerful.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
