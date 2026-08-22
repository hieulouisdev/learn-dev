# Lesson 02: Hello, World!

> Module: Python · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01, you ran `print("Hello, World!")` and saw text appear on your screen. That was a tiny taste of Python. In this lesson, we slow down and dissect every piece of writing and running a real Python script file, so that by the end you understand exactly how Python reads your code, how to leave notes for yourself, and why indentation is the most important concept in the language.

We will create a real `.py` file, learn the `print()` function in depth, write comments, explore Python's signature indentation rules, and run our script from the terminal. This is the workflow you will repeat thousands of times as a Python developer, so it is worth getting right now.

---

## Learning Objectives

After this lesson, you will be able to:

1. Create a Python script file (`.py`) in a project folder and run it from the terminal.
2. Use the `print()` function with multiple arguments, the `sep=` parameter, and the `end=` parameter.
3. Write single-line comments with `#` and multi-line docstrings with `"""..."""`.
4. Explain why Python uses indentation instead of curly braces, and indent code correctly.

---

## 1. Creating a Python File

A Python program is just a plain text file with the `.py` extension. There is no special ceremony, no project file, no build step — you write the file, you run it, you see the output. Let's set up a small folder for everything you do in this module.

### Step 1: Create a folder

On your computer, create a folder called `learn-python` (or any name you like). Inside it, create another folder called `lesson-02`. This keeps your work organized by lesson, which will matter when you have dozens of files.

```text
learn-python/
└── lesson-02/
    └── hello.py
```

### Step 2: Create the file

Open VS Code, then use `File → Open Folder` to open the `learn-python` folder you just created. Inside the `lesson-02` subfolder, create a new file called `hello.py`. The `.py` extension is what tells VS Code to treat it as a Python file (giving you syntax highlighting and autocomplete) and what tells the operating system to treat it as a Python script.

### Step 3: Type your first script

Type this exact code into `hello.py`. Do not copy and paste — typing it yourself trains your fingers.

```python
# This is my first real Python script
print("Hello, World!")
print("My name is Hieu.")
print("I am learning Python.")
```

### Step 4: Run the script

Open the integrated terminal in VS Code (`Ctrl+\`` on Windows/Linux, `Cmd+\`` on Mac). Make sure you are inside the `lesson-02` folder (use `cd lesson-02` if needed). Then run:

```bash
python hello.py
# or, on Mac and Linux:
python3 hello.py
```

You should see three lines printed in the terminal:

```text
Hello, World!
My name is Hieu.
I am learning Python.
```

If you see those three lines — congratulations. You have written and run your first real Python script. This edit-save-run loop is the heartbeat of Python development, and you will repeat it thousands of times.

---

## 2. The `print()` Function in Detail

In Lesson 01, you saw `print("Hello, World!")`. Now let's look at what `print()` can really do. The function accepts any number of arguments, separated by commas, and prints them on one line by default.

```python
print("Hello,", "Hieu", "—", "welcome to Python!")
```

Run this and you get:

```text
Hello, Hieu — welcome to Python!
```

Notice that Python automatically inserts a space between each argument. That is the default `sep` (separator) behavior — by default `sep=" "` (one space). You can change it:

```python
print("2025", "10", "15", sep="-")
```

Output:

```text
2025-10-15
```

The `sep="-"` argument tells `print()` to use a dash between the values instead of a space. You can use any string: `sep=", "`, `sep=" | "`, `sep=""` (no separator at all). This is how you build date strings, CSV lines, or formatted log lines without messy string concatenation.

By default, every `print()` call ends with a newline character — that is why each `print` in the previous example was on its own line. You can change this with the `end=` argument:

```python
print("Loading", end="...")
print("done")
```

Output:

```text
Loading...done
```

The first `print` ended with `...` instead of a newline, so the second `print` continued on the same line. This is useful for progress bars, prompts, and any case where you want multiple `print()` calls to share one line. The full signature of `print()` is `print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)` — for now you only need `sep` and `end`, but knowing that `file=` exists (you can print to a file instead of the screen) is a useful mental note for later.

---

## 3. Comments

Comments are notes inside your code that Python completely ignores. They exist for one reason: to explain your thinking to a human reader (which, six months from now, includes you). Python has two kinds of comments.

### Single-line comments with `#`

Anything from a `#` to the end of the line is a comment:

```python
# Calculate the user's age in months
age_years = 25
age_months = age_years * 12  # 12 months in a year
print(age_months)  # prints 300
```

The first line is a full-line comment — the whole line is a note. The second and third lines have **inline comments** at the end of code. Both work. Use full-line comments to explain what a section of code does, and inline comments to explain tricky individual lines. Do not over-comment obvious code — `x = 5  # assign 5 to x` is noise, not help.

### Multi-line docstrings with `"""..."""`

For longer descriptions — especially describing what a function or module does — Python uses **docstrings**: triple-quoted strings that appear as the first statement of a function or file.

```python
"""
This is a docstring.
It can span multiple lines.
Python ignores it at runtime, but tools like help() and IDEs read it.
"""
print("Docstrings do not affect this line.")
```

A docstring is technically a string literal, not a comment, but because it sits at the top of a file or function and is not assigned to anything, Python ignores it the same way it ignores comments. We will use docstrings properly in Lesson 05 when we cover functions. For now, remember: use `#` for short notes, and `"""..."""` for longer explanations at the top of a file or function.

---

## 4. Indentation-Based Blocks

This is the most important section in this entire module. Read it twice. Python is one of the very few languages where **whitespace (spaces and tabs) is part of the syntax**. There are no curly braces `{}` to mark code blocks like in JavaScript, Java, C, or C++. Instead, Python uses indentation.

Here is a comparison. In JavaScript, a conditional looks like this:

```javascript
// JavaScript
if (true) {
    console.log("yes");
    console.log("still yes");
}
```

In Python, the same logic looks like this:

```python
# Python
if True:
    print("yes")
    print("still yes")
```

Notice the differences:

- No parentheses around the condition (`True` instead of `(true)`).
- A colon `:` at the end of the `if` line — this is mandatory and is what tells Python "a block is coming."
- No curly braces — instead, the next two lines are indented by 4 spaces, and that indentation **is the block**.
- No semicolons at the end of statements.

### Correct vs incorrect indentation

```python
# CORRECT — every line in the block is indented the same amount
age = 20
if age >= 18:
    print("You are an adult.")
    print("You can vote.")
print("This line is outside the if block.")
```

```python
# WRONG — the second print is not indented, so it is not part of the if
age = 20
if age >= 18:
    print("You are an adult.")
print("You can vote.")  # ← IndentationError or wrong logic
```

The second example either crashes with an `IndentationError` or runs but does the wrong thing (the "You can vote" line runs always, not conditionally). Indentation is not decoration in Python — it is meaning.

### Why does Python do this?

Two reasons. **First, readability.** Because every Python program is forced to be indented consistently, Python code visually shows its structure at a glance — you can see what is inside what just by looking. **Second, no brace wars.** In languages with curly braces, developers argue endlessly about where braces go (same line or next line, "K&R style" vs "Allman style"). Python eliminates the entire argument: there are no braces to argue about. The community agreed on 4 spaces per level (PEP 8, the official style guide) and that is the end of it. Configure your editor to insert 4 spaces when you press Tab, and you will never have an indentation problem again.

---

## 5. Running a Script from the Terminal

We have already run scripts from the terminal a few times. Let's look at this workflow explicitly, because you will do it hundreds of times and small details matter.

### The basic command

```bash
python hello.py
# or on Mac and Linux:
python3 hello.py
```

Python opens `hello.py`, reads it from top to bottom, executes each statement in order, and exits when it reaches the end of the file. Any output produced by `print()` goes to your terminal.

### Why Mac and Linux users need `python3`

On macOS and many Linux distributions, the bare `python` command either does not exist or points to an old Python 2 (which is end-of-life and incompatible with this module). To make sure you run Python 3, always type `python3`. If you find this annoying, add an alias to your shell profile (`~/.zshrc` on Mac, `~/.bashrc` on Linux):

```bash
alias python=python3
alias pip=pip3
```

After saving the file and running `source ~/.zshrc` (or `source ~/.bashrc`), the bare `python` command will run Python 3 for you.

### Common errors you will see

If you are in the wrong folder, you get:

```text
can't open file 'hello.py': [Errno 2] No such file or directory
```

Fix: use `pwd` (Mac/Linux) or `cd` (Windows) to check where you are, then `cd` to the folder containing `hello.py`. If you saved the file as `hello.py.txt` by accident (Windows hides extensions by default), rename it to `hello.py`. If you see `SyntaxError`, the file ran but has a typo — read the error message; it usually points to the exact line.

---

## Try It Yourself

### Exercise 1: Modify the Greeting

Open `hello.py` and change the three `print` lines so they introduce yourself: your name, your favorite color, and one thing you hope to learn from Python. Run the script. Make sure all three lines print on separate lines. Then add a fourth `print` that uses the `sep=` argument to print three words separated by `" | "`.

### Exercise 2: Add a Comment Block

Add a multi-line docstring at the very top of `hello.py` that describes what the script does and who wrote it. Then add at least two `#` comments inside the script — one full-line comment before a section, and one inline comment at the end of a `print` line. Run the script again. Confirm that the comments do not appear in the output.

### Exercise 3: Write a Multi-Line Print

Use the `end=` parameter to print the sentence `"Python is fun."` on a single terminal line, but built from three separate `print()` calls. The first call prints `"Python "`, the second prints `"is "`, and the third prints `"fun."`. The final output should be exactly one line: `Python is fun.`. Hint: pass `end=""` to the first two calls so they do not add a newline.

---

## Common Mistakes

### Mistake 1: Mixing tabs and spaces

```python
# WRONG — first line uses 4 spaces, second line uses 1 tab
def greet():
    print("hello")        # 4 spaces
	print("world")        # ← one tab character
```

```python
# RIGHT — both lines use 4 spaces
def greet():
    print("hello")
    print("world")
```

Mixing tabs and spaces causes `IndentationError: unindent does not match any outer indentation level`. In VS Code, look at the bottom-right of the window — it should say "Spaces: 4" for Python files. If it says "Tab Size: 4" but you suspect real tabs, run the command "Convert Indentation to Spaces" from the Command Palette.

### Mistake 2: Using `print` without parentheses

```python
# WRONG — Python 2 syntax, no parentheses
print "Hello, World!"
```

```python
# RIGHT — Python 3 syntax
print("Hello, World!")
```

In Python 3, `print` is a function and **must** have parentheses. If you forget them, you get `SyntaxError: Missing parentheses in call to 'print'`. The error message is friendly and tells you exactly what to fix. This mistake is most common when copying from old tutorials written for Python 2.

### Mistake 3: Forgetting the colon `:` before an indented block

```python
# WRONG — missing colon
if True
    print("yes")
```

```python
# RIGHT — colon at end of if line
if True:
    print("yes")
```

Every statement that introduces a block (`if`, `elif`, `else`, `for`, `while`, `def`, `class`, `with`, `try`, `except`) must end with a colon `:`. If you forget it, Python raises `SyntaxError: expected ':'`. If your editor is set up well, it will usually highlight the missing colon with a red squiggle before you even run the file.

### Mistake 4: Using curly braces by mistake (JavaScript habit)

```python
# WRONG — this is JavaScript syntax, not Python
if True {
    print("yes")
}
```

```python
# RIGHT — Python uses indentation, no braces
if True:
    print("yes")
```

If you are coming from JavaScript, Java, C, or C++, your fingers will type `{}` automatically. Python will accept the braces — they are valid syntax for a **set** (which you will meet in Lesson 06) — but they will not do what you expect. Break the habit by repeating the rule out loud: **colon, then indent, no braces.** After a few days of Python, the habit fades.

---

## Summary

- A Python program is a plain text file with the `.py` extension.
- `print()` accepts multiple arguments; use `sep=` to change the separator and `end=` to change the line ending.
- Single-line comments start with `#`; multi-line docstrings use `"""..."""`.
- Python uses indentation to mark code blocks — there are no curly braces.
- A colon `:` at the end of `if`, `for`, `while`, `def`, etc. signals that a block is coming.
- Always use 4 spaces per indent level, never tabs.
- On Mac and Linux, run scripts with `python3 script.py` to be sure you are using Python 3.
- The edit-save-run loop (`Ctrl+S`, then `python hello.py`) is the daily workflow of every Python developer.

You can now create, comment, and run a Python script. You understand indentation, which is the single biggest mental shift from JavaScript-style languages. In Lesson 03, we will start working with real data — variables, numbers, text, booleans, and Python's elegant f-strings.

---

**Next:** [Lesson 03: Variables and Data Types →](./03-variables-and-data-types.md)
