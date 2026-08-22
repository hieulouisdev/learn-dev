# Lesson 01: What is Python?

> Module: Python · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your very first Python lesson. In the next 30 minutes, you will learn what Python is, where it came from, how to install it on your computer, and how to run your first Python program. By the end, you will have typed `print("Hello, World!")` into a real Python interpreter and seen it respond.

You do not need any prior programming experience for this lesson. If you completed the HTML and CSS modules, you already understand the *idea* of writing code in a text file and seeing a result — and that is enough. We will start from zero and build up. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Python is, where its name comes from, and why it is popular.
2. Install Python 3.12 or newer on your operating system and verify the installation from the terminal.
3. Distinguish between the interactive REPL and running a `.py` script file, and know when to use each.
4. Run your first Python program (`print("Hello, World!")`) and understand what each character does.

---

## 1. What is Python, Really?

Python is a **high-level, general-purpose programming language**. "High-level" means it hides the messy hardware details — you never have to think about memory addresses, CPU registers, or bytes when writing Python. "General-purpose" means it is not specialized for one job: the same language that runs Instagram's backend can also automate your taxes, train a neural network, and control a robotic arm in a factory.

The language was created by a Dutch programmer named **Guido van Rossum**. He started working on it in December 1989, during the Christmas holidays at his research lab in the Netherlands, as a successor to a teaching language called ABC. The first public release, version 0.9.0, came out in February 1991 — meaning Python is older than the World Wide Web. Today, Guido is affectionately known in the community as the **BDFL** (Benevolent Dictator For Life), although he officially stepped down from the role in 2018.

The name "Python" has nothing to do with the snake. Guido was a big fan of the BBC comedy series *Monty Python's Flying Circus*, and he wanted a name that was short, unique, and slightly mysterious. The snake logo and mascot came later — but the original spirit is comedy, not reptiles. If you ever see a Python tutorial that mentions "spam," "eggs," or "knights who say Ni," now you know why: they are Monty Python references.

Why is Python so beginner-friendly? Three reasons. **First**, the syntax is extremely close to English — keywords like `if`, `else`, `for`, `in`, `not`, `and`, `or` read like real sentences. **Second**, Python uses **indentation** (spaces) instead of curly braces to mark blocks of code, which forces every Python program to look tidy and consistently indented. **Third**, Python is **dynamically typed** — you write `x = 42` without declaring `int x` first, which removes a whole category of beginner confusion that exists in Java and C#. The trade-off is that Python is slower than compiled languages like Rust or C, but for almost everything a beginner does, the speed difference is invisible.

---

## 2. Installing Python

Before you can write Python, you must install the Python interpreter — the program that reads `.py` files and executes them. There are many ways to install Python, but the simplest and most reliable is to download the official installer from [python.org/downloads](https://www.python.org/downloads/). The website auto-detects your operating system and offers the correct installer. As of this writing, the latest stable version is Python 3.13; any version 3.10 or newer will work fine for this entire module.

### On Windows

Download the installer (something like `python-3.13.x-amd64.exe`), double-click it, and **before you click "Install Now", tick the checkbox at the bottom that says "Add Python 3.13 to PATH"**. This is the single most important step. Without it, Windows will not find the `python` command in the terminal and you will get the dreaded "'python' is not recognized as an internal or external command" error. After installation, close any open terminal windows and open a new one so the PATH change takes effect.

### On Mac

Download the macOS installer (something like `python-3.13.x-macos11.pkg`) and run it. Follow the prompts — the defaults are correct. After installation, **open a fresh Terminal window** (the old ones do not see the new command). On Mac, the system version of Python is invoked as `python3`, not `python` — this confuses many beginners. If you use Homebrew, you can also run `brew install python` to get the same result.

### On Linux

Most distributions ship Python 3 already, but it may be an older version. On Debian/Ubuntu, run `sudo apt update && sudo apt install python3 python3-pip` to install Python and its package manager. On Fedora, run `sudo dnf install python3 python3-pip`. On Arch, run `sudo pacman -S python`. After installation, the command is `python3`.

### Verifying the Installation

Open your terminal and type one of the following commands. You should see a version banner.

```bash
python --version
# or, on Mac and Linux:
python3 --version
```

If you see something like `Python 3.13.1`, congratulations — Python is installed and ready. If you see `command not found`, go back and double-check the "Add to PATH" step (Windows) or open a fresh terminal (Mac/Linux). If you see a version starting with `Python 2.7`, you are running the obsolete Python 2 — go install Python 3 from python.org and use the `python3` command instead.

---

## 3. The REPL vs Script Files

Python gives you two ways to run code, and a real developer uses both. Understanding the difference early will save you hours of confusion later.

The first way is the **interactive REPL** (Read-Eval-Print Loop). Open a terminal and type `python` (or `python3`) with no arguments. You will see something like this:

```text
Python 3.13.1 (main, Dec  4 2024, 08:49:23) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

The `>>>` prompt is the REPL waiting for you to type a Python expression. Type `2 + 2` and press Enter — Python immediately responds `4`. Type `print("hi")` and press Enter — it prints `hi`. The REPL is fantastic for **experimenting, exploring a library, or doing quick math**. Think of it as a powerful calculator. To exit, type `quit()` or press `Ctrl+D` (Mac/Linux) / `Ctrl+Z` then Enter (Windows).

The second way is a **script file**. Open your code editor, create a file called `hello.py`, type `print("Hello, World!")` in it, save it, then in the terminal run:

```bash
python hello.py
# or, on Mac and Linux:
python3 hello.py
```

Python reads the file, executes each line in order, prints the output, and exits. Scripts are how you save programs to reuse, share, and grow. Use the REPL for quick experiments (what does `len("python")` return?) and script files for anything you want to keep.

---

## 4. Your First Program

It is finally time. Open a terminal, start the REPL (type `python` or `python3`), and type your first line of Python:

```python
print("Hello, World!")
```

Press Enter. You should see:

```text
Hello, World!
```

Let's break this single line into its parts, because every character matters.

- `print` — the name of a **built-in function**. A function is a named block of code that does something. The `print` function takes whatever you give it and writes it to the screen.
- `(` and `)` — the parentheses **call** the function. In Python, you always call a function by putting parentheses after its name, even if you pass no arguments: `print()` prints an empty line. Without the parentheses, `print` is just a reference to the function object — Python does not actually run it.
- `"Hello, World!"` — a **string literal**. Anything between matching double quotes (or single quotes `'...'`) is text data. Python treats whatever is between the quotes as a single piece of text, including spaces and punctuation.
- The whole thing — `print("Hello, World!")` — is a **statement**. A statement is one complete instruction to Python. Python reads the statement, runs it, and then moves to the next line.

Now exit the REPL (`quit()` or `Ctrl+D`) and try the same thing as a script. Create a file called `hello.py` in your editor, type the same line in it, save, and run it with `python hello.py`. You should see the same output. That is your first real Python program.

---

## 5. Python's Design Philosophy

Every programming language has a personality, and Python's is opinionated. The most famous expression of Python's philosophy is a poem-like document called **The Zen of Python**, written by Tim Peters in 1999. You can read it inside any Python interpreter by typing one special line:

```python
import this
```

Run that in the REPL and you will see 19 aphorisms printed. The most quoted ones are:

- **"Beautiful is better than ugly."** Python values code that looks clean and reads naturally.
- **"Explicit is better than implicit."** Python prefers code that says exactly what it does, rather than hiding behavior behind clever tricks.
- **"There should be one — and preferably only one — obvious way to do it."** This is the biggest contrast with Perl and JavaScript, where there are five ways to do everything. Python tries to make the right way obvious.

This principle — "one obvious way" — is sometimes called the **Pythonic** style. When experienced Python developers say "that's not very Pythonic," they mean your code is fighting the language's design. For example, looping over a list with `for i in range(len(items)): items[i]` works, but the Pythonic way is `for item in items:` — shorter, clearer, more readable.

Contrast this with JavaScript, where you can write a function with `function`, `var`, `let`, `const`, arrow syntax, generator syntax, and so on. Python deliberately has fewer ways to do the same thing, and that is a feature, not a limitation. It means when you read someone else's Python code, you immediately recognize the patterns — they are the same patterns you use yourself. This consistency is why Python codebases tend to be easier to maintain over years than equivalent JavaScript or PHP codebases.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `python --version` (or `python3 --version`). Write down the version number you see. If it is anything below 3.10, go back to python.org and install a newer version. Then run `python -c "print(2 ** 10)"` — this should print `1024`. If you see that, your Python is fully working.

### Exercise 2: Explore the REPL

Start the REPL by typing `python` (or `python3`) in your terminal. Try these expressions one at a time and observe the output: `40 + 2`, `len("python")`, `type(3.14)`, `import this`. When you are done, exit with `quit()` or `Ctrl+D`. Write a one-sentence note about what `len("python")` returns — you will need this fact later.

### Exercise 3: Run Your First Script

Create a new file called `first.py` in your code editor. Type exactly `print("Hello, World!")` on the first line and nothing else. Save the file. In the terminal, navigate to the folder where you saved the file (use `cd`), then run `python first.py` (or `python3 first.py`). Confirm that you see `Hello, World!` printed. If you get an error like "No such file or directory," you are in the wrong folder — use `pwd` (Mac/Linux) or `cd` (Windows) to check where you are.

---

## Common Mistakes

### Mistake 1: Using Python 2 syntax

```python
# WRONG (Python 2 — obsolete)
print "Hello, World!"
```

```python
# RIGHT (Python 3)
print("Hello, World!")
```

In Python 2, `print` was a statement, not a function, so parentheses were not required. In Python 3, `print` is a function and **must** have parentheses. If you copy a snippet from an old blog post and get `SyntaxError: Missing parentheses in call to 'print'`, the article was written for Python 2 — find a newer tutorial.

### Mistake 2: Using `python` instead of `python3` on Mac/Linux

```bash
# On Mac/Linux, this often points to Python 2 (or nothing)
python hello.py

# Use this instead
python3 hello.py
```

On Mac and many Linux distributions, the bare command `python` either does not exist or points to an old Python 2. Always use `python3` to be sure you are running Python 3. You can also create an alias in your shell profile (`alias python=python3`) so the bare `python` command works for you.

### Mistake 3: Indentation errors from mixing spaces and tabs

```python
# WRONG — first line uses 4 spaces, second line uses 1 tab
def greet():
    print("hi")
	print("bye")   # ← this is a tab character
```

```python
# RIGHT — every indented line uses 4 spaces
def greet():
    print("hi")
    print("bye")
```

Python is one of the only languages where indentation is **part of the syntax**, not just for looks. If you mix tabs and spaces, Python raises `IndentationError: unindent does not match any outer indentation level`. Always configure your editor to insert spaces when you press Tab (the standard is 4 spaces per indent level). VS Code does this by default for Python files.

### Mistake 4: Saving the file as `hello.py.txt` by mistake

```text
hello.py.txt   ← Windows hid the .txt extension; Python never sees it
```

On Windows, Notepad and some editors silently append `.txt` to the filename. When you run `python hello.py`, you get `can't open file 'hello.py': [Errno 2] No such file or directory`. Fix: in File Explorer, click the View menu and tick "File name extensions" so you can see the full name. Then rename `hello.py.txt` to `hello.py`. Or save from VS Code, which respects the extension you type.

---

## Summary

- Python is a high-level, interpreted, dynamically-typed programming language created by Guido van Rossum.
- The name comes from Monty Python's Flying Circus, not from the snake.
- Python was started in December 1989 and first released in February 1991.
- Install Python 3.12+ from python.org; on Windows, tick "Add Python to PATH".
- On Mac and Linux, use `python3` instead of `python` to ensure you get Python 3.
- The REPL (`python` with no arguments) is for experiments; script files (`.py`) are for real programs.
- Python's philosophy is "There should be one — and preferably only one — obvious way to do it."
- Read the Zen of Python anytime by typing `import this` in the REPL.

You wrote your first Python program. That is the hardest step — getting the toolchain working. From here on, every lesson will build on this foundation, and you will be writing real, useful programs by Lesson 05. Take a short break, then continue to Lesson 02 where we will go deeper into `print()`, comments, and Python's signature indentation rules.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
