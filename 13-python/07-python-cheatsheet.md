# Lesson 07: Python Cheatsheet

> Module: Python · Lesson 7 of 7
> Estimated time: 20–30 minutes

This is the capstone of the Python module — a single-page reference you can keep open while you code. It collects every syntax pattern, every built-in, and every common pitfall from Lessons 01 through 06 in one place. Bookmark it, print it, pin it next to your monitor. Six months from now, when you cannot quite remember whether to use `is` or `==` with `None`, this is the page you will reach for.

Cheatsheets are not a substitute for understanding — they are a **recall aid**. Each snippet here was explained in depth in earlier lessons; if a line confuses you, go back and re-read the relevant lesson. Once the patterns become muscle memory, you will rarely need this page — but it is always here when you do.

---

## Learning Objectives

After this lesson, you will be able to:

1. Look up any Python syntax pattern from earlier lessons in seconds.
2. Recognize the most common Python pitfalls before they bite you.
3. Identify the next step in your programming journey after this module.

---

## 1. Hello, World!

The minimal Python program — the template every script starts from.

```python
def main():
    print("Hello, World!")

if __name__ == "__main__":
    main()
```

Line by line: `def main():` defines a function called `main` that prints the greeting. The line `if __name__ == "__main__":` is a Python idiom — it means "if this file is being run directly as a script, run the `main()` function." It is not strictly required for tiny scripts, but it is good practice because it lets you import the file as a module without the print running on import. Run the script from the terminal with `python hello.py` (or `python3 hello.py` on Mac/Linux).

---

## 2. Variables and Types

| Type | Example | Notes |
|------|---------|-------|
| `int`     | `42`, `-7`, `1_000_000` | Unlimited precision |
| `float`   | `3.14`, `-0.5`, `6.022e23` | IEEE 754 double |
| `str`     | `"hi"`, `'hi'`, `"""multi"""` | Immutable, Unicode |
| `bool`    | `True`, `False` | Capital first letter |
| `NoneType`| `None` | The "no value" value |

```python
x = 10                       # int
y = 3.14                     # float
name = "Hieu"                # str
is_active = True             # bool
result = None                # NoneType

print(type(x))               # <class 'int'>
print(isinstance(x, int))    # True
```

Inspect a value's type with `type(x)`. Use `isinstance(x, int)` for type checks (it returns `True` for subclasses too). Type conversion: `int("42")`, `float("3.14")`, `str(42)`, `bool(0)`.

---

## 3. Strings

Strings are immutable sequences of Unicode characters. The most important feature for beginners is the **f-string** — Python's modern way to embed values.

```python
name = "Hieu"
age = 25

# f-string (Python 3.6+)
greeting = f"Hello, {name}! You are {age} years old."

# Expressions inside braces
print(f"2 + 2 = {2 + 2}")
print(f"Upper: {name.upper()}")
print(f"Length: {len(name)}")

# Number formatting
price = 19.99
tax = 0.08
total = price * (1 + tax)
print(f"Total: ${total:.2f}")           # Total: $21.59
print(f"Big: {1234567.89:,.2f}")        # Big: 1,234,567.89
print(f"Ratio: {0.857:.1%}")            # Ratio: 85.7%
```

### Common string methods

```python
s = "  Hello, World!  "

print(s.strip())              # "Hello, World!" — remove leading/trailing whitespace
print(s.lower())              # "  hello, world!  "
print(s.upper())              # "  HELLO, WORLD!  "
print(s.replace("World", "Python"))   # "  Hello, Python!  "
print(s.split(","))           # ['  Hello', ' World!  ']
print("a,b,c".split(","))     # ['a', 'b', 'c']
print(",".join(["a", "b", "c"]))  # a,b,c
print("hello".startswith("he"))    # True
print("hello".endswith("lo"))     # True
print("ell" in "hello")           # True
```

Note that none of these methods modify `s` — they all return a **new** string, because strings are immutable. To "change" a string, you reassign: `s = s.strip()`.

---

## 4. Numbers

Python has two numeric types every beginner needs: `int` (whole numbers, unlimited precision) and `float` (decimal numbers, IEEE 754). The arithmetic operators:

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `+`  | addition           | `5 + 3` | `8` |
| `-`  | subtraction        | `5 - 3` | `2` |
| `*`  | multiplication     | `5 * 3` | `15` |
| `/`  | true division      | `7 / 2` | `3.5` |
| `//` | floor division     | `7 // 2` | `3` |
| `%`  | modulo (remainder) | `7 % 2` | `1` |
| `**` | exponent           | `2 ** 10` | `1024` |

```python
print(10 / 3)               # 3.3333333333333335 — always float
print(10 // 3)              # 3 — integer floor division
print(10 % 3)               # 1 — remainder
print(2 ** 8)               # 256 — 2 to the 8th power
print(7 / 2)                # 3.5 — true division (float)
print(7 // 2)               # 3 — floor division (int)

# Augmented assignment
x = 10
x += 5                      # x = x + 5 → 15
x -= 3                      # 12
x *= 2                      # 24
x //= 5                     # 4
```

`/` always returns a `float`, even when the result is a whole number (`6 / 2` is `3.0`, not `3`). Use `//` when you want an integer result. The `**` operator is exponentiation — `2 ** 10` is 1024. Python has no built-in `^` for exponents; `^` is the bitwise XOR operator (an entirely different thing), which is a common surprise for users coming from other languages.

---

## 5. Control Flow

```python
# if / elif / else
score = 85
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
print(grade)                # B

# Comparison operators: == != < > <= >=
# Logical operators: and, or, not (NOT && || !)
# Identity: is, is not (use is None, not == None)
# Chained comparisons
age = 25
if 18 <= age <= 65:
    print("Working age")

# for loop with range
for i in range(5):          # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 8):       # 2, 3, 4, 5, 6, 7
    print(i)

for i in range(0, 10, 2):   # 0, 2, 4, 6, 8
    print(i)

# for loop over a collection
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# enumerate for index + value
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# while loop
count = 0
while count < 3:
    print(count)
    count += 1

# break — exit loop early
for n in range(1, 10):
    if n == 5:
        break
    print(n)                 # 1, 2, 3, 4

# continue — skip current iteration
for n in range(1, 6):
    if n % 2 == 0:
        continue
    print(n)                 # 1, 3, 5

# while True with break — interactive prompt pattern
while True:
    cmd = input("> ")
    if cmd == "quit":
        break
    print(f"You typed: {cmd}")
```

Remember the half-open interval rule: `range(5)` gives 5 numbers starting at 0 (`0,1,2,3,4`), not ending at 5. `range(1, 6)` gives `1,2,3,4,5`. The end is always **exclusive**.

---

## 6. Functions

```python
# Basic function
def greet(name):
    """Print a greeting for the given name."""
    print(f"Hello, {name}!")

greet("Hieu")                # Hello, Hieu!

# Default parameter values
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Hieu")                # Hello, Hieu!  (uses default)
greet("Alice", "Hi")         # Hi, Alice!    (overrides default)

# Return value
def add(a, b):
    return a + b

result = add(3, 4)
print(result)                # 7

# Multiple return values (returns a tuple)
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([3, 1, 4, 1, 5, 9, 2, 6])
print(low, high)             # 1 9

# Keyword arguments — order independent
def describe(name, age, city):
    print(f"{name}, {age}, {city}")

describe(name="Hieu", city="Hanoi", age=25)
describe("Hieu", 25, city="Hanoi")    # mix positional + keyword

# *args — variable positional arguments
def sum_all(*numbers):
    return sum(numbers)

print(sum_all(1, 2, 3, 4))      # 10

# **kwargs — variable keyword arguments
def make_profile(**info):
    return info

print(make_profile(name="Hieu", age=25, city="Hanoi"))
# {'name': 'Hieu', 'age': 25, 'city': 'Hanoi'}

# Docstring access
help(greet)
print(greet.__doc__)          # Print a greeting for the given name.
```

`*args` packs extra positional arguments into a tuple; `**kwargs` packs extra keyword arguments into a dict. They are advanced features, but they appear constantly in real-world Python libraries — when you see `*args, **kwargs` in a function signature, you now know what it means. A function with no `return` statement returns `None` automatically.

---

## 7. Collections

| Type | Syntax | Mutable? | Ordered? | Duplicates? | Use For |
|------|--------|-----------|-----------|--------------|---------|
| List  | `[1, 2, 3]`        | Yes | Yes | Yes | Ordered, changeable sequence |
| Tuple | `(1, 2, 3)`        | No  | Yes | Yes | Fixed record, dict key, return values |
| Dict  | `{"a": 1, "b": 2}` | Yes | Yes (3.7+) | Keys unique | Keyed lookup |
| Set   | `{1, 2, 3}`        | Yes | No  | No  | Uniqueness, fast membership |

```python
# Lists
fruits = ["apple", "banana", "cherry"]
print(fruits[0])             # apple (0-based)
print(fruits[-1])            # cherry (last)
print(fruits[1:3])           # ['banana', 'cherry'] (slice, end exclusive)
print(len(fruits))           # 3

fruits.append("date")        # add to end
fruits.insert(0, "apricot")  # insert at index
last = fruits.pop()          # remove and return last
fruits.remove("banana")      # remove first match by value
fruits.sort()                # sort in place (returns None)
new_list = sorted(fruits)    # return new sorted list

# Tuples
point = (3, 4)
x, y = point                 # unpack
print(point[0])             # 3

# Dicts
person = {"name": "Hieu", "age": 25}
print(person["name"])        # Hieu
print(person.get("email"))           # None — no error
print(person.get("email", "N/A"))    # N/A — default
person["email"] = "x@y.com"         # add new key
person["age"] = 26                  # update existing key
for key in person.keys():   print(key)
for value in person.values(): print(value)
for key, value in person.items():
    print(f"{key}: {value}")

# Sets
unique = {1, 2, 3, 2, 1}
print(unique)               # {1, 2, 3}
print(2 in unique)          # True — fast O(1)
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
print(a | b)                # union      {1, 2, 3, 4, 5, 6}
print(a & b)                # intersect  {3, 4}
print(a - b)                # difference {1, 2}
print(a ^ b)                # sym diff   {1, 2, 5, 6}

# List comprehensions
squares = [x * x for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
doubled_odds = [x * 2 for x in range(10) if x % 2 == 1]

# Dict comprehension
squares_dict = {x: x * x for x in range(5)}

# Set comprehension
unique_lens = {len(w) for w in ["hi", "hello", "hey"]}
```

---

## 8. Common Idioms

These are the patterns you will see in every Python codebase. Memorize them — they are the difference between "code that happens to work" and "code that looks Pythonic."

```python
# The main guard — makes a script importable
if __name__ == "__main__":
    main()

# Enumerate — index + value in one call
for i, fruit in enumerate(fruits):
    print(i, fruit)

# Zip — iterate two lists in parallel
names = ["Hieu", "Alice", "Bob"]
ages = [25, 30, 28]
for name, age in zip(names, ages):
    print(f"{name} is {age}")

# Conditional assignment (ternary)
status = "adult" if age >= 18 else "minor"

# Default value via or
username = input_name or "guest"

# Multiple assignment
x, y = 10, 20
x, y = y, x                  # swap!

# Range with step
for i in range(10, 0, -1):   # 10, 9, 8, ..., 1 (countdown)
    print(i)

# Reading a file safely
with open("data.txt") as f:
    content = f.read()
print(content)

# String formatting mini-reference
n = 3.14159
print(f"{n:.2f}")             # 3.14
print(f"{n:>10.2f}")          # '      3.14' (right-align width 10)
print(f"{1234567:,}")         # 1,234,567

# Truthy / falsy check
if my_list:                  # if list is non-empty
    print("has items")
if not my_string:            # if string is empty or None
    print("no string")
```

The `with open(...) as f:` idiom is the standard way to read or write files in Python. It automatically closes the file when the block ends, even if an error occurs inside. Never use raw `f = open(...)` and `f.close()` — the `with` statement is safer and shorter.

---

## 9. Common Pitfalls

These are the bugs that bite every Python beginner at least once. Read them, recognize them, avoid them.

```python
# Pitfall 1: Mutable default arguments
def add_item(item, items=[]):        # ← BUG: list is shared across calls
    items.append(item)
    return items

print(add_item("a"))                # ['a']
print(add_item("b"))                # ['a', 'b'] — surprise!

# Fix: use None
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

# Pitfall 2: is vs ==
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)               # True — same values
print(a is b)               # False — different objects
print(a is None)            # False
print(None is None)         # True — always use 'is' with None

# Pitfall 3: / vs //
print(7 / 2)                # 3.5 — always float
print(7 // 2)               # 3 — floor division (int)

# Pitfall 4: True == 1
print(True == 1)            # True — bool is subclass of int!
print(True + True)          # 2 — booleans can be added
print(isinstance(True, int))  # True

# Pitfall 5: Late binding in closures (advanced)
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs]) # [2, 2, 2] — all return final i!
# Fix: bind with default argument
funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs]) # [0, 1, 2]
```

The mutable-default-argument pitfall is the single most notorious Python gotcha. The `is` vs `==` distinction matters mostly for `None` checks. And the late-binding closure trap (Pitfall 5) is something you will hit when you start writing event handlers or callbacks — keep it in mind for later.

---

## 10. The 10 Built-in Functions You Will Use Most

Python has dozens of built-in functions available without any `import`. Here are the ten you will reach for constantly — commit their signatures to memory.

```python
# 1. print — output to the terminal
print("hello")
print("a", "b", "c")           # a b c
print("a", "b", sep="-")       # a-b
print("loading", end="...")    # loading...

# 2. len — length of a collection
print(len("hello"))            # 5
print(len([1, 2, 3]))          # 3
print(len({"a": 1, "b": 2}))   # 2

# 3. range — generate a sequence of numbers
list(range(5))                 # [0, 1, 2, 3, 4]
list(range(2, 8))              # [2, 3, 4, 5, 6, 7]
list(range(0, 10, 2))          # [0, 2, 4, 6, 8]

# 4. input — read a string from the user
name = input("What is your name? ")
print(f"Hello, {name}!")

# 5. int — convert to integer
print(int("42"))              # 42
print(int(3.9))               # 3 — truncates toward zero
print(int("0xff", 16))        # 255 — base 16

# 6. str — convert to string
print(str(42))                # "42"
print(str(3.14))              # "3.14"

# 7. float — convert to float
print(float("3.14"))          # 3.14
print(float(5))               # 5.0

# 8. type — get the type of a value
print(type(42))               # <class 'int'>
print(type("hi"))             # <class 'str'>

# 9. dir — list attributes of an object (great for exploration)
print(dir("hello"))           # [...'upper', 'lower', 'split', ...]

# 10. open — open a file (always use with 'with')
with open("data.txt") as f:
    content = f.read()
print(content)
```

Bonus built-ins you will meet soon: `sum()`, `min()`, `max()`, `sorted()`, `enumerate()`, `zip()`, `map()`, `filter()`, `isinstance()`, `hasattr()`, `getattr()`. The official docs list every built-in at `docs.python.org/3/library/functions.html` — bookmark that page.

---

## 11. What's Next?

You have completed the Python module. You can install Python, write and run scripts, work with variables and types, control flow with `if`/`for`/`while`, package logic into functions, and model data with lists, tuples, dicts, and sets. That is genuinely a lot — these are the same skills used by professional Python developers every day.

What should you do next? Three options, in order of difficulty:

1. **Build small projects to solidify what you learned.** Write a script that renames all the files in a folder, a script that reads a CSV and prints a summary, or a script that scrapes a web page. Real projects expose gaps in your understanding faster than any tutorial.

2. **Learn a Python framework.** For web backends, learn **Flask** (small, friendly) then **Django** (large, batteries-included). For data science, learn **pandas** and **Jupyter notebooks**. For automation, learn the standard library modules `os`, `pathlib`, `subprocess`, and `re`.

3. **Learn a second, very different language.** The next module in this curriculum is **Rust**, located at `/home/z/my-project/learn-dev/14-rust/`. Rust is the opposite of Python in many ways: compiled, statically-typed, blazingly fast, with no garbage collector. Studying Rust after Python will teach you about memory, types, and performance in ways Python hides from you. Together, Python for fast prototyping and Rust for performance are a powerful combination used at companies like Discord, Figma, and AWS.

Whatever you choose, the most important thing is to **keep writing code every day**. Reading tutorials teaches you what exists; writing code teaches you how it actually works. Open the next module, take your time, and congratulations on finishing Python — you are now a programmer.

---

**Next:** Continue your journey in [Module 14: Rust →](/home/z/my-project/learn-dev/14-rust/)
