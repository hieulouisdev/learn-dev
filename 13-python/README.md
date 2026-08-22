# Module 13: Python — The Friendly All-Rounder

<p align="center">
  <img src="../logorepo.png" alt="Learn Dev Logo" width="120" height="120" />
</p>

Welcome to Module 13. So far you have learned the languages of the browser: HTML for structure, CSS for style, and (we hope) a little JavaScript for behavior. Now we step away from the browser and enter the world of **general-purpose programming** with Python — a language that runs scripts, builds servers, trains AI models, crunches data, and automates the boring parts of everyday work.

Python is famous for being **the most beginner-friendly language** that is also powerful enough to run the world's largest websites. Instagram, Spotify, Netflix, Dropbox, and Google all rely heavily on Python in production. In this module, you will go from "What is Python?" to writing real programs that make decisions, repeat work, and structure data.

---

## What is Python?

Python is a **high-level, interpreted, dynamically-typed programming language** created by Guido van Rossum and first released in 1991. "High-level" means it sits far above the hardware — you do not manage memory, pointers, or CPU registers like you would in C. "Interpreted" means Python code is run line-by-line by a program called the Python interpreter, instead of being compiled into machine code ahead of time like C++ or Rust. "Dynamically-typed" means you do not declare a variable's type; Python figures it out from the value you assign.

The name "Python" is **not** about the snake. Guido van Rossum was a fan of the British comedy series *Monty Python's Flying Circus*, and he wanted a name that was short, unique, and slightly mysterious. The snake mascot came later as a convenient coincidence, and today both associations are everywhere — but the language's true mascot is the comedy troupe, not the reptile.

Python's biggest selling point is **readability**. It uses English keywords (`if`, `else`, `for`, `in`, `not`, `and`, `or`) instead of symbols (`&&`, `||`, `!`, `{}`). It enforces indentation as part of the syntax, which forces every Python program to look tidy and consistent. A typical Python program reads almost like English sentences, which is why so many universities and coding bootcamps teach Python as a first language.

Python is also **batteries-included**: the standard library that ships with Python contains hundreds of modules for working with files, dates, regular expressions, JSON, networking, math, and more. Need to download a web page? `urllib.request` is built in. Need to parse JSON? `import json`, one line, done. This means a beginner can build genuinely useful tools without installing a single third-party package.

In industry, Python dominates four major areas. First, **artificial intelligence and machine learning** — the libraries PyTorch, TensorFlow, and scikit-learn are all Python-first. Second, **data science** — pandas, NumPy, and Jupyter notebooks are the lingua franca of data analysts. Third, **web backends** — Django, Flask, and FastAPI power backends at Instagram, Pinterest, and Reddit. Fourth, **automation and scripting** — sysadmins, scientists, and accountants write Python scripts to automate tedious tasks that used to take hours. Wherever there is code, there is probably some Python nearby.

---

## Why Learn Python?

You already know HTML and CSS. Why add Python to your toolkit? Here are the strongest reasons.

- **It is the easiest path from "I can write markup" to "I can write programs."** HTML and CSS are not programming languages — they describe structure and appearance. Python is the gentlest possible introduction to actual programming concepts like variables, loops, functions, and data structures. Its syntax removes most of the punctuation noise that confuses beginners in JavaScript, Java, or C, so you can focus on the ideas instead of the brackets.

- **It opens the door to AI, data science, and machine learning.** If you have ever dreamed of building a chatbot, training an image classifier, or analyzing a dataset with millions of rows, Python is the language you need. Almost every modern AI tool — from OpenAI's APIs to local Llama models — has a Python SDK as its first-class citizen, and the tutorials you find online will all assume Python.

- **It pays well and has enormous job demand.** Python has been one of the top three most in-demand languages on job boards for over five years. Roles like "data analyst," "backend engineer," "ML engineer," "DevOps engineer," and "QA automation engineer" all regularly list Python as a required skill. The salary ranges are competitive with JavaScript and Java roles, and the barrier to entry is lower because the language is so readable.

- **It automates the boring stuff.** Even if you never become a full-time software engineer, Python can save you hours every week. Rename 500 files in a folder, scrape prices from a website, convert a thousand CSV files to JSON, send personalized emails to a list of contacts — these are 20-line Python scripts, not weekend projects. Many people learn Python purely to automate their day job, even if their job title has nothing to do with programming.

- **It has the friendliest community and the largest ecosystem of free libraries.** The Python Package Index (PyPI) hosts over 500,000 free packages. Whatever you want to do — generate PDFs, send SMS, control a Raspberry Pi, build a Discord bot, manipulate images — there is almost always a one-line `pip install` that gets you started, plus a tutorial on YouTube or a blog post on Medium explaining exactly how.

---

## How This Module Works

This module contains 7 lessons. Each lesson is a separate Markdown file in this folder. Read them in order — later lessons assume you understood the earlier ones.

| # | Lesson | What You Will Learn |
|---|--------|---------------------|
| 01 | [What is Python?](./01-what-is-python.md) | What Python is, how to install it, and your first program. |
| 02 | [Hello, World!](./02-hello-world.md) | Writing and running a `.py` file, `print()`, comments, and indentation. |
| 03 | [Variables and Data Types](./03-variables-and-data-types.md) | `int`, `float`, `str`, `bool`, `None`, type conversion, f-strings. |
| 04 | [Control Flow](./04-control-flow.md) | `if` / `elif` / `else`, `for`, `while`, `break`, `continue`. |
| 05 | [Functions](./05-functions.md) | `def`, parameters, default values, `return`, keyword arguments, docstrings. |
| 06 | [Lists, Dicts, Tuples, and Sets](./06-lists-dicts-tuples-sets.md) | The four core collection types and when to use each. |
| 07 | [Python Cheatsheet](./07-python-cheatsheet.md) | A printable one-page summary of everything. |

---

## How to Practice

For this entire module, you will need three things. The setup takes about 15 minutes and you only do it once.

1. **Install Python 3.12 or newer** from [python.org/downloads](https://www.python.org/downloads/). Download the installer for your operating system, run it, and **on Windows, tick the box that says "Add Python to PATH"** before clicking Install. On Mac, the official installer works fine; alternatively you can use Homebrew with `brew install python`. On Linux, use your package manager: `sudo apt install python3` on Debian/Ubuntu or `sudo dnf install python3` on Fedora.

2. **Install Visual Studio Code** with the official Python extension. Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/). Once installed, open the Extensions panel (`Ctrl+Shift+X` / `Cmd+Shift+X`), search for "Python" by Microsoft, and click Install. This gives you syntax highlighting, autocompletion, a built-in terminal, and a debugger — all the essentials for writing Python comfortably.

3. **Use the terminal to run your scripts.** Open a folder in VS Code, create a file called `hello.py`, type some code, then open the integrated terminal (`Ctrl+\`` / `Cmd+\``) and run `python hello.py` (on Windows) or `python3 hello.py` (on Mac and Linux). You will see your output printed in the terminal window. This edit-save-run feedback loop is the heart of Python development and you will repeat it thousands of times.

---

## Estimated Time

If you spend 30 to 60 minutes per lesson (reading, typing out the examples, and doing the exercises), you should complete this module in roughly **8 to 12 hours of total study**. Do not try to do it all in one weekend. Your brain needs sleep between sessions to absorb the concepts, especially the difference between lists, tuples, dicts, and sets in Lesson 06.

If you get stuck, re-read the previous lesson. Every concept in Python builds on the previous one, so a shaky foundation will cause problems later. Take your time. Type every example yourself rather than copy-pasting — your fingers need to learn the keystrokes, and muscle memory is a real part of learning to code.

---

<p align="center">
  Ready? Open <a href="./01-what-is-python.md">Lesson 01: What is Python?</a> and write your first Python program.
</p>
