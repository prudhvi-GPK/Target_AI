# Python Functions, Arguments, Lambda & Comprehensions
*A 1-hour focused study guide — designed to read, run, and commit to GitHub.*

---

## ⏱️ Suggested 1-Hour Plan

| Time | Topic |
|------|-------|
| 0:00 – 0:15 | Functions basics + all argument types |
| 0:15 – 0:25 | `*args` and `**kwargs` deep dive |
| 0:25 – 0:35 | Lambda functions |
| 0:35 – 0:55 | Comprehensions (list, dict, set, generator) |
| 0:55 – 1:00 | Quick recap + practice checklist |

---

## 1. Functions Basics

```python
def greet(name):
    """Return a greeting string."""
    return f"Hello, {name}!"

print(greet("Alice"))  # Hello, Alice!
```

Key points:
- `def` defines a function; `return` sends a value back (no `return` → returns `None`).
- The triple-quoted string right after `def` is a **docstring** (`func.__doc__`).
- Functions are **objects** — you can assign them, pass them, store them in lists.

```python
def square(x):
    return x * x

ops = [square, str, len]
print(ops[0](5))  # 25
```

---

## 2. Arguments — All the Flavors

### 2.1 Positional Arguments
Passed in order.
```python
def power(base, exponent):
    return base ** exponent

power(2, 3)  # 8
```

### 2.2 Keyword Arguments
Passed by name — order doesn't matter.
```python
power(exponent=3, base=2)  # 8
```

### 2.3 Default Arguments
```python
def power(base, exponent=2):
    return base ** exponent

power(5)      # 25  (uses default exponent)
power(5, 3)   # 125
```

⚠️ **Mutable default trap** — never do this:
```python
def add_item(item, basket=[]):   # BAD: shared across calls!
    basket.append(item)
    return basket
```
Fix with `None`:
```python
def add_item(item, basket=None):
    if basket is None:
        basket = []
    basket.append(item)
    return basket
```

### 2.4 Keyword-Only & Positional-Only Arguments
```python
def connect(host, *, port=443, timeout=30):
    # port and timeout MUST be passed as keywords
    ...

connect("example.com", port=8080)  # ok
connect("example.com", 8080)       # TypeError!
```

```python
def divide(a, b, /):
    # a and b MUST be positional (Python 3.8+)
    return a / b
```

### 2.5 Argument Order Rule
```python
def func(pos_only, /, normal, *, kw_only):
    ...
# Order: positional-only → normal → *args → keyword-only → **kwargs
```

---

## 3. `*args` and `**kwargs`

### 3.1 `*args` — variable positional arguments (tuple)
```python
def total(*args):
    return sum(args)

total(1, 2, 3, 4)  # 10
```

### 3.2 `**kwargs` — variable keyword arguments (dict)
```python
def profile(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

profile(name="Bob", age=30)
# name: Bob
# age: 30
```

### 3.3 Combining Everything
```python
def full_example(a, b, *args, c=10, **kwargs):
    print(a, b, args, c, kwargs)

full_example(1, 2, 3, 4, c=99, d=5, e=6)
# 1 2 (3, 4) 99 {'d': 5, 'e': 6}
```

### 3.4 Unpacking When *Calling* a Function
```python
def add(x, y, z):
    return x + y + z

nums = [1, 2, 3]
add(*nums)  # unpacks list into positional args -> 6

info = {"x": 1, "y": 2, "z": 3}
add(**info)  # unpacks dict into keyword args -> 6
```

---

## 4. Lambda Functions

A **lambda** is an anonymous, single-expression function.

```python
square = lambda x: x * x
square(5)  # 25

add = lambda a, b: a + b
add(2, 3)  # 5
```

### When lambdas shine: as short throwaway functions passed to other functions.

```python
# sorting with a custom key
people = [("Alice", 30), ("Bob", 25), ("Carol", 35)]
people.sort(key=lambda person: person[1])
# [('Bob', 25), ('Alice', 30), ('Carol', 35)]

# with map/filter
nums = [1, 2, 3, 4, 5]
squares = list(map(lambda n: n ** 2, nums))       # [1, 4, 9, 16, 25]
evens = list(filter(lambda n: n % 2 == 0, nums))  # [2, 4]
```

### Lambda limitations
- Only ONE expression, no statements (`no `if x: ...`, no loops, no assignments).
- No docstring.
- Prefer a regular `def` if the logic needs a name, multiple lines, or reuse.

```python
# Conditional inside lambda (ternary expression only)
classify = lambda n: "even" if n % 2 == 0 else "odd"
classify(4)  # 'even'
```

---

## 5. Comprehensions

Comprehensions build collections in a single readable line — often faster than manual loops.

### 5.1 List Comprehension
```python
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

evens = [x for x in range(20) if x % 2 == 0]
# [0, 2, 4, ..., 18]

# nested loop
pairs = [(x, y) for x in range(3) for y in range(2)]
# [(0,0),(0,1),(1,0),(1,1),(2,0),(2,1)]

# with if/else (conditional expression, not filter)
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]
# ['even', 'odd', 'even', 'odd', 'even']
```

### 5.2 Dict Comprehension
```python
squares_dict = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# swap keys and values
original = {"a": 1, "b": 2}
swapped = {v: k for k, v in original.items()}
# {1: 'a', 2: 'b'}
```

### 5.3 Set Comprehension
```python
unique_lengths = {len(word) for word in ["cat", "dog", "goat", "ox"]}
# {2, 3, 4}
```

### 5.4 Generator Expression
Like a list comprehension but **lazy** — computes values on demand, saves memory.
```python
gen = (x**2 for x in range(1_000_000))  # no memory used yet
next(gen)  # 0
next(gen)  # 1

total = sum(x**2 for x in range(10))  # generator passed directly to sum()
```

**List vs. generator — when to use which:**
| Use a list comprehension when | Use a generator when |
|---|---|
| You need to reuse/index the data | You only iterate once |
| Dataset is small | Dataset is huge or infinite |
| You need `len()` | Memory efficiency matters |

### 5.5 Comprehensions + Functions/Lambdas Together
```python
words = ["hello", "world", "python"]
lengths = [len(w) for w in words]                 # [5, 5, 6]
upper = list(map(lambda w: w.upper(), words))     # ['HELLO', 'WORLD', 'PYTHON']
long_words = [w for w in words if len(w) > 5]     # ['python']
```

---

## 6. Quick Reference Cheat Sheet

```python
# Function with every argument type
def demo(pos_only, /, normal, *args, kw_only, **kwargs):
    pass

# Lambda
f = lambda x, y=1: x + y

# List comprehension
[expr for item in iterable if condition]

# Dict comprehension
{key_expr: val_expr for item in iterable if condition}

# Set comprehension
{expr for item in iterable if condition}

# Generator expression
(expr for item in iterable if condition)
```

---

## 7. Practice Checklist ✅

- [ ] Write a function using `*args` to find the max of any number of inputs.
- [ ] Write a function using `**kwargs` to build a config dictionary.
- [ ] Sort a list of dicts by a specific key using `lambda`.
- [ ] Convert a `for` loop that builds a list into a list comprehension.
- [ ] Write a dict comprehension that filters out items with `None` values.
- [ ] Write a generator expression and consume it with `next()`.

---

## 8. How to Use This on GitHub

```bash
git init
git add python-functions-lambda-comprehensions.md
git commit -m "Add Python functions/lambda/comprehensions study notes"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

Rename the file to `README.md` if you want it to render as the repo's landing page.
