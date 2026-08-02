# 🐍 Python Journey – Chapter 2
## Variables, Data Types & Python Memory Model (FAANG Level)

> 🎯 **Goal:** Understand not just *how* to use variables — but *what Python actually does in memory* when you write `x = 10`. This depth is exactly what FAANG interviews test.

---

## 📋 Learning Objectives

By the end of this chapter, you will understand:

- ✅ What a variable **actually is** in Python (hint: it's not a box)
- ✅ How Python stores data in memory
- ✅ Why Python has **no explicit type declarations**
- ✅ Object identity, references, and the `id()` function
- ✅ Every basic Python data type with examples
- ✅ Dynamic typing — what it means and how it works
- ✅ Type conversion (implicit and explicit)
- ✅ `is` vs `==` — a classic FAANG interview topic
- ✅ Small integer caching — CPython optimization
- ✅ Practice programs with memory diagrams

---

## 📌 1. What is a Variable?

### 🟢 Beginner Definition

A variable is a **name used to store a value**.

```python
x = 10
```

Here:
- `x` → variable name
- `10` → value

### 🔴 FAANG-Level Definition

> **A variable in Python does NOT store the object itself.**
> It stores a **reference (pointer)** to an object in memory.

```python
x = 10
```

**Memory (conceptually):**

```
 Variable Table          Memory (Heap)
 ┌───┐                  ┌──────────────────┐
 │ x │─────────────────►│  Object: int     │
 └───┘                  │  Value: 10       │
                        │  Type: int       │
                        │  id: 140083...   │
                        └──────────────────┘
```

> Think of a variable as a **label** or **sticky note** attached to an object — not a box that contains the value.

---

## 📌 2. Everything in Python is an Object

This is one of the most important Python fundamentals.

| Thing | Is an Object? |
|-------|-------------|
| Integer `10` | ✅ Yes |
| String `"Python"` | ✅ Yes |
| List `[1, 2, 3]` | ✅ Yes |
| Function `def foo():` | ✅ Yes |
| Class `class Dog:` | ✅ Yes |
| `None` | ✅ Yes |
| `True` / `False` | ✅ Yes |

Every object in Python has three properties:

```
┌──────────────────────────────┐
│         Python Object        │
├──────────────────────────────┤
│  Type     →  int             │
│  Value    →  10              │
│  Identity →  140083475200    │
└──────────────────────────────┘
```

You can inspect all three:

```python
x = 100

print(type(x))   # <class 'int'>   → Type
print(x)         # 100             → Value
print(id(x))     # 140083475200    → Identity (memory address in CPython)
```

---

## 📌 3. Assignment (`=`) Does NOT Copy Data

```python
a = 10
b = a
```

**Memory Diagram:**

```
 ┌───┐
 │ a │───────────┐
 └───┘           │
                 ▼
              ┌──────┐
              │  10  │
              └──────┘
                 ▲
 ┌───┐           │
 │ b │───────────┘
 └───┘
```

Both `a` and `b` point to the **same integer object**.

```python
a = 10
b = a

print(id(a))   # e.g. 140083475200
print(id(b))   # Same! e.g. 140083475200
```

> `=` does **not** copy data. It creates a new reference to the **same object**.

---

## 📌 4. Reassignment

```python
a = 10
b = a
a = 20
```

**Memory after reassignment:**

```
 ┌───┐                ┌──────┐
 │ a │───────────────►│  20  │  (new object)
 └───┘                └──────┘

 ┌───┐                ┌──────┐
 │ b │───────────────►│  10  │  (original object, unchanged)
 └───┘                └──────┘
```

> Reassigning `a` **does not affect `b`**. `b` still points to `10`.
> The original integer `10` is **not modified** — a **new object** `20` is created.

---

## 📌 5. Dynamic Typing

Unlike Java or C++, Python does **not** require type declarations.

**C++ (statically typed):**
```cpp
int age = 25;       // type must be declared
age = "hello";      // ERROR — type mismatch
```

**Python (dynamically typed):**
```python
age = 25            # Python infers int
age = "twenty-five" # Now it's a str — perfectly valid
age = [25, 26, 27]  # Now it's a list — still valid
```

**What's actually happening in memory:**

```
Step 1: age = 25
  age ──► [ int: 25 ]

Step 2: age = "twenty-five"
  age ──► [ str: "twenty-five" ]
         [ int: 25 ] → reference count drops to 0 → garbage collected

Step 3: age = [25, 26, 27]
  age ──► [ list: [25, 26, 27] ]
```

> The **type is attached to the object**, not the variable. The variable is just a name pointing to whatever object you assign.

---

## 📌 6. Basic Data Types

### 🔢 Integer (`int`)

```python
x = 100
population = 1_400_000_000   # underscores for readability
negative = -42
```

- Whole numbers
- No size limit in Python (arbitrary precision)
- `1_000_000` is valid — Python ignores underscores in numbers

---

### 🔵 Float (`float`)

```python
pi = 3.14159
temperature = -7.5
scientific = 1.5e10   # 1.5 × 10^10
```

- Decimal numbers
- 64-bit double precision (IEEE 754)
- Beware of floating point precision:

```python
print(0.1 + 0.2)   # 0.30000000000000004  ← not exactly 0.3!
```

---

### 🔶 Complex (`complex`)

```python
z = 3 + 4j
print(z.real)   # 3.0
print(z.imag)   # 4.0
```

- Used in mathematics, engineering, signal processing
- `j` represents the imaginary unit (√-1)

---

### ✅ Boolean (`bool`)

```python
is_active = True
is_deleted = False
```

- Only two values: `True` and `False`
- `bool` is a **subclass of `int`** in Python

```python
print(True + True)   # 2
print(True * 5)      # 5
print(int(True))     # 1
print(int(False))    # 0
```

---

### 🔤 String (`str`)

```python
name = "Swapnil"
city = 'Pune'
multi = """
This is
a multiline
string
"""
```

- Sequence of Unicode characters
- **Immutable** — cannot be changed after creation
- Supports indexing and slicing:

```python
name = "Swapnil"
print(name[0])     # S
print(name[-1])    # l
print(name[0:4])   # Swap
```

---

### 📋 List (`list`)

```python
marks = [90, 80, 70]
mixed = [1, "Python", True, 3.14]
nested = [[1, 2], [3, 4]]
```

- **Ordered** — elements maintain insertion order
- **Mutable** — can be modified after creation
- **Indexed** — access elements by position

```python
marks = [90, 80, 70]
marks[0] = 95       # ✅ valid — lists are mutable
print(marks)        # [95, 80, 70]
```

---

### 📦 Tuple (`tuple`)

```python
point = (10, 20)
rgb = (255, 128, 0)
single = (42,)      # trailing comma needed for single-element tuple
```

- **Ordered** — elements maintain insertion order
- **Immutable** — cannot be modified after creation
- Faster than lists for read-only data

```python
point = (10, 20)
point[0] = 99       # ❌ TypeError: 'tuple' object does not support item assignment
```

---

### 🗺️ Dictionary (`dict`)

```python
student = {
    "name": "Swapnil",
    "age": 22,
    "city": "Pune"
}

print(student["name"])   # Swapnil
```

- **Key–value mapping**
- Keys must be **hashable** (immutable types: str, int, tuple)
- Values can be anything
- **Ordered** as of Python 3.7+

```python
# Access
student["age"]           # 22

# Add/Update
student["grade"] = "A"

# Delete
del student["city"]

# Check key
"name" in student        # True
```

---

### 🔘 Set (`set`)

```python
numbers = {1, 2, 3, 3, 2}
print(numbers)   # {1, 2, 3}  ← duplicates removed
```

- **Unordered** — no guaranteed position
- **Unique elements** only
- **Mutable** — can add/remove elements
- Backed by a **hash table** → O(1) lookup

```python
a = {1, 2, 3}
b = {2, 3, 4}

print(a | b)   # Union:        {1, 2, 3, 4}
print(a & b)   # Intersection: {2, 3}
print(a - b)   # Difference:   {1}
```

---

### 📊 Data Types Summary Table

| Type | Example | Ordered | Mutable | Duplicates |
|------|---------|---------|---------|-----------|
| `int` | `10` | — | ❌ | — |
| `float` | `3.14` | — | ❌ | — |
| `complex` | `3+4j` | — | ❌ | — |
| `bool` | `True` | — | ❌ | — |
| `str` | `"Python"` | ✅ | ❌ | ✅ |
| `list` | `[1, 2, 3]` | ✅ | ✅ | ✅ |
| `tuple` | `(1, 2, 3)` | ✅ | ❌ | ✅ |
| `dict` | `{"a": 1}` | ✅ (3.7+) | ✅ | Keys: ❌ |
| `set` | `{1, 2, 3}` | ❌ | ✅ | ❌ |

---

## 📌 7. Checking Types

```python
print(type(10))          # <class 'int'>
print(type(3.14))        # <class 'float'>
print(type(True))        # <class 'bool'>
print(type("Python"))    # <class 'str'>
print(type([1, 2, 3]))   # <class 'list'>
print(type((1, 2)))      # <class 'tuple'>
print(type({"a": 1}))    # <class 'dict'>
print(type({1, 2}))      # <class 'set'>
print(type(None))        # <class 'NoneType'>
```

**`isinstance()` — preferred in production code:**

```python
x = 10

print(isinstance(x, int))         # True
print(isinstance(x, (int, float))) # True — checks against multiple types
```

> ✅ Use `isinstance()` over `type() ==` in real code because it respects inheritance.

---

## 📌 8. Type Conversion

### 🔄 Implicit Conversion (Automatic)

Python automatically converts types when safe to do so:

```python
x = 10        # int
y = 2.5       # float

result = x + y
print(result)        # 12.5
print(type(result))  # <class 'float'>
```

Python promoted `int` → `float` to prevent data loss.

**Implicit conversion rules:**

```
bool → int → float → complex
```

---

### 🔧 Explicit Conversion (Type Casting)

```python
# str → int
age = int("21")
print(age, type(age))   # 21 <class 'int'>

# int → float
x = float(10)
print(x)                # 10.0

# int → str
s = str(100)
print(s, type(s))       # 100 <class 'str'>

# str → list
chars = list("ABC")
print(chars)            # ['A', 'B', 'C']

# list → tuple
t = tuple([1, 2, 3])
print(t)                # (1, 2, 3)

# list → set (removes duplicates)
unique = set([1, 2, 2, 3, 3])
print(unique)           # {1, 2, 3}
```

**Conversion that will fail:**

```python
int("hello")    # ❌ ValueError: invalid literal for int()
int("3.14")     # ❌ ValueError — use float("3.14") first
int(float("3.14"))  # ✅ → 3
```

---

## 📌 9. `id()` Function

`id()` returns the **memory address** (identity) of an object in CPython.

```python
x = 50
print(id(x))   # e.g. 9789280

y = x
print(id(y))   # Same as id(x) — same object
```

**Key rule:**

> If `id(a) == id(b)`, then `a is b` is `True` — they are the **exact same object** in memory.

```python
a = "hello"
b = "hello"

print(id(a))   # May or may not be same (string interning)
print(id(b))

a = [1, 2, 3]
b = [1, 2, 3]

print(id(a))   # Different
print(id(b))   # Different — two separate list objects
```

---

## 📌 10. `is` vs `==`

> This is one of the **most common FAANG interview questions** on Python.

| Operator | Checks | Example |
|----------|--------|---------|
| `==` | **Value equality** | `[1,2] == [1,2]` → `True` |
| `is` | **Identity (same object in memory)** | `[1,2] is [1,2]` → `False` |

### `==` — Value Comparison

```python
a = [1, 2]
b = [1, 2]

print(a == b)   # True ← same values
```

### `is` — Identity Comparison

```python
a = [1, 2]
b = [1, 2]

print(a is b)   # False ← different objects in memory
```

**Memory Diagram:**

```
 a ──────────► [ list: 1, 2, 3 ]   (Object A)

 b ──────────► [ list: 1, 2, 3 ]   (Object B)

 a == b → True   (same values)
 a is b → False  (different objects)
```

### When `is` returns `True`

```python
a = [1, 2]
b = a           # b references the SAME object

print(a is b)   # True ← same object
```

```
 a ──────────┐
             ▼
             [ list: 1, 2 ]
             ▲
 b ──────────┘

 a is b → True
```

---

## 📌 11. Small Integer Caching (CPython Optimization)

CPython **pre-creates and caches** integer objects in the range **-5 to 256** at startup.

```python
a = 100
b = 100

print(a is b)   # True ← both point to the SAME cached object
print(id(a))    # Same as id(b)
print(id(b))
```

**Why?** Integers in this range are used extremely frequently. Caching them avoids creating thousands of identical objects, saving memory and time.

**Outside the cache range:**

```python
a = 1000
b = 1000

print(a is b)   # False ← two separate objects created
                # (though Python may optimize in some contexts)
```

> ⚠️ **FAANG Warning:** Never use `is` to compare integers or strings. Use `==` for value comparison. The caching behavior is an **implementation detail** of CPython that can change.

---

## 📌 12. Common Beginner Mistake

### `input()` always returns a string

```python
x = input("Enter age: ")

print(x + 10)   # ❌ TypeError: can only concatenate str (not "int") to str
```

**Fix:**

```python
x = int(input("Enter age: "))

print(x + 10)   # ✅ Works correctly
```

---

## 📌 13. Real-World Data Engineering Example

Imagine you're a Data Engineer at Amazon reading a CSV with employee salary data:

```python
import pandas as pd

df = pd.read_csv("employees.csv")

print(df.dtypes)
# name      object   ← string
# salary    object   ← read as string! ⚠️
# age       int64
```

If salary is read as `object` (string), you **cannot** do math:

```python
total = df["salary"].sum()   # ❌ Concatenates strings instead of summing!
```

**Fix — explicit type conversion:**

```python
df["salary"] = df["salary"].astype(int)   # or pd.to_numeric()

total = df["salary"].sum()   # ✅ Correct numeric sum
avg   = df["salary"].mean()
```

> 🏗️ **Lesson:** Data type awareness is critical in ETL pipelines. A single wrong type causes wrong results — and at FAANG scale, that means wrong business decisions from millions of rows.

---

## 🖥️ Practice Programs

### ✅ Program 1 — Swap Two Variables Without a Third Variable

```python
# Input
a = int(input("Enter first number: "))
b = int(input("Enter second number: "))

print("\nBefore Swapping")
print("a =", a)
print("b =", b)

# Swap (Pythonic way)
a, b = b, a

print("\nAfter Swapping")
print("a =", a)
print("b =", b)
```

**Output:**
```
Enter first number: 10
Enter second number: 20

Before Swapping
a = 10
b = 20

After Swapping
a = 20
b = 10
```

**How does `a, b = b, a` work?**

```
Step 1: Python evaluates the right side → creates tuple (b, a) = (20, 10)
Step 2: Unpacks the tuple → a = 20, b = 10
```

No temporary variable needed. This is called **tuple unpacking**.

---

### ✅ Program 2 — Read Two Numbers and Print Their Sum

```python
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))

sum_result = num1 + num2

print("Sum =", sum_result)
```

**Output:**
```
Enter first number: 25
Enter second number: 15
Sum = 40
```

---

### ✅ Program 3 — Print the Type of Each Basic Python Data Type

```python
integer_value    = 100
float_value      = 10.5
complex_value    = 2 + 3j
string_value     = "Python"
boolean_value    = True
list_value       = [1, 2, 3]
tuple_value      = (1, 2, 3)
set_value        = {1, 2, 3}
dictionary_value = {"name": "Swapnil", "age": 22}

values = [
    integer_value, float_value, complex_value, string_value,
    boolean_value, list_value, tuple_value, set_value, dictionary_value
]

for v in values:
    print(f"{str(v):<30} → {type(v)}")
```

**Output:**
```
100                            → <class 'int'>
10.5                           → <class 'float'>
(2+3j)                         → <class 'complex'>
Python                         → <class 'str'>
True                           → <class 'bool'>
[1, 2, 3]                      → <class 'list'>
(1, 2, 3)                      → <class 'tuple'>
{1, 2, 3}                      → <class 'set'>
{'name': 'Swapnil', 'age': 22} → <class 'dict'>
```

---

### ✅ Program 4 — Demonstrate `==` vs `is`

```python
list1 = [1, 2, 3]
list2 = [1, 2, 3]
list3 = list1

print("─── list1 vs list2 (same values, different objects) ───")
print("list1 == list2 :", list1 == list2)   # True  — same values
print("list1 is list2 :", list1 is list2)   # False — different objects
print("id(list1)      :", id(list1))
print("id(list2)      :", id(list2))

print()

print("─── list1 vs list3 (same object) ───")
print("list1 == list3 :", list1 == list3)   # True  — same values
print("list1 is list3 :", list1 is list3)   # True  — SAME object
print("id(list1)      :", id(list1))
print("id(list3)      :", id(list3))
```

**Output:**
```
─── list1 vs list2 (same values, different objects) ───
list1 == list2 : True
list1 is list2 : False
id(list1)      : 140234...
id(list2)      : 140235...    ← different address

─── list1 vs list3 (same object) ───
list1 == list3 : True
list1 is list3 : True
id(list1)      : 140234...
id(list3)      : 140234...    ← SAME address
```

**Memory Diagram:**

```
list1 ──────────────► [ 1, 2, 3 ]  (Object A at 0x...234)

list2 ──────────────► [ 1, 2, 3 ]  (Object B at 0x...235)

list3 ──────────────► [ 1, 2, 3 ]  (Object A at 0x...234)  ← same as list1
```

---

### ✅ Program 5 — Reassigning One Variable Doesn't Affect Another

```python
a = 100
b = a

print("─── Initially ───")
print("a =", a, "  id:", id(a))
print("b =", b, "  id:", id(b))
print("a is b:", a is b)   # True — same cached int object

a = 200

print("\n─── After Reassigning a = 200 ───")
print("a =", a, "  id:", id(a))
print("b =", b, "  id:", id(b))
print("a is b:", a is b)   # False — a now points to different object
```

**Output:**
```
─── Initially ───
a = 100   id: 140735...
b = 100   id: 140735...   ← same object
a is b: True

─── After Reassigning a = 200 ───
a = 200   id: 140736...   ← new object
b = 100   id: 140735...   ← b unchanged
a is b: False
```

**Memory Timeline:**

```
Step 1: a = 100
        b = a

  a ──┐
      ▼
    [ 100 ]
      ▲
  b ──┘

Step 2: a = 200

  a ──────► [ 200 ]   (new object)

  b ──────► [ 100 ]   (unchanged)
```

---

## 🎯 FAANG Interview Questions & Answers

### Q1. Does a variable store a value or a reference?

> **Answer:** In Python, a variable stores a **reference (pointer) to an object**, not the object itself. The object lives on the heap. When you write `x = 10`, Python creates an integer object `10` in memory, and `x` is just a name that points to it.

---

### Q2. What is the difference between `=` and `==`?

> **Answer:**
> - `=` is the **assignment operator** — it makes a variable point to an object
> - `==` is the **equality operator** — it compares the **values** of two objects
>
> ```python
> x = 10      # assignment — x now references integer 10
> x == 10     # comparison — evaluates to True
> ```

---

### Q3. What does `is` compare?

> **Answer:** `is` checks whether two variables **reference the exact same object in memory** (same `id()`). It checks **object identity**, not value equality.
>
> ```python
> a = [1, 2]
> b = [1, 2]
> print(a == b)   # True  — same values
> print(a is b)   # False — different objects
> ```

---

### Q4. Why does `input()` return a string?

> **Answer:** The `input()` function reads from stdin (keyboard), which is a **text stream**. All keyboard input arrives as text (characters). Python has no way to know the intended type, so it always returns a `str`. The programmer must explicitly convert: `int(input(...))`, `float(input(...))`, etc.

---

### Q5. Why shouldn't you use `is` to compare numbers or strings?

> **Answer:** `is` checks object identity, not value. Python's small integer caching makes `a is b` appear `True` for small integers (like `100`), but for larger integers or strings created differently, it may be `False` even when values are equal. This is an **implementation detail** of CPython — it's unreliable and misleading. Always use `==` for value comparison.

---

### Q6. What is dynamic typing and how is it different from static typing?

> **Answer:**
> - **Dynamic typing** (Python): The **type is attached to the object**, not the variable. A variable can point to objects of different types at different times. Type checking happens at **runtime**.
> - **Static typing** (Java, C++): The **type is declared with the variable** and cannot change. Type checking happens at **compile time**.
>
> ```python
> x = 10        # int
> x = "hello"   # now str — valid in Python
> ```
> ```java
> int x = 10;
> x = "hello";  // ERROR in Java — type mismatch
> ```

---

### Q7. What is the difference between mutable and immutable types?

> **Answer:**
> - **Immutable** types: `int`, `float`, `str`, `tuple`, `bool`, `frozenset` — their content **cannot be changed** after creation. Any "modification" creates a **new object**.
> - **Mutable** types: `list`, `dict`, `set` — their content **can be changed in place** without creating a new object.
>
> ```python
> s = "hello"
> s[0] = "H"    # ❌ TypeError — strings are immutable
>
> lst = [1, 2]
> lst[0] = 99   # ✅ Works — lists are mutable
> ```

---

### Q8. What does CPython's small integer cache do?

> **Answer:** CPython pre-creates integer objects for values **-5 to 256** at interpreter startup and reuses them. When you assign `x = 100` and `y = 100`, both variables point to the **same cached object**. This saves memory and improves performance since these small integers are used constantly. Values outside this range get new objects each time (though compiler optimizations can cause surprises in interactive vs module contexts).

---

## 📝 Chapter Summary

```
┌──────────────────────────────────────────────────────────────────┐
│                     CHAPTER 2 SUMMARY                            │
├──────────────────────────────────────────────────────────────────┤
│ Variable        → Name that references an object in memory       │
│ Assignment (=)  → Creates a reference, does NOT copy data        │
│ Everything      → Is an object (has type, value, identity)       │
│ id()            → Returns memory address of object (CPython)     │
│ type()          → Returns the type of an object                  │
│ ==              → Compares values                                 │
│ is              → Compares object identity (same memory address) │
│ Dynamic typing  → Type on the object, not the variable           │
│ int cache       → CPython caches integers -5 to 256              │
│ Immutable types → int, float, str, tuple, bool                   │
│ Mutable types   → list, dict, set                                │
│ Implicit conv.  → bool → int → float → complex                  │
│ Explicit conv.  → int(), float(), str(), list(), set(), tuple()  │
└──────────────────────────────────────────────────────────────────┘
```

---

## ⬅️ Navigation

| | |
|---|---|
| ⬅️ Previous | [Chapter 1 — Introduction to Python](../Chapter-01-Introduction-to-Python/README.md) |
| 🏠 Home | [Python Journey](../README.md) |
| ➡️ Next | [Chapter 3 — Control Flow](../Chapter-03-Control-Flow/README.md) |
