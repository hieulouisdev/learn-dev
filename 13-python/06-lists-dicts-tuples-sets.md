# Lesson 06: Lists, Dicts, Tuples, and Sets

> Module: Python · Lesson 6 of 7
> Estimated time: 30–45 minutes

Almost every program needs to work with **collections** of values, not just single values. Python ships with four built-in collection types that cover 95% of everyday needs: **lists** for ordered, changeable sequences; **tuples** for ordered, unchangeable records; **dicts** for keyed lookups; and **sets** for unordered collections of unique items. Each has a clear purpose, and knowing which to reach for is a core Python skill.

In this lesson, you will learn the syntax and key methods of each type, how to slice and search them, and the elegant **list comprehension** feature that replaces many small loops with a single readable line. By the end, you will be able to model almost any real-world data — a user, a shopping cart, a list of unique tags — with the right Python collection.

---

## Learning Objectives

After this lesson, you will be able to:

1. Create, index, slice, and modify lists using their common methods.
2. Explain when tuples are preferable to lists and use them for fixed records.
3. Build and query dictionaries, including `.get()`, `.keys()`, `.values()`, and `.items()`.
4. Use sets for uniqueness and set operations, and write list comprehensions with filters.

---

## 1. Lists

A **list** is an ordered, mutable collection of values. "Ordered" means the items appear in the order you put them in. "Mutable" means you can change the list after creation — add, remove, or replace items. Lists are the most-used collection in Python and the go-to choice whenever you need a sequence.

```python
fruits = ["apple", "banana", "cherry"]
print(fruits[0])           # apple
print(fruits[-1])          # cherry (negative index = from end)
print(len(fruits))         # 3
```

You create a list with square brackets `[]`, separating items with commas. Indexing is **0-based** — the first item is `fruits[0]`, the second is `fruits[1]`, and so on. Python also supports **negative indexing**: `fruits[-1]` is the last item, `fruits[-2]` the second-to-last, etc. This is a convenient shortcut for "the last item" without needing to know the length.

### Slicing

You can extract a sub-list with **slice syntax** `[start:stop]`. The `stop` index is exclusive — the same half-open convention as `range()`.

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]
print(fruits[1:3])         # ['banana', 'cherry']
print(fruits[:2])          # ['apple', 'banana']  — start defaults to 0
print(fruits[3:])          # ['date', 'elderberry']  — stop defaults to end
print(fruits[::2])         # ['apple', 'cherry', 'elderberry']  — step by 2
print(fruits[::-1])        # ['elderberry', 'date', 'cherry', 'banana', 'apple'] — reverse
```

The full form is `[start:stop:step]`. Omitting `start` defaults to 0, omitting `stop` defaults to the end, and omitting `step` defaults to 1. The trick `fruits[::-1]` (step of -1) reverses a list — it is one of the most-used Python idioms.

### Common list methods

Lists have many built-in methods. The ones you will use most:

```python
fruits = ["apple", "banana"]

fruits.append("cherry")          # add to end
print(fruits)                     # ['apple', 'banana', 'cherry']

fruits.insert(1, "apricot")      # insert at index 1
print(fruits)                     # ['apple', 'apricot', 'banana', 'cherry']

last = fruits.pop()               # remove and return last
print(last)                       # cherry
print(fruits)                     # ['apple', 'apricot', 'banana']

fruits.remove("apricot")          # remove first matching value
print(fruits)                     # ['apple', 'banana']

fruits.sort()                     # sort in place (alphabetical)
print(fruits)                     # ['apple', 'banana']

numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort()
print(numbers)                    # [1, 1, 2, 3, 4, 5, 6, 9]
print(sorted(numbers, reverse=True))  # [9, 6, 5, 4, 3, 2, 1, 1] — sorted() returns new list
```

Note that `.sort()` sorts the list **in place** (modifies the original) and returns `None`, while the built-in `sorted()` function returns a **new** sorted list and leaves the original alone. This is a common interview question — make sure you understand the difference.

---

## 2. Tuples

A **tuple** is like a list, but **immutable** — once created, you cannot add, remove, or change items. Tuples use parentheses `()` instead of square brackets `[]`.

```python
point = (3, 4)
print(point[0])            # 3
print(point[1])            # 4
print(len(point))          # 2

# point[0] = 5             # ← TypeError: 'tuple' object does not support item assignment
```

The syntax looks like a list, but Python treats it as a fixed record. Indexing and slicing work the same way. The difference shows up when you try to modify: `point[0] = 5` raises `TypeError` because tuples are immutable.

### Why use tuples?

Three reasons. **First, safety:** if you have a value that should never change (the dimensions of a screen, the RGB color of a pixel, the x/y of a 2D point), a tuple signals "this is fixed" — you cannot accidentally mutate it later. **Second, dictionary keys:** tuples can be used as dict keys (because they are immutable and hashable), but lists cannot. **Third, multiple return values:** when a function returns two or three values, Python packs them into a tuple — you saw this in Lesson 05 with `return min(numbers), max(numbers)`.

```python
# Tuple unpacking
point = (3, 4)
x, y = point
print(x, y)                # 3 4

# Tuple as a dict key
distances = {("Hanoi", "Halong"): 150, ("Hanoi", "Sapa"): 320}
print(distances[("Hanoi", "Halong")])  # 150
```

A common gotcha: a one-element tuple needs a trailing comma — `(5,)` is a tuple, but `(5)` is just the integer `5` in parentheses. The empty tuple is `()`. Also, tuples (and other "hashable" types) can be set elements and dict keys; lists cannot.

---

## 3. Dictionaries

A **dictionary** (or `dict`) maps **keys** to **values**. Where a list stores items in order by position, a dict stores items by name. Dicts are the second most-used collection in Python after lists, and they are the natural way to model any "object with named fields" — a user, a product, a configuration.

```python
person = {
    "name": "Hieu",
    "age": 25,
    "city": "Hanoi"
}
print(person["name"])          # Hieu
print(person["age"])            # 25
```

You create a dict with curly braces `{}`, separating keys and values with a colon. You look up a value by writing `dict[key]`. If the key does not exist, you get a `KeyError`. To avoid that, use the `.get()` method, which returns `None` (or a default you provide) instead of raising:

```python
print(person.get("email"))              # None — no error
print(person.get("email", "N/A"))       # N/A — your default
print(person.get("name", "Anonymous"))  # Hieu — key exists, default ignored
```

### Adding and updating keys

```python
person["email"] = "hieu@example.com"    # adds new key
person["age"] = 26                       # updates existing key
print(person)
# {'name': 'Hieu', 'age': 26, 'city': 'Hanoi', 'email': 'hieu@example.com'}
```

Assignment `person[key] = value` works whether or not the key already exists — if it does, the value is replaced; if it does not, a new key-value pair is added. This is the standard way to build up a dict dynamically.

### Iterating a dict

Three methods give you different views of the dict:

```python
for key in person.keys():
    print(key)                  # name, age, city, email

for value in person.values():
    print(value)                # Hieu, 26, Hanoi, hieu@example.com

for key, value in person.items():
    print(f"{key}: {value}")
# name: Hieu
# age: 26
# city: Hanoi
# email: hieu@example.com
```

The most useful is `.items()` — it yields `(key, value)` tuples you can unpack right in the `for` line. Use it whenever you need both pieces. As of Python 3.7+, dicts **preserve insertion order** — keys come out in the same order you added them. This was an implementation detail in 3.6 and a language guarantee from 3.7 onward.

---

## 4. Sets

A **set** is an unordered collection of **unique** items. Sets are useful for two things: removing duplicates from a list, and fast membership testing (`x in my_set` is much faster than `x in my_list` for large collections — sets use a hash table, lists scan linearly).

```python
unique = {1, 2, 3, 2, 1}
print(unique)              # {1, 2, 3} — duplicates dropped

print(2 in unique)         # True — fast O(1) check
print(5 in unique)         # False

unique.add(4)
unique.add(3)              # already there, no effect
print(unique)              # {1, 2, 3, 4}

unique.remove(2)
print(unique)              # {1, 3, 4}
```

Notice that the duplicates `{1, 2, 3, 2, 1}` were automatically collapsed to `{1, 2, 3}` — sets never contain duplicates. Order is not preserved (sets are unordered), so you cannot index into a set like `unique[0]`. If you need to remove duplicates while preserving order, use `dict.fromkeys(my_list)` instead — a Python idiom that exploits dict ordering.

### Set operations

Sets support mathematical set operations with operators:

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

print(a | b)              # {1, 2, 3, 4, 5, 6}  — union (in either)
print(a & b)              # {3, 4}              — intersection (in both)
print(a - b)              # {1, 2}              — difference (in a but not b)
print(a ^ b)              # {1, 2, 5, 6}        — symmetric difference (in exactly one)
```

These are surprisingly useful in real code. "Find users who are in group A but not group B" is `a - b`. "Find tags shared by two articles" is `a & b`. Once you start using sets, you will find yourself reaching for them constantly for membership and deduplication tasks.

---

## 5. List Comprehensions

A **list comprehension** is a compact, readable way to build a list by transforming or filtering another collection. It replaces the common pattern of "create an empty list, loop, append" with a single expression.

```python
# Long way
squares = []
for x in range(10):
    squares.append(x * x)
print(squares)             # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# List comprehension — same result in one line
squares = [x * x for x in range(10)]
print(squares)             # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

The syntax is `[expression for item in collection]`. Read it left to right: "build a list of `expression` for each `item` in `collection`." The expression can be anything — arithmetic, function calls, even another comprehension. Once you internalize the pattern, you will find it reads more naturally than the equivalent loop.

### Adding a filter

You can add an `if` clause at the end to filter:

```python
numbers = [1, -2, 3, -4, 5, -6]

positives = [x for x in numbers if x > 0]
print(positives)           # [1, 3, 5]

evens = [x for x in range(20) if x % 2 == 0]
print(evens)               # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Transform and filter together
doubled_positives = [x * 2 for x in numbers if x > 0]
print(doubled_positives)   # [2, 6, 10]
```

The full form is `[expression for item in collection if condition]`. The `if` goes **after** the `for` when it is a filter (keep only items where the condition is true). There is also a ternary form `[a if cond else b for x in xs]` for choosing between two expressions — useful but quickly unreadable, so use sparingly.

### Dict and set comprehensions

The same syntax works for dicts and sets — just use curly braces and a `key: value` expression for dicts:

```python
# Dict comprehension
squares_dict = {x: x * x for x in range(5)}
print(squares_dict)        # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Set comprehension
unique_lengths = {len(word) for word in ["hi", "hello", "hey", "world"]}
print(unique_lengths)       # {2, 3, 5}
```

Comprehensions are one of Python's most-loved features. They are concise, fast, and read clearly. The only rule: do not nest them too deeply. A two-level comprehension like `[x*y for x in a for y in b]` is fine; a three-level comprehension is unreadable and should be a regular loop.

---

## 6. When to Use Each

| Collection | Syntax | Mutable? | Ordered? | Duplicates? | Use When |
|------------|--------|-----------|-----------|--------------|-----------|
| List  | `[1, 2, 3]`        | Yes | Yes (insertion order) | Yes | You need an ordered sequence you can change |
| Tuple | `(1, 2, 3)`        | No  | Yes (insertion order) | Yes | You need a fixed record (coordinates, RGB, return values) |
| Dict  | `{"a": 1, "b": 2}` | Yes | Yes (insertion, 3.7+) | Keys unique | You need to look up values by a key/name |
| Set   | `{1, 2, 3}`        | Yes | No | No (unique only) | You need uniqueness or fast membership tests |

Mental shortcut: if the data is **ordered and mutable**, use a list. If it is **ordered and fixed**, use a tuple. If it has **named fields / keyed lookups**, use a dict. If it needs **uniqueness**, use a set. When in doubt, start with a list — it is the most flexible and the most common choice in everyday Python.

---

## Try It Yourself

### Exercise 1: Shopping Cart

Create a list called `cart` with three items: `"apple"`, `"banana"`, `"milk"`. Use `.append()` to add `"bread"`. Use `.insert(0, "eggs")` to put eggs at the front. Use `.pop()` to remove and print the last item. Use `len(cart)` to print the final size. The final cart should be `['eggs', 'apple', 'banana', 'milk']` with size 4.

### Exercise 2: Phone Book

Create a dict called `phone_book` with three entries: `"Hieu": "0901234567"`, `"Alice": "0912345678"`, `"Bob": "0923456789"`. Use `.get()` to look up Alice's number with a default of `"N/A"`. Look up a name that does not exist (like `"Carol"`) using `.get()` with the same default, and confirm you get `"N/A"` instead of an error. Then use a `for` loop with `.items()` to print every name and number on its own line.

### Exercise 3: Unique Words

Take this list of words: `["apple", "banana", "apple", "cherry", "banana", "date"]`. Use a list comprehension to convert every word to uppercase. Then convert the list to a set to remove duplicates, and print both results. Finally, write a one-line dict comprehension that maps each word to its length, e.g. `{"apple": 5, "banana": 6, ...}`. Confirm the duplicates are collapsed in the dict keys.

---

## Common Mistakes

### Mistake 1: Using `[]` instead of `()` for a tuple

```python
# WRONG — this is a list, not a tuple (mutable, cannot be a dict key)
point = [3, 4]
locations = {point: "origin"}
# TypeError: unhashable type: 'list'
```

```python
# RIGHT — tuples use parentheses and can be dict keys
point = (3, 4)
locations = {point: "origin"}
print(locations[(3, 4)])   # origin
```

Lists and tuples look similar but behave differently. Lists are mutable, so they cannot be hashed — Python refuses to use them as dict keys or set members. If you need a fixed record that you can use as a key, use a tuple. If you need to grow the collection later, use a list.

### Mistake 2: Using a list as a dict key

```python
# WRONG — list is unhashable
scores = {["Hieu", "math"]: 90}
# TypeError: unhashable type: 'list'
```

```python
# RIGHT — convert to tuple
scores = {("Hieu", "math"): 90}
print(scores[("Hieu", "math")])   # 90
```

A dict key must be **hashable** — meaning Python can compute a stable hash for it. Lists, dicts, and sets are mutable, so their hash could change at any time, which would break the dict. Tuples, strings, numbers, and frozensets are immutable and hashable. If you need a multi-part key, use a tuple of the parts.

### Mistake 3: Modifying a list while iterating over it

```python
# WRONG — removing items while iterating causes skipped items
numbers = [1, 2, 3, 4, 5]
for n in numbers:
    if n % 2 == 0:
        numbers.remove(n)
print(numbers)             # [1, 3, 5] — looks right but only by luck; try a longer list
```

```python
# RIGHT — build a new list with a comprehension
numbers = [1, 2, 3, 4, 5]
odds = [n for n in numbers if n % 2 != 0]
print(odds)                # [1, 3, 5]
```

Modifying a list while you are iterating over it causes the loop to skip items because the indices shift underneath you. The fix is to build a new list (with a comprehension) instead of mutating the original. If you absolutely must mutate in place, iterate over a copy: `for n in numbers[:]:` — the slice `[:]` creates a shallow copy that does not change when you modify `numbers`.

### Mistake 4: Off-by-one in slicing

```python
fruits = ["apple", "banana", "cherry", "date"]

# WRONG — wanted "banana" and "cherry", got only "banana"
print(fruits[1:2])         # ['banana']

# RIGHT — stop is exclusive, so add 1
print(fruits[1:3])         # ['banana', 'cherry']
```

Slice syntax `[start:stop]` includes `start` but excludes `stop`, following the half-open interval convention. If you want items at indices 1 and 2, you need `[1:3]`, not `[1:2]`. The rule of thumb: the number of items in `a[start:stop]` is `stop - start`. Memorize this and slicing becomes predictable.

---

## Summary

- Lists (`[]`) are ordered, mutable, allow duplicates — the everyday sequence.
- Tuples (`()`) are ordered, immutable, allow duplicates — fixed records and dict keys.
- Dicts (`{k: v}`) map keys to values, preserve insertion order since Python 3.7.
- Sets (`{v}`) are unordered, mutable, no duplicates — use for uniqueness and fast `in`.
- `.get(key, default)` returns a default instead of raising `KeyError`.
- `for k, v in d.items():` iterates a dict's key-value pairs.
- List comprehensions `[expr for x in xs if cond]` replace many small loops.
- Choose the right collection by asking: ordered? mutable? keyed? unique?

You now know all four core collections. In Lesson 07, we will pull the entire module together into a single printable **cheatsheet** you can keep open while you code. It is the capstone of the module and the page you will reference most often.

---

**Next:** [Lesson 07: Python Cheatsheet →](./07-python-cheatsheet.md)
