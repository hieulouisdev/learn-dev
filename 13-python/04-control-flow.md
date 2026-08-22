# Lesson 04: Control Flow

> Module: Python · Lesson 4 of 7
> Estimated time: 30–45 minutes

So far your Python programs have run straight from top to bottom, executing each line once in order. Real programs do not work that way — they make decisions based on input, repeat work many times, and skip or stop early when conditions are met. This lesson teaches you **control flow**: the keywords that direct how Python moves through your code.

You will learn `if`/`elif`/`else` for decisions, comparison and logical operators for building conditions, `for` and `while` loops for repetition, and `break`/`continue` for fine-grained control over loops. By the end, you will be able to write programs that respond to their inputs and automate repetitive work — which is, after all, the whole point of programming.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write `if` / `elif` / `else` statements with correct Python syntax (colon and indentation, no parentheses).
2. Use comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) and explain the difference between `==` and `is`.
3. Combine conditions with `and`, `or`, `not` and apply short-circuit evaluation rules.
4. Write `for` loops with `range()` and over collections; write `while` loops; use `break` and `continue`.

---

## 1. `if` / `elif` / `else`

An `if` statement lets your program run a block of code only when a condition is true. Python's syntax is minimal and readable — no parentheses around the condition, just the keyword, the condition, a colon, and an indented block.

```python
age = 20

if age >= 18:
    print("You are an adult.")
    print("You can vote.")
elif age >= 13:
    print("You are a teenager.")
else:
    print("You are a child.")
```

Let's break it down line by line. The line `if age >= 18:` says "if the value of `age` is greater than or equal to 18, run the indented block that follows." The colon at the end is mandatory — it is what tells Python a block is coming. The next two lines (`print("You are an adult.")` and `print("You can vote.")`) are the **block** — they are both indented 4 spaces and that is what makes them part of the `if`. The `elif` (short for "else if") is checked only when the first condition was false. The `else` block runs only if no condition above was true. Exactly one of these three blocks will run.

Compare the same logic in JavaScript, where braces and parentheses clutter the screen:

```javascript
// JavaScript — more symbols, same logic
if (age >= 18) {
    console.log("You are an adult.");
    console.log("You can vote.");
} else if (age >= 13) {
    console.log("You are a teenager.");
} else {
    console.log("You are a child.");
}
```

Python's version is shorter, has no braces, no semicolons, and reads almost like English. The trade-off is that the indentation is **required** — if you mis-indent a line, it changes what the program does. This is the cost of Python's readability.

### Truthiness in conditions

The condition of an `if` does not have to be a comparison. It can be any value at all — Python applies the **truthiness rules** from Lesson 03.

```python
name = ""
if name:
    print(f"Hello, {name}!")
else:
    print("You did not give a name.")
```

Because `name` is the empty string (`""`), which is falsy, the `else` block runs. This idiom — `if my_list:` instead of `if len(my_list) > 0:` — is preferred Python style. It reads naturally and works for any type.

---

## 2. Comparison Operators

Python has six comparison operators. They all return a `bool` (`True` or `False`).

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `==` | equal to | `5 == 5` | `True` |
| `!=` | not equal to | `5 != 3` | `True` |
| `<`  | less than | `3 < 5` | `True` |
| `>`  | greater than | `3 > 5` | `False` |
| `<=` | less than or equal | `5 <= 5` | `True` |
| `>=` | greater than or equal | `4 >= 5` | `False` |

Notice: equality is `==` (two equals), not `=` (one equals). A single `=` is the **assignment** operator — `x = 5` stores 5 in `x`. Using `=` inside an `if` is a `SyntaxError` in Python, which protects you from the classic C/JavaScript bug of writing `if (x = 5)` by accident.

### `==` vs `is`

`==` compares **values** — does this variable hold the same data as that one? `is` compares **identity** — are these two variables literally the same object in memory? For most comparisons of strings, numbers, and lists, you want `==`.

```python
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)         # True — same values
print(a is b)         # False — different list objects in memory
```

The one place `is` is mandatory: comparing to `None`. Always write `if x is None:` or `if x is not None:`, never `if x == None:`. The reason is subtle — some custom objects override `==` in surprising ways, but `is None` is a pure identity check that can never lie. Treat it as a Python idiom and use it always.

### Chained comparisons

Python has a delightful feature: you can chain comparisons in a single expression, just like in mathematics.

```python
age = 25
if 18 <= age <= 65:
    print("Working age.")
# Equivalent to: if 18 <= age and age <= 65:
```

Most languages require you to write the awkward `18 <= age && age <= 65`. Python lets you write what you actually mean. Use this freely — it is idiomatic and readable.

---

## 3. Logical Operators: `and`, `or`, `not`

Python uses English words for logical operators, not symbols. This is one of the most distinctive and beginner-friendly features of the language.

| Python | JavaScript / C | Meaning |
|--------|----------------|---------|
| `and`  | `&&`           | both must be true |
| `or`   | `\|\|`         | at least one is true |
| `not`  | `!`            | flip true/false |

```python
age = 25
has_id = True

if age >= 18 and has_id:
    print("You may enter.")

if age < 13 or age > 65:
    print("Discount applies.")

if not has_id:
    print("You need an ID.")
```

Each condition is evaluated left to right. The `and` operator returns the first falsy value it sees (or the last value if all are truthy). The `or` operator returns the first truthy value it sees (or the last value if all are falsy). This is called **short-circuit evaluation**.

```python
result = "" or "default"
print(result)         # default — "" is falsy, so 'or' returns the second value

result = "exists" or "default"
print(result)         # exists — first value is truthy, 'or' short-circuits

result = "x" and "y"
print(result)         # y — both truthy, 'and' returns the last value
```

This lets you write elegant defaults: `username = input_name or "guest"` means "use `input_name` if it is truthy, otherwise use 'guest'." Many Python developers prefer this over an `if`/`else` block because it reads as a single thought. Once you internalize short-circuit evaluation, you will use it constantly.

---

## 4. `for` Loops

A `for` loop repeats a block of code **once for each item in a collection**. The most common collection to loop over is the `range()` function, which generates a sequence of numbers.

```python
# Count from 0 to 4
for i in range(5):
    print(i)
```

Output:

```text
0
1
2
3
4
```

`range(5)` produces the numbers 0, 1, 2, 3, 4 — **five numbers, starting at 0, not including 5**. This is the "half-open interval" convention that Python shares with most modern languages: the start is included, the end is excluded. Memorize this — it eliminates entire classes of off-by-one errors.

### Variations of `range()`

`range()` accepts one, two, or three arguments:

```python
# range(stop) — 0 to stop-1
for i in range(3):
    print(i)              # 0, 1, 2

# range(start, stop) — start to stop-1
for i in range(2, 8):
    print(i)              # 2, 3, 4, 5, 6, 7

# range(start, stop, step) — start to stop-1, stepping by 'step'
for i in range(0, 10, 2):
    print(i)              # 0, 2, 4, 6, 8

# Negative step counts down
for i in range(5, 0, -1):
    print(i)              # 5, 4, 3, 2, 1
```

### Looping over collections

You can loop over any collection — lists, strings, dictionaries — with the same `for item in collection:` pattern.

```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)              # apple, banana, cherry (one per line)

for char in "Hi":
    print(char)               # H, i (one per line)
```

This is the **Pythonic way** to loop. Beginners coming from C or JavaScript often write `for i in range(len(fruits)): print(fruits[i])` — that works but is unidiomatic. Use `for item in fruits:` whenever you do not need the index. If you need both the index and the value, use `enumerate()`:

```python
fruits = ["apple", "banana", "cherry"]
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry
```

---

## 5. `while` Loops

A `while` loop repeats a block **as long as a condition is true**. Use it when you do not know in advance how many times you need to repeat.

```python
countdown = 3
while countdown > 0:
    print(countdown)
    countdown -= 1
print("Liftoff!")
```

Output:

```text
3
2
1
Liftoff!
```

The condition `countdown > 0` is checked before each iteration. If it is true, the block runs. Then the condition is checked again. The loop ends when the condition becomes false. **You must update the variable inside the loop** — in this case, `countdown -= 1` decreases `countdown` by 1 each time. If you forget to update the variable, you create an **infinite loop** that runs forever and you must kill it with `Ctrl+C` in the terminal.

`while` is best for situations where the number of iterations is unknown — for example, "keep asking the user for input until they type 'quit'." If you know the count in advance (`repeat 10 times`, `for each item in this list`), use a `for` loop instead. The Python community strongly prefers `for` because it cannot accidentally infinite-loop.

---

## 6. `break` and `continue`

Two keywords give you fine-grained control over loops. **`break`** exits the loop immediately, skipping any remaining iterations. **`continue`** skips the rest of the current iteration and jumps to the next one.

```python
# break — exit the loop early
for num in range(1, 10):
    if num == 5:
        break
    print(num)
# Output: 1, 2, 3, 4
```

```python
# continue — skip the current iteration
for num in range(1, 6):
    if num % 2 == 0:
        continue
    print(num)
# Output: 1, 3, 5
```

In the first example, the loop prints `1`, `2`, `3`, `4` — when `num` reaches `5`, the `if` triggers `break` and the loop ends. The numbers 6 through 9 never print. In the second example, when `num` is even (2, 4), the `continue` skips the `print`, so only odd numbers print.

A common pattern is to use `break` inside a `while True:` loop with an exit condition inside:

```python
while True:
    command = input("Type 'quit' to exit: ")
    if command == "quit":
        break
    print(f"You typed: {command}")
print("Goodbye.")
```

`while True:` looks dangerous — an infinite loop! — but the `break` inside makes it safe. This is the standard Python idiom for "keep doing this until something happens." It is clearer than a `while` with a complicated condition because the exit logic is right there next to where it triggers.

---

## Try It Yourself

### Exercise 1: Grade Classifier

Write a script called `grade.py` that defines a variable `score = 85` (an integer from 0 to 100). Use `if` / `elif` / `else` to print: `"A"` for 90 and above, `"B"` for 80–89, `"C"` for 70–79, `"D"` for 60–69, and `"F"` for below 60. Change the value of `score` and run the script several times to confirm every branch works correctly.

### Exercise 2: Sum of Even Numbers

Write a script called `sum_even.py` that uses a `for` loop with `range()` to add up all even numbers from 1 to 100 (inclusive). Print the final sum. Hint: `range(2, 101, 2)` produces 2, 4, 6, ..., 100. The expected answer is 2550. Then modify your script to use `continue` to skip odd numbers in a `range(1, 101)` loop — both versions should give the same result.

### Exercise 3: Number Guessing Loop

Write a script called `guess.py` that defines `secret = 7` and `guess = 1`. Use a `while` loop that keeps incrementing `guess` by 1 and printing it, until `guess == secret`. When the loop ends, print `"Found it!"`. Then add a `break` version: use `while True:` with an `if guess == secret: break` inside. Confirm both scripts print the same output.

---

## Common Mistakes

### Mistake 1: Forgetting the colon `:`

```python
# WRONG — missing colon
if age >= 18
    print("Adult")
```

```python
# RIGHT — colon at end of the if line
if age >= 18:
    print("Adult")
```

Every statement that introduces a block (`if`, `elif`, `else`, `for`, `while`, `def`, `class`, `with`, `try`, `except`) must end with a colon. If you forget it, Python raises `SyntaxError: expected ':'`. VS Code and most editors will draw a red underline under the missing colon — pay attention to those squiggles before you even run the file.

### Mistake 2: Mixing tabs and spaces

```python
# WRONG — first line uses 4 spaces, second line uses a tab
if True:
    print("yes")
	print("no")    # ← tab character here
# IndentationError: unindent does not match any outer indentation level
```

```python
# RIGHT — every indented line uses 4 spaces
if True:
    print("yes")
    print("no")
```

Python's indentation-sensitive syntax means **tabs and spaces cannot be mixed**. The fix is in your editor: open VS Code settings, search for "Insert Spaces," and make sure it is enabled with a Tab Size of 4. If you ever inherit a file with mixed indentation, run the "Convert Indentation to Spaces" command from the Command Palette to fix the entire file in one click.

### Mistake 3: Using `&&` instead of `and`

```python
# WRONG — JavaScript syntax
if age >= 18 && has_id:
    print("Enter")
# SyntaxError
```

```python
# RIGHT — Python uses English words
if age >= 18 and has_id:
    print("Enter")
```

Python uses `and`, `or`, `not` — not `&&`, `||`, `!`. This is one of the most common mistakes for developers coming from JavaScript, C, or Java. The rule is simple: **Python uses English, never symbols, for logical operators.** Once you adjust, you will find the English versions read more naturally — `if age >= 18 and has_id:` reads like a real sentence.

### Mistake 4: Off-by-one with `range()`

```python
# WRONG — wanted to print 1 through 5, but only prints 1 through 4
for i in range(1, 5):
    print(i)
```

```python
# RIGHT — range stop is exclusive, so add 1
for i in range(1, 6):
    print(i)
```

`range(1, 5)` produces 1, 2, 3, 4 — **it does not include 5**. This is the "half-open interval" convention: the start is included, the end is excluded. If you want to count from 1 to N inclusive, write `range(1, N + 1)`. If you want to count from 0 to N-1, write `range(N)`. Memorize this rule — it eliminates most off-by-one bugs in loops.

---

## Summary

- `if` / `elif` / `else` use a colon and indentation, no parentheses or braces.
- Six comparison operators: `==`, `!=`, `<`, `>`, `<=`, `>=`.
- Use `==` to compare values; use `is` only for identity (especially `is None`).
- Python uses `and`, `or`, `not` instead of `&&`, `||`, `!`.
- Chained comparisons like `18 <= age <= 65` work and are idiomatic.
- `for` loops iterate over `range()` or any collection; use `enumerate()` to get index + value.
- `while` loops repeat as long as a condition is true — make sure to update the variable.
- `break` exits a loop; `continue` skips to the next iteration.

You can now make decisions and repeat work — the two pillars of programming. In Lesson 05, you will learn to package reusable code into **functions**, so you can write a block once and call it many times with different inputs. This is how small scripts grow into real programs.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
