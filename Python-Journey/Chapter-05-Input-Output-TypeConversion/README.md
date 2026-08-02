# 🐍 Python Journey – Chapter 5
## Input, Output & Type Conversion (FAANG Level)

> 🎯 **Goal:** Master Python's I/O system deeply — understand *why* `input()` always returns a string, *how* `print()` really works, and *when* type conversion can silently break your ETL pipelines.

---

## 📋 Learning Objectives

By the end of this chapter, you will understand:

- ✅ How `input()` works **internally** at the OS level
- ✅ Why `input()` **always** returns a string (not a number)
- ✅ **Explicit vs Implicit** type conversion — with edge cases
- ✅ The `print()` function in depth — `sep`, `end`, `file`, `flush`
- ✅ **f-strings** — the modern, recommended way to format strings
- ✅ `str.format()` and `%` formatting (legacy)
- ✅ Input validation with `try/except`
- ✅ Reading multiple inputs in one line
- ✅ Real Data Engineering type conversion scenarios
- ✅ FAANG Interview Q&A

---

## 📌 1. Input in Python

The `input()` function **pauses execution**, displays a prompt, and reads a line of text entered by the user from **stdin** (standard input).

```python
name = input("Enter your name: ")
print(name)
```

**Example:**
```
Enter your name: Swapnil
Swapnil
```

**Internally, what happens:**

```
 Your Keyboard
      │
      ▼ (key presses → characters)
 Operating System Buffer
      │
      ▼
 input() reads until Enter key (\n)
      │
      ▼ (strips the \n)
 Returns a str object
      │
      ▼
 name = "Swapnil"
```

---

## 📌 2. What Does `input()` Return?

> This is a **very common FAANG interview question.**

```python
age = input("Enter age: ")

print(age)
print(type(age))
```

**User types:** `25`

**Output:**
```
25
<class 'str'>
```

> Even though you typed a **number**, Python returns a **string**.

---

## 📌 3. Why Does `input()` Always Return a String?

The keyboard sends **characters**, not integers or floats.

When you type `25`, the operating system sends:

```
'2'  →  character (Unicode code point 50)
'5'  →  character (Unicode code point 53)
'\n' →  Enter key
```

Python reads these characters and combines them into the string `"25"`.

**Python cannot know the intended type because `"25"` could be:**

| Intent | Type | Example |
|--------|------|---------|
| An age | `int` | `25` |
| A roll number | `str` | `"25"` |
| A PIN | `str` (no math needed) | `"0025"` |
| A salary | `float` | `25.00` |
| An employee ID | `str` | `"EMP25"` |

> Python safely returns a `str` and lets **you** decide the type.

---

## 📌 4. Converting Input to the Right Type

### Integer Input

```python
age = int(input("Enter age: "))

print(age)
print(type(age))
```

**User types:** `25`

**Output:**
```
25
<class 'int'>
```

**Memory flow:**

```
input()  →  "25"  (str)
              │
          int("25")
              │
              ▼
             25   (int object)
              │
           age = 25
```

---

### Float Input

```python
salary = float(input("Enter salary: "))
print(salary)
print(type(salary))
```

**User types:** `75000.50`

**Output:**
```
75000.5
<class 'float'>
```

---

### String Input (default — no conversion needed)

```python
name = input("Enter name: ")
print(name)
print(type(name))
```

---

### Boolean Input (no direct way — must parse manually)

Python has no `bool(input(...))` that works as expected:

```python
# ❌ This doesn't work as expected
flag = bool(input("Enter True/False: "))
# Any non-empty string is truthy — even "False"!
```

**Correct approach:**

```python
value = input("Enter True or False: ")
flag = value.strip().lower() == "true"
print(flag)
print(type(flag))
```

**User types:** `True`
```
True
<class 'bool'>
```

---

## 📌 5. Reading Multiple Inputs in One Line

### Split into separate variables

```python
a, b = input("Enter two numbers separated by space: ").split()
print(a)
print(b)
print(type(a))   # still str!
```

**User types:** `10 20`
```
10
20
<class 'str'>
```

---

### Split + Convert in one line using `map()`

```python
a, b = map(int, input("Enter two numbers: ").split())

print(a + b)
print(type(a))
```

**User types:** `10 20`
```
30
<class 'int'>
```

**How `map()` works here:**

```
input()   →  "10 20"
.split()  →  ["10", "20"]
map(int, ...) → applies int() to each element → [10, 20]
a, b = 10, 20
```

---

### Read a list of integers

```python
numbers = list(map(int, input("Enter numbers: ").split()))
print(numbers)
print(type(numbers))
```

**User types:** `1 2 3 4 5`
```
[1, 2, 3, 4, 5]
<class 'list'>
```

---

## 📌 6. The `print()` Function — In Depth

### Signature

```python
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```

### Basic Usage

```python
print("Hello, World!")
```

---

### Multiple Values

```python
name = "Swapnil"
age = 22

print(name, age)   # Swapnil 22
```

Python automatically calls `str()` on each argument.

---

### `sep` Parameter — Custom Separator

Default separator between values is a **space**.

```python
print(10, 20, 30)           # 10 20 30
print(10, 20, 30, sep="-")  # 10-20-30
print(10, 20, 30, sep=", ") # 10, 20, 30
print(10, 20, 30, sep="")   # 102030
```

**Real-world use:**

```python
# Print a date formatted
print(2026, 8, 2, sep="-")   # 2026-8-2
```

---

### `end` Parameter — Custom Line Ending

Default end character is `\n` (newline).

```python
print("Hello")
print("World")
```
```
Hello
World
```

With `end`:

```python
print("Hello", end=" ")
print("World")
```
```
Hello World
```

**Real-world use — printing a progress bar:**

```python
import time

for i in range(5):
    print(f"Processing {i+1}/5...", end="\r")
    time.sleep(0.5)
```

---

### `file` Parameter — Print to a File

```python
with open("output.txt", "w") as f:
    print("Logging data...", file=f)
    print("Pipeline complete.", file=f)
```

---

### `flush` Parameter — Force Immediate Output

```python
import time

print("Starting...", end="", flush=True)
time.sleep(2)
print(" Done!")
```

> `flush=True` ensures output appears immediately — useful in pipelines and logging.

---

## 📌 7. String Formatting Methods

### ✅ Method 1 — f-strings (Recommended — Python 3.6+)

```python
name = "Swapnil"
age = 22
salary = 75000.50

print(f"My name is {name} and I am {age} years old.")
print(f"Salary: ₹{salary:,.2f}")
```

**Output:**
```
My name is Swapnil and I am 22 years old.
Salary: ₹75,000.50
```

**Expressions inside f-strings:**

```python
a = 10
b = 20

print(f"Sum of {a} + {b} = {a + b}")
print(f"Type of a: {type(a).__name__}")
print(f"Uppercase: {'swapnil'.upper()}")
```

**Output:**
```
Sum of 10 + 20 = 30
Type of a: int
Uppercase: SWAPNIL
```

**Format specifiers:**

```python
pi = 3.14159265

print(f"{pi:.2f}")      # 3.14       — 2 decimal places
print(f"{pi:.4f}")      # 3.1416     — 4 decimal places
print(f"{1000000:,}")   # 1,000,000  — thousand separator
print(f"{'Python':>10}") #     Python — right-align in 10 chars
print(f"{'Python':<10}") # Python     — left-align in 10 chars
print(f"{'Python':^10}") #   Python   — center in 10 chars
print(f"{42:05d}")       # 00042      — zero-padded
```

**Why f-strings are preferred:**

```
✅ Most readable
✅ Fastest at runtime (compiled at parse time)
✅ Supports expressions, not just variables
✅ Modern Python standard (3.6+)
✅ Easier to debug
```

---

### ✅ Method 2 — `str.format()`

```python
name = "Swapnil"
age = 22

print("My name is {} and I am {}.".format(name, age))
```

**Named placeholders:**

```python
print("Name: {n}, Age: {a}".format(n=name, a=age))
```

**Index-based:**

```python
print("{0} is {1} years old. {0} loves Python.".format(name, age))
# Swapnil is 22 years old. Swapnil loves Python.
```

**Format specifiers:**

```python
print("{:.2f}".format(3.14159))   # 3.14
print("{:,}".format(1000000))     # 1,000,000
```

---

### ⚠️ Method 3 — `%` Formatting (Legacy)

```python
name = "Swapnil"
age = 22

print("Hello %s, you are %d years old." % (name, age))
```

**Common format codes:**

| Code | Type |
|------|------|
| `%s` | String |
| `%d` | Integer |
| `%f` | Float |
| `%.2f` | Float with 2 decimal places |

> ⚠️ Still seen in older codebases and C-extension code. **Use f-strings in new code.**

---

## 📌 8. Type Conversion — Deep Dive

### Explicit Conversion (Type Casting)

```python
# str → int
print(int("100"))         # 100

# str → float
print(float("12.5"))      # 12.5

# int → str
print(str(100))           # "100"

# str → list of characters
print(list("Python"))     # ['P', 'y', 't', 'h', 'o', 'n']

# list → tuple
print(tuple([1, 2, 3]))   # (1, 2, 3)

# list → set (removes duplicates)
print(set([1, 2, 2, 3]))  # {1, 2, 3}

# bool conversions
print(int(True))          # 1
print(int(False))         # 0
print(bool(0))            # False
print(bool(1))            # True
print(bool(""))           # False
print(bool("Python"))     # True
print(bool([]))           # False
print(bool([1, 2]))       # True
```

---

### Implicit Conversion (Automatic)

Python automatically converts types when it's safe and lossless:

```python
x = 10        # int
y = 5.5       # float

result = x + y
print(result)         # 15.5
print(type(result))   # <class 'float'>
```

**Promotion hierarchy:**

```
bool  →  int  →  float  →  complex

True + 1        = 2       (bool → int)
10 + 5.5        = 15.5    (int → float)
5 + (2+3j)      = (7+3j)  (int → complex)
```

---

### ❌ Invalid Conversions — What Fails

```python
int("Hello")      # ❌ ValueError — not a valid integer
int("10.5")       # ❌ ValueError — float string can't go directly to int
int([1, 2])       # ❌ TypeError  — list can't be converted to int
float("abc")      # ❌ ValueError
```

**Fix for `int("10.5")`:**

```python
int(float("10.5"))   # ✅ → 10  (truncates, does not round)
round(float("10.5")) # ✅ → 10 or 11 (rounds)
```

---

### ⚠️ Subtle Difference — `int("10")` vs `int(10.9)`

```python
print(int("10"))    # 10  — parses the string "10"
print(int(10.9))    # 10  — TRUNCATES (not rounds) the decimal
print(int(10.1))    # 10  — also truncates
print(int(-10.9))   # -10 — truncates toward zero
```

> ⚠️ **`int()` always truncates — never rounds.** Use `round()` if you need rounding.

```python
print(round(10.9))  # 11
print(round(10.1))  # 10
```

---

## 📌 9. Input Validation

Never assume users will enter valid data. Always handle errors gracefully in production.

### Basic Validation with `try/except`

```python
try:
    age = int(input("Enter age: "))
    print(f"Your age is {age}")
except ValueError:
    print("Invalid input. Please enter a valid integer.")
```

---

### Loop Until Valid Input

```python
while True:
    try:
        age = int(input("Enter age (1–120): "))
        if 1 <= age <= 120:
            print(f"Valid age: {age}")
            break
        else:
            print("Age must be between 1 and 120.")
    except ValueError:
        print("Please enter a valid integer.")
```

**Output (with bad inputs):**
```
Enter age (1–120): hello
Please enter a valid integer.
Enter age (1–120): 200
Age must be between 1 and 120.
Enter age (1–120): 25
Valid age: 25
```

---

### Validate Float Input

```python
while True:
    try:
        salary = float(input("Enter salary: "))
        if salary >= 0:
            print(f"Salary: ₹{salary:,.2f}")
            break
        else:
            print("Salary cannot be negative.")
    except ValueError:
        print("Invalid input. Enter a numeric value.")
```

---

## 📌 10. Real-World Data Engineering Example

### Problem: CSV data with salary as string

Imagine you're building an ETL pipeline at Amazon:

```
employees.csv:
id, name,     salary
1,  Swapnil,  75000
2,  Raj,      90000
3,  Priya,    65000
```

```python
import pandas as pd

df = pd.read_csv("employees.csv")

print(df.dtypes)
```

**Output:**
```
id        int64
name      object    ← string
salary    object    ← read as string! ⚠️
```

**Wrong result without conversion:**

```python
total = df["salary"].sum()   # "7500090000650000" — string concatenation!
```

**Correct — explicit conversion:**

```python
df["salary"] = pd.to_numeric(df["salary"], errors="coerce")
# errors="coerce" → invalid values become NaN instead of crashing

total = df["salary"].sum()   # ✅ 230000.0
avg   = df["salary"].mean()  # ✅ 76666.67
```

> 🏗️ **FAANG Lesson:** In production pipelines handling millions of rows, wrong types = wrong aggregations = wrong business reports. Always validate and convert types at the ingestion layer.

---

## 🖥️ Practice Programs

### ✅ Program 1 — Read name and age, print formatted output

```python
name = input("Enter your name: ")
age  = int(input("Enter your age: "))

print(f"\nHello, {name}!")
print(f"You are {age} years old.")
print(f"In 10 years, you will be {age + 10}.")
```

**Output:**
```
Enter your name: Swapnil
Enter your age: 22

Hello, Swapnil!
You are 22 years old.
In 10 years, you will be 32.
```

---

### ✅ Program 2 — Read two numbers, print all operations

```python
a, b = map(float, input("Enter two numbers separated by space: ").split())

print(f"\na = {a}, b = {b}")
print(f"Addition       : {a + b}")
print(f"Subtraction    : {a - b}")
print(f"Multiplication : {a * b}")

if b != 0:
    print(f"Division       : {a / b:.4f}")
else:
    print("Division       : undefined (b = 0)")
```

**Output:**
```
Enter two numbers separated by space: 10 3

a = 10.0, b = 3.0
Addition       : 13.0
Subtraction    : 7.0
Multiplication : 30.0
Division       : 3.3333
```

---

### ✅ Program 3 — Demonstrate all formatting methods

```python
name   = "Swapnil"
age    = 22
salary = 75000.555

print("=== f-string ===")
print(f"Name: {name}, Age: {age}, Salary: ₹{salary:,.2f}")

print("\n=== str.format() ===")
print("Name: {}, Age: {}, Salary: ₹{:,.2f}".format(name, age, salary))

print("\n=== % formatting ===")
print("Name: %s, Age: %d, Salary: ₹%.2f" % (name, age, salary))
```

**Output:**
```
=== f-string ===
Name: Swapnil, Age: 22, Salary: ₹75,000.56

=== str.format() ===
Name: Swapnil, Age: 22, Salary: ₹75,000.56

=== % formatting ===
Name: Swapnil, Age: 22, Salary: ₹75000.56
```

---

### ✅ Program 4 — Show all `print()` parameters

```python
# sep
print("2026", "08", "02", sep="-")      # 2026-08-02
print("Data", "Engineer", sep=" | ")    # Data | Engineer

# end
print("Loading", end="...")
print(" Done!")                          # Loading... Done!

# Multiple sep examples
print(*range(1, 6), sep=" → ")          # 1 → 2 → 3 → 4 → 5
```

**Output:**
```
2026-08-02
Data | Engineer
Loading... Done!
1 → 2 → 3 → 4 → 5
```

---

### ✅ Program 5 — Type conversion showcase

```python
print("=== Explicit Conversion ===")
print(int("100"))             # 100
print(float("3.14"))          # 3.14
print(str(2026))              # "2026"
print(list("FAANG"))          # ['F', 'A', 'A', 'N', 'G']
print(tuple([1, 2, 3]))       # (1, 2, 3)
print(set([1, 2, 2, 3, 3]))   # {1, 2, 3}
print(bool(""))               # False
print(bool("Python"))         # True

print("\n=== Implicit Conversion ===")
print(10 + 5.5)               # 15.5
print(True + True + False)    # 2
print(type(10 + 5.5))         # <class 'float'>

print("\n=== Truncation vs Rounding ===")
print(int(10.9))              # 10 — truncates!
print(round(10.9))            # 11 — rounds!
print(int(-10.9))             # -10 — truncates toward zero
```

---

### ✅ Program 6 — Input validation loop

```python
while True:
    try:
        marks = int(input("Enter marks (0–100): "))
        if 0 <= marks <= 100:
            if marks >= 90:
                grade = "A+"
            elif marks >= 80:
                grade = "A"
            elif marks >= 70:
                grade = "B"
            elif marks >= 60:
                grade = "C"
            else:
                grade = "F"
            print(f"Marks: {marks} | Grade: {grade}")
            break
        else:
            print("Marks must be between 0 and 100.")
    except ValueError:
        print("Invalid input. Please enter an integer.")
```

---

## 🎯 FAANG Interview Questions & Answers

### Q1. Why does `input()` always return a string?

> **Answer:** `input()` reads from **stdin** (keyboard), which is a text stream. The OS sends keystrokes as **characters** (Unicode code points), not typed numbers or booleans. Python receives these characters as a string and returns them as-is. It cannot infer the intended type — the same characters `"25"` could represent an age, a PIN, a roll number, or any other concept. The programmer must explicitly convert: `int(input(...))`, `float(input(...))`, etc.

---

### Q2. What is the difference between implicit and explicit type conversion?

> **Answer:**
> - **Implicit conversion** (coercion): Python automatically converts types when safe and lossless — e.g., `int + float` → `float` automatically.
> - **Explicit conversion** (casting): The programmer manually converts using built-in functions: `int()`, `float()`, `str()`, `list()`, `set()`, etc.
>
> ```python
> # Implicit
> result = 10 + 5.5    # Python converts 10 → 10.0 automatically
>
> # Explicit
> age = int(input("Age: "))   # programmer decides to convert
> ```

---

### Q3. Why are f-strings preferred over other formatting methods?

> **Answer:**
> 1. **Most readable** — expressions are inline with the string
> 2. **Fastest** — evaluated at parse time (no runtime overhead of `.format()`)
> 3. **Supports expressions** — `f"{a + b}"`, `f"{'hello'.upper()}"` etc.
> 4. **Modern standard** — introduced in Python 3.6, now the recommended way
> 5. **Less error-prone** — no index/count mismatches like `%s %d` formatting

---

### Q4. What is the difference between `int("10")` and `int(10.9)`?

> **Answer:**
> - `int("10")` **parses** the string `"10"` and returns the integer `10`.
> - `int(10.9)` **truncates** the float toward zero and returns `10`. It does NOT round — `int(10.9)` and `int(10.1)` both return `10`.
>
> ```python
> print(int("10"))    # 10
> print(int(10.9))    # 10  (truncated, not rounded)
> print(round(10.9))  # 11  (rounded)
> print(int(-10.9))   # -10 (truncated toward zero)
> ```

---

### Q5. What happens if you call `int("10.5")`?

> **Answer:** It raises a `ValueError` because `"10.5"` is not a valid **integer literal** — it contains a decimal point. `int()` can only parse strings that represent whole integers like `"10"`, `"-5"`, `"+100"`.
>
> **Correct approach:**
> ```python
> int(float("10.5"))   # ✅ → 10  (converts to float first, then truncates)
> ```

---

### Q6. How do you read a list of integers from a single line of input?

> **Answer:**
> ```python
> numbers = list(map(int, input("Enter numbers: ").split()))
> ```
> - `input()` reads the line as a string: `"1 2 3 4 5"`
> - `.split()` splits by whitespace: `["1", "2", "3", "4", "5"]`
> - `map(int, ...)` applies `int()` to each: `[1, 2, 3, 4, 5]`
> - `list(...)` materializes the map object into a list

---

### Q7. What is `errors="coerce"` in `pd.to_numeric()`?

> **Answer:** In Pandas, `pd.to_numeric(series, errors="coerce")` attempts to convert each value to a numeric type. If a value cannot be converted (e.g., `"N/A"`, `"hello"`), instead of raising an error, it replaces that value with `NaN`. This is critical in data engineering pipelines where real-world data often contains dirty, inconsistent values.
>
> ```python
> import pandas as pd
> s = pd.Series(["100", "200", "N/A", "abc"])
> print(pd.to_numeric(s, errors="coerce"))
> # 0    100.0
> # 1    200.0
> # 2      NaN
> # 3      NaN
> ```

---

## 📝 Chapter Summary

```
┌──────────────────────────────────────────────────────────────────┐
│                     CHAPTER 5 SUMMARY                            │
├──────────────────────────────────────────────────────────────────┤
│ input()       → Always returns str — reads from stdin            │
│ Why str?      → OS sends characters; Python can't infer type     │
│ Convert       → int(), float(), bool() — explicit casting        │
│ Multiple in   → map(int, input().split())                        │
│ print()       → sep, end, file, flush parameters                 │
│ f-string      → f"{var}" — fastest, most readable (3.6+)        │
│ .format()     → "{}".format(var) — older but widely used        │
│ %             → "%s" % var — legacy, avoid in new code           │
│ Implicit conv → bool→int→float→complex (auto promotion)         │
│ Explicit conv → int(), float(), str(), list(), set(), tuple()   │
│ int("10.5")   → ValueError — use int(float("10.5"))             │
│ int(10.9)     → 10 — truncates, does NOT round                  │
│ Validation    → try/except ValueError around int(input(...))     │
│ DE lesson     → pd.to_numeric(errors="coerce") for dirty data   │
└──────────────────────────────────────────────────────────────────┘
```

---

## ⬅️ Navigation

| | |
|---|---|
| ⬅️ Previous | [Chapter 3 — Mutable vs Immutable Objects](../Chapter-03-Mutable-vs-Immutable/README.md) |
| 🏠 Home | [Python Journey](../README.md) |
| ➡️ Next | [Chapter 6 — Operators in Python](../Chapter-06-Operators/README.md) |
