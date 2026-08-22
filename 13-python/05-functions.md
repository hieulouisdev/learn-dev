# Lesson 05: Functions

> Module: Python · Lesson 5 of 7
> Estimated time: 30–45 minutes

So far, every script you have written runs top to bottom, line by line. That works for tiny programs, but real software is built from **functions** — named, reusable blocks of code that take inputs, do work, and return outputs. Functions are how you avoid repeating yourself, how you organize a 500-line program into 20 small pieces, and how you make your code testable and shareable.

In this lesson you will define your own functions with `def`, give them parameters and default values, return results, mix positional and keyword arguments, and write docstrings so other people (and future you) understand what your function does. Once you understand functions, you stop writing scripts and start writing **programs**.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define a function with `def`, parameters, and a return value.
2. Call functions with positional arguments and keyword arguments.
3. Use default parameter values correctly.
4. Write docstrings and access them with `help()`.

---

## 1. Defining a Function

A function is created with the `def` keyword, followed by the function name, parentheses containing any parameters, a colon, and an indented block of code. Here is the simplest possible function — one that takes no parameters and does nothing useful:

```python
def greet():
    print("Hello, World!")
```

Let's dissect this. `def` is a Python keyword that tells the interpreter "I am defining a function." `greet` is the function's name — follow the same `snake_case` convention as variables. The empty parentheses `()` mean this function takes no parameters. The colon `:` introduces the body block. The next line, indented 4 spaces, is the function's body — the code that runs when the function is called.

**Defining a function does not run it.** Nothing happens when Python reads the `def` line. The function only runs when you **call** it by writing its name followed by parentheses:

```python
def greet():
    print("Hello, World!")

greet()      # ← this is the call. It prints "Hello, World!"
greet()      # call it again — prints again
greet()      # and again
```

You can call a function as many times as you like. Each call runs the body from scratch. This is the whole point of functions: write once, run many times. If you write `greet` without parentheses — just `greet` — Python evaluates it to the function object itself but does not call it. This is a common beginner mistake: you write the function name, see no output, and wonder what went wrong. Add the parentheses.

---

## 2. Parameters and Arguments

Functions become useful when they take inputs — called **parameters** when you define them, and **arguments** when you pass them in a call. (People use these words interchangeably; the difference is subtle and not worth worrying about.)

```python
def greet(name):
    print(f"Hello, {name}!")

greet("Hieu")      # Hello, Hieu!
greet("Alice")     # Hello, Alice!
greet("Bob")       # Hello, Bob!
```

Line by line: `def greet(name):` defines a function called `greet` that takes one parameter, which we happen to call `name`. Inside the body, `name` is a local variable holding whatever was passed in. When we call `greet("Hieu")`, the string `"Hieu"` is assigned to the parameter `name` and the body runs. Each call gets its own `name` — calling `greet("Alice")` afterwards does not remember "Hieu."

You can have multiple parameters, separated by commas:

```python
def describe_person(name, age, city):
    print(f"{name} is {age} years old and lives in {city}.")

describe_person("Hieu", 25, "Hanoi")
# Hieu is 25 years old and lives in Hanoi.
```

The arguments are matched to the parameters **by position** — first argument to first parameter, second to second, and so on. If you pass the wrong number of arguments, Python raises `TypeError: describe_person() missing 1 required positional argument`. Always match the count.

---

## 3. Default Parameter Values

Sometimes a parameter has a sensible default. You can specify it in the function definition with `=`:

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Hieu")              # Hello, Hieu!
greet("Alice", "Hi")       # Hi, Alice!
greet("Bob", greeting="Hey")  # Hey, Bob!
```

The function `greet` has two parameters: `name` (required, no default) and `greeting` (optional, defaults to `"Hello"`). When you call `greet("Hieu")`, Python uses the default `"Hello"`. When you call `greet("Alice", "Hi")`, the second argument overrides the default. Parameters with defaults must come **after** parameters without defaults — `def greet(greeting="Hello", name):` is a `SyntaxError`. The rule makes sense: Python matches arguments by position, so it cannot skip an early parameter to fill a later one.

---

## 4. Return Values

A function can send a value back to the caller with the `return` keyword. This is different from `print` — `print` writes to the screen, `return` hands a value back to the code that called the function. You can then store that value in a variable, pass it to another function, or do anything else with it.

```python
def add(a, b):
    return a + b

result = add(3, 4)
print(result)              # 7
print(add(10, 20))         # 30 — the return value goes straight into print
```

The line `return a + b` evaluates the expression `a + b` and immediately exits the function, handing the result back to the caller. Any code in the function **after** `return` does not run — `return` is a "stop and go back" instruction.

### Functions without an explicit `return` return `None`

If a function has no `return` statement, or if `return` has no value after it, Python automatically returns `None`. This catches many beginners off guard:

```python
def greet(name):
    print(f"Hello, {name}!")
    # no return statement

result = greet("Hieu")     # prints "Hello, Hieu!"
print(result)              # None
print(type(result))        # <class 'NoneType'>
```

The function did its job (printed a greeting) but returned `None` because no `return` was specified. If you ever find that `result` is unexpectedly `None`, the most likely cause is a function that uses `print` where you expected a `return`. The fix: add `return` and have the function give back the value, then `print` it from outside.

### Multiple return values

Python has a clean way to return multiple values — just separate them with commas. Python automatically bundles them into a **tuple** (Lesson 06):

```python
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([3, 1, 4, 1, 5, 9])
print(low, high)           # 1 9
```

This is called **unpacking** — the function returns a tuple of two values, and Python assigns them to `low` and `high` in order. This is one of Python's most-loved features; it makes functions that compute several related values feel natural to use.

---

## 5. Positional vs Keyword Arguments

You have already seen **positional arguments** — arguments matched to parameters by their position in the call. Python also supports **keyword arguments**, where you write `parameter=value` in the call. This is incredibly useful for functions with many parameters or many defaults.

```python
def describe_person(name, age, city, occupation):
    print(f"{name}, {age}, from {city}, works as a {occupation}.")

# Positional only — order matters
describe_person("Hieu", 25, "Hanoi", "engineer")

# Keyword arguments — order does not matter
describe_person(name="Hieu", occupation="engineer", city="Hanoi", age=25)

# Mixing positional and keyword — positional first
describe_person("Hieu", 25, city="Hanoi", occupation="engineer")
```

Three rules: (1) keyword arguments can appear in any order, (2) positional arguments must come before any keyword arguments in the call, and (3) you cannot pass the same argument twice (once positional, once keyword) — Python raises `TypeError`. The convention in real code is: use positional for the first one or two obvious arguments (like `name`), and use keyword for everything else, especially when a function has many optional parameters. This makes the call self-documenting — `describe_person("Hieu", 25, city="Hanoi", occupation="engineer")` reads better than `describe_person("Hieu", 25, "Hanoi", "engineer")` because the reader does not have to look up what the third and fourth parameters mean.

---

## 6. Docstrings

A **docstring** is a triple-quoted string immediately after the `def` line that documents what the function does. It is ignored by Python at runtime, but it is read by tools like `help()`, IDEs (which show it as a tooltip), and documentation generators like Sphinx.

```python
def add(a, b):
    """Return the sum of a and b."""
    return a + b

help(add)
```

Running `help(add)` displays:

```text
Help on function add in module __main__:

add(a, b)
    Return the sum of a and b.
```

For longer functions, use the multi-line form:

```python
def calculate_bmi(weight_kg, height_m):
    """
    Calculate Body Mass Index.

    Parameters
    ----------
    weight_kg : float
        Weight in kilograms.
    height_m : float
        Height in meters.

    Returns
    -------
    float
        The BMI value, rounded to one decimal place.
    """
    return round(weight_kg / (height_m ** 2), 1)

print(calculate_bmi(70, 1.75))     # 22.9
```

The convention (Google style, NumPy style, or Sphinx style — all slightly different) is to describe the parameters, their types, and the return value. You do not need to memorize a style now — the habit of *writing a docstring at all* is what matters. A one-line docstring is better than no docstring. Once your team adopts a style, your IDE will help you format docstrings consistently. **Every function you write should have at least a one-line docstring** — future you will thank past you.

---

## Try It Yourself

### Exercise 1: Temperature Converter

Write a function `celsius_to_fahrenheit(celsius)` that takes a Celsius temperature (a float) and returns the Fahrenheit equivalent using the formula `F = C * 9/5 + 32`. Add a one-line docstring. Call the function with `0`, `100`, and `37` and print the results. Expected output: `32.0`, `212.0`, `98.6`.

### Exercise 2: Greeting with Defaults

Write a function `greet(name, greeting="Hello", punctuation="!")` that prints `f"{greeting}, {name}{punctuation}"`. Call it four different ways: with just a name, with a name and a greeting, with a name and a punctuation (using keyword argument), and overriding all three parameters. Confirm that the defaults work when arguments are omitted.

### Exercise 3: Multiple Return Values

Write a function `divide_with_remainder(a, b)` that takes two integers and returns a tuple of two values: the quotient `a // b` and the remainder `a % b`. Add a docstring. Call it with `divide_with_remainder(17, 5)` and unpack the result into two variables, then print both. Expected output: `3 2`. Try another call with `divide_with_remainder(100, 7)`.

---

## Common Mistakes

### Mistake 1: Forgetting `return`, so the function returns `None`

```python
# WRONG — uses print instead of return
def double(x):
    print(x * 2)

result = double(5)
print(result)              # None
```

```python
# RIGHT — uses return
def double(x):
    return x * 2

result = double(5)
print(result)              # 10
```

If your function is supposed to give a value back to the caller, use `return`. `print` only writes to the screen — it does not return anything, so the function silently returns `None`. This is the most common function-related bug for beginners. The rule of thumb: if the function's job is to *compute* something, use `return`; if its job is to *display* something, `print` is fine.

### Mistake 2: Using a mutable default argument

```python
# WRONG — default list is shared across calls
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item("apple"))   # ['apple']
print(add_item("banana"))  # ['apple', 'banana'] — surprise!
```

```python
# RIGHT — use None as the default, create a new list inside
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item("apple"))   # ['apple']
print(add_item("banana"))  # ['banana']
```

Default argument values are evaluated **once** when the function is defined, not each time the function is called. So `items=[]` creates one empty list and reuses it for every call — the list grows across calls, which is almost never what you want. The fix is the idiom shown on the right: use `None` as the default, then create a new list inside the function. This is one of Python's most notorious gotchas; every experienced Python developer has hit it at least once.

### Mistake 3: Calling a function before defining it

```python
# WRONG — say_hi() is called before it is defined
say_hi()

def say_hi():
    print("Hi!")
# NameError: name 'say_hi' is not defined
```

```python
# RIGHT — define first, then call
def say_hi():
    print("Hi!")

say_hi()
```

Python executes a script **top to bottom**. A function only exists after the `def` line runs. If you call a function before its definition, Python has not seen the name yet and raises `NameError`. The convention is to put all function definitions at the top of the file (or in a separate module you import) and the actual "main" code at the bottom. A common pattern is `if __name__ == "__main__":` at the bottom — Lesson 07's cheatsheet shows this.

### Mistake 4: Forgetting the colon `:` after `def`

```python
# WRONG — missing colon
def greet(name)
    print(f"Hello, {name}!")
# SyntaxError: expected ':'
```

```python
# RIGHT — colon at end of def line
def greet(name):
    print(f"Hello, {name}!")
```

Every statement that introduces a block (`def`, `if`, `for`, `while`, `class`, etc.) must end with a colon. If you forget it, Python raises `SyntaxError: expected ':'` and points to the offending line. Good editors highlight this immediately. Make the colon a reflex whenever you type `def` — like putting a period at the end of a sentence.

---

## Summary

- Functions are defined with `def name(params):` and called with `name(args)`.
- Parameters are the names in the definition; arguments are the values you pass in.
- Default parameter values use `=`, and must come after parameters without defaults.
- `return` sends a value back; without `return`, the function returns `None`.
- Multiple values can be returned as a tuple and unpacked by the caller.
- Keyword arguments (`name=value`) make calls self-documenting and order-independent.
- Docstrings (`"""..."""`) document functions and are read by `help()` and IDEs.
- Beware of mutable default arguments — use `None` and create the collection inside.

You can now package reusable logic into functions, which means your programs can grow without becoming spaghetti. In Lesson 06, you will meet the four core collection types — lists, tuples, dicts, and sets — which together with functions are enough to build almost any program.

---

**Next:** [Lesson 06: Lists, Dicts, Tuples, and Sets →](./06-lists-dicts-tuples-sets.md)
