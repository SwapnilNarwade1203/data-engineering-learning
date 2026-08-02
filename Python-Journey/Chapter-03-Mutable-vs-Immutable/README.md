# 🐍 Python Journey – Chapter 3
## Mutable vs Immutable Objects (FAANG Level)

> 🎯 **Goal:** Understand one of the most important concepts in Python — mutability. This is the source of some of the nastiest production bugs and one of the most frequently tested topics in FAANG interviews.

---

## 📋 Learning Objectives

By the end of this chapter, you will understand:

- ✅ What **mutable** and **immutable** objects are — at a memory level
- ✅ Why strings **cannot** be modified in place
- ✅ Why lists **can** be modified in place
- ✅ Why tuples are immutable (and when to prefer them)
- ✅ What actually happens in memory during modification
- ✅ **Aliasing** — the silent cause of production bugs
- ✅ How to correctly **copy** objects (shallow vs deep)
- ✅ `+=` behavior difference on mutable vs immutable types
- ✅ How Python passes arguments to functions (pass-by-object-reference)

---

## 📌 1. What Does Mutable Mean?

> A **mutable** object can be **changed in place** after it is created — without creating a new object.

```python
numbers = [10, 20, 30]
numbers.append(40)
print(numbers)   # [10, 20, 30, 40]
```

**Memory — what actually happened:**

```
Before append:
  numbers
     │
     ▼
  ┌─────────────────┐
  │  10 │ 20 │ 30  │   id: 140451...
  └─────────────────┘

After append:
  numbers
     │
     ▼
  ┌──────────────────────┐
  │  10 │ 20 │ 30 │ 40  │   id: 140451...  ← SAME object, SAME id
  └──────────────────────┘
```

> ✅ The **same object** was modified. The `id()` does not change.

---

## 📌 2. What Does Immutable Mean?

> An **immutable** object **cannot be modified** after creation. Any "change" creates a **brand new object**.

```python
name = "Python"
name[0] = "J"   # ❌ TypeError: 'str' object does not support item assignment
```

**Why?** Strings are immutable — their internal characters cannot be overwritten.

**What you CAN do:**

```python
name = "Python"
name = "Jython"   # ✅ — but this creates a NEW object
```

**Memory:**

```
Step 1: name = "Python"

  name
   │
   ▼
 ┌──────────┐
 │ "Python" │   id: 140351...
 └──────────┘

Step 2: name = "Jython"

  name
   │
   ▼
 ┌──────────┐
 │ "Jython" │   id: 140782...   ← completely NEW object
 └──────────┘

 ┌──────────┐
 │ "Python" │   id: 140351...   ← still exists until garbage collected
 └──────────┘
```

---

## 📌 3. Immutable Data Types

These types **cannot be modified** after creation:

| Type | Example |
|------|---------|
| `int` | `x = 10` |
| `float` | `x = 3.14` |
| `bool` | `x = True` |
| `complex` | `x = 3+4j` |
| `str` | `x = "Python"` |
| `tuple` | `x = (1, 2, 3)` |
| `frozenset` | `x = frozenset({1, 2})` |
| `bytes` | `x = b"data"` |

**Example with integers:**

```python
x = 10
print(id(x))   # e.g. 9789440

x = 20
print(id(x))   # different — new object
```

**What happened:**

```
x = 10
  x ──────► [ int: 10 ]   id: 9789440

x = 20
  x ──────► [ int: 20 ]   id: 9789760  (new object)
             [ int: 10 ]              (reference count → 0 → garbage collected)
```

> Python didn't change `10` into `20`. It created a new `int` object `20` and rebound `x` to it.

---

## 📌 4. Mutable Data Types

These types **can be modified in place**:

| Type | Example |
|------|---------|
| `list` | `[1, 2, 3]` |
| `dict` | `{"a": 1}` |
| `set` | `{1, 2, 3}` |
| `bytearray` | `bytearray(b"data")` |

```python
marks = [80, 90]
print(id(marks))   # 140451...

marks.append(100)
print(id(marks))   # 140451... ← SAME id

print(marks)       # [80, 90, 100]
```

---

## 📌 5. Proving It with `id()` — String vs List

### 🔤 String (Immutable)

```python
name = "Google"
print(id(name))       # 140351...

name = name + " Cloud"
print(id(name))       # 140782... ← DIFFERENT id
print(name)           # Google Cloud
```

> Every string operation that "modifies" a string actually **creates a brand new string object**.

---

### 📋 List (Mutable)

```python
numbers = [1, 2, 3]
print(id(numbers))    # 140451...

numbers.append(4)
print(id(numbers))    # 140451... ← SAME id
print(numbers)        # [1, 2, 3, 4]
```

> `append()` modifies the **existing object** in place.

---

## 📌 6. Aliasing — The Silent Bug

> **Aliasing** means multiple variables refer to the **same object** in memory.

```python
a = [10, 20]
b = a

b.append(30)

print(a)   # [10, 20, 30]  ← Most beginners expect [10, 20]
print(b)   # [10, 20, 30]
```

**Memory Diagram:**

```
Before append:

  a ──────────┐
              ▼
          [ 10 │ 20 ]   id: 140451...
              ▲
  b ──────────┘

After b.append(30):

  a ──────────┐
              ▼
          [ 10 │ 20 │ 30 ]   id: 140451...  ← same object, modified
              ▲
  b ──────────┘
```

> Both `a` and `b` point to the **same list object**. Modifying it through `b` is visible through `a` too — because they are aliases.

---

## 📌 7. Immutable Types Don't Have This Problem

```python
x = 100
y = x

x = 200

print(y)   # 100  ← unchanged
```

**Why?**

```
Step 1: x = 100, y = x

  x ──────┐
          ▼
        [ 100 ]
          ▲
  y ──────┘

Step 2: x = 200

  x ──────► [ 200 ]   (new object)

  y ──────► [ 100 ]   (unchanged — integers are immutable)
```

> When you do `x = 200`, Python **cannot modify** the integer `100` (it's immutable). So it creates a new object `200` and rebinds `x`. The variable `y` still points to `100`.

---

## 📌 8. How Python Passes Arguments to Functions

Python uses **pass-by-object-reference** (also called pass-by-assignment).

### Mutable argument — original IS modified

```python
def add_item(lst):
    lst.append(100)

numbers = [10, 20]
add_item(numbers)
print(numbers)    # [10, 20, 100]  ← original list changed!
```

**Memory:**

```
  numbers ──────► [ 10 │ 20 ]
                      ▲
  lst    ──────────────┘   (same object inside function)

  lst.append(100) → modifies the shared object

  numbers → [ 10 │ 20 │ 100 ]   ← original affected!
```

---

### Immutable argument — original is NOT modified

```python
def increment(x):
    x = x + 1
    print("Inside:", x)

num = 10
increment(num)
print("Outside:", num)
```

**Output:**
```
Inside: 11
Outside: 10
```

**Why?**

```
  num ──────► [ 10 ]
                 ▲
  x  ───────────┘   (same object on function call)

  x = x + 1
  → creates NEW object [ 11 ]
  → x now points to [ 11 ]
  → num still points to [ 10 ]
```

> The integer `10` is immutable — `x + 1` creates a new object. `num` is unaffected.

---

## 📌 9. Interview Questions — Predict the Output

### 🔴 Question 1

```python
a = [1, 2]
b = a
a = [5, 6]

print(b)
```

**Answer:**
```
[1, 2]
```

**Why?**

```
Step 1: a = [1, 2]
  a ──────► [ 1, 2 ]

Step 2: b = a
  a ──────► [ 1, 2 ] ◄── b

Step 3: a = [5, 6]    ← REBINDING, not modifying
  a ──────► [ 5, 6 ]  (new object)
             [ 1, 2 ] ◄── b  (b still points to original)
```

> `a = [5, 6]` **rebinds** `a` to a new list. It does **not** modify the old list. `b` still sees `[1, 2]`.

---

### 🔴 Question 2

```python
a = [1, 2]
b = a
a.append(3)

print(b)
```

**Answer:**
```
[1, 2, 3]
```

**Why?**

```
a ──────┐
        ▼
    [ 1, 2 ]  ◄── b   (same object)

a.append(3) → MODIFIES the object in place

a ──────┐
        ▼
    [ 1, 2, 3 ]  ◄── b
```

> `a.append(3)` modifies the **same list object** that `b` also points to.

---

### 🔴 Question 3 — Aliasing with 3 Variables

```python
a = [10]
b = a
c = b

c.append(20)

print(a)   # ?
print(b)   # ?
print(c)   # ?
```

**Answer:**
```
[10, 20]
[10, 20]
[10, 20]
```

**Memory:**

```
  a ──────────┐
              │
  b ──────────┼──────► [ 10 │ 20 ]   (all point to same object)
              │
  c ──────────┘
```

> All three are aliases of the same list. Modifying via any one of them affects all.

---

## 📌 10. `+=` Behavior — Mutable vs Immutable

This is a subtle but important difference.

### `+=` on a List (Mutable) — Same Object

```python
a = [1, 2]
print(id(a))   # 140451...

a += [3]

print(id(a))   # 140451... ← SAME id
print(a)       # [1, 2, 3]
```

> `list += [3]` calls `__iadd__` which is **in-place add** → modifies the existing list → same `id`.

---

### `+=` on a String (Immutable) — New Object

```python
s = "Hi"
print(id(s))   # 140351...
print(s)       # Hi

s += "!"

print(id(s))   # 140782... ← DIFFERENT id
print(s)       # Hi!
```

> `str += "!"` creates a **brand new string object** → different `id`.

---

### ⚠️ Bug with `+=` and Aliasing

```python
a = [1, 2]
b = a

a += [3]   # in-place — same object

print(b)   # [1, 2, 3]  ← b is affected too!
print(a is b)  # True — still same object
```

vs.

```python
a = [1, 2]
b = a

a = a + [3]   # creates NEW object

print(b)   # [1, 2]  ← b is NOT affected
print(a is b)  # False — different objects now
```

> `+=` on a list modifies in place. `= a + [3]` creates a new object. Knowing the difference prevents subtle bugs.

---

## 📌 11. Copying Objects

To avoid aliasing bugs, you need to make a **copy**.

### ❌ Aliasing (NOT a copy)

```python
a = [1, 2, 3]
b = a          # b is an alias, NOT a copy
```

---

### ✅ Shallow Copy

Copies the **outer object** but nested objects are still shared.

```python
import copy

a = [1, 2, 3]
b = a.copy()         # method 1
b = list(a)          # method 2
b = a[:]             # method 3
b = copy.copy(a)     # method 4

b.append(99)

print(a)   # [1, 2, 3]   ← unaffected
print(b)   # [1, 2, 3, 99]
```

**Shallow Copy Limitation — nested objects are still shared:**

```python
a = [[1, 2], [3, 4]]
b = a.copy()

b[0].append(99)   # modifying nested list

print(a)   # [[1, 2, 99], [3, 4]]  ← AFFECTED!
print(b)   # [[1, 2, 99], [3, 4]]
```

**Memory:**

```
a ──────► [ ref0 │ ref1 ]
b ──────► [ ref0 │ ref1 ]   ← b is a new list, but contains SAME inner lists

  ref0 ──► [ 1 │ 2 ]   ← shared between a and b
  ref1 ──► [ 3 │ 4 ]   ← shared between a and b
```

---

### ✅ Deep Copy — Fully Independent

```python
import copy

a = [[1, 2], [3, 4]]
b = copy.deepcopy(a)

b[0].append(99)

print(a)   # [[1, 2], [3, 4]]   ← NOT affected
print(b)   # [[1, 2, 99], [3, 4]]
```

**Memory:**

```
a ──────► [ ref0 │ ref1 ]
            ref0 ──► [ 1 │ 2 ]
            ref1 ──► [ 3 │ 4 ]

b ──────► [ ref2 │ ref3 ]   ← completely separate copies
            ref2 ──► [ 1 │ 2 │ 99 ]
            ref3 ──► [ 3 │ 4 ]
```

---

## 📌 12. Why Are Strings Immutable?

This is a frequently asked FAANG interview question.

| Reason | Explanation |
|--------|-------------|
| **Performance** | Immutable strings can be **interned** (cached and reused). Python can safely share one `"hello"` object between multiple variables |
| **Hashability** | Strings can be used as **dictionary keys** and **set members** because their hash never changes. If strings were mutable, their hash could change — breaking the data structure |
| **Security** | Connection strings, file paths, config values should not be accidentally modified |
| **Thread Safety** | Immutable objects can be shared across threads without locks |
| **Predictability** | Code is easier to reason about when you know a string can't be changed by another part of the program |

---

## 📌 13. Summary Table

| Feature | Mutable | Immutable |
|---------|---------|-----------|
| Can change after creation? | ✅ Yes | ❌ No |
| Same `id()` after modification? | ✅ Yes | ❌ No (new object) |
| Examples | `list`, `dict`, `set` | `int`, `float`, `str`, `tuple` |
| Shared references see changes? | ✅ Yes | ❌ No |
| Can be dict key / set member? | ❌ No (not hashable) | ✅ Yes |
| Thread safe by default? | ❌ No | ✅ Yes |
| `+=` creates new object? | ❌ No (in-place) | ✅ Yes |

---

## 🖥️ Practice Programs

### ✅ Program 1 — List aliasing: modifying through one variable affects another

```python
list1 = [10, 20, 30]
list2 = list1   # alias — NOT a copy

print("Before modification:")
print("list1:", list1)
print("list2:", list2)
print("id(list1):", id(list1))
print("id(list2):", id(list2))
print("list1 is list2:", list1 is list2)

list2.append(40)

print("\nAfter list2.append(40):")
print("list1:", list1)   # [10, 20, 30, 40] ← affected!
print("list2:", list2)
```

**Output:**
```
Before modification:
list1: [10, 20, 30]
list2: [10, 20, 30]
id(list1): 140451...
id(list2): 140451...   ← same
list1 is list2: True

After list2.append(40):
list1: [10, 20, 30, 40]   ← affected!
list2: [10, 20, 30, 40]
```

---

### ✅ Program 2 — Integer immutability: reassigning doesn't affect another variable

```python
a = 50
b = a

print("Before reassignment:")
print("a:", a, "  id:", id(a))
print("b:", b, "  id:", id(b))
print("a is b:", a is b)

a = 999

print("\nAfter a = 999:")
print("a:", a, "  id:", id(a))
print("b:", b, "  id:", id(b))
print("a is b:", a is b)
```

**Output:**
```
Before reassignment:
a: 50   id: 9790624
b: 50   id: 9790624   ← same object
a is b: True

After a = 999:
a: 999   id: 140735...   ← new object
b: 50    id: 9790624     ← unchanged
a is b: False
```

---

### ✅ Program 3 — String `id()` before and after concatenation

```python
s = "Data"
print("Before:", s, "  id:", id(s))

s = s + " Engineering"
print("After :", s, "  id:", id(s))

# Confirm: completely new object
```

**Output:**
```
Before: Data         id: 140351...
After : Data Engineering   id: 140782...   ← different object
```

---

### ✅ Program 4 — List `id()` before and after `append()`

```python
lst = [1, 2, 3]
print("Before:", lst, "  id:", id(lst))

lst.append(4)
print("After :", lst, "  id:", id(lst))

# Confirm: same object
```

**Output:**
```
Before: [1, 2, 3]      id: 140451...
After : [1, 2, 3, 4]   id: 140451...   ← same object!
```

---

### ✅ Program 5 — Aliasing with a dictionary (3 variables)

```python
d1 = {"name": "Swapnil", "role": "Data Engineer"}
d2 = d1
d3 = d1

print("All point to same object?")
print("d1 is d2:", d1 is d2)   # True
print("d2 is d3:", d2 is d3)   # True

d3["city"] = "Pune"

print("\nAfter d3['city'] = 'Pune':")
print("d1:", d1)   # all three show the new key!
print("d2:", d2)
print("d3:", d3)
```

**Output:**
```
All point to same object?
d1 is d2: True
d2 is d3: True

After d3['city'] = 'Pune':
d1: {'name': 'Swapnil', 'role': 'Data Engineer', 'city': 'Pune'}
d2: {'name': 'Swapnil', 'role': 'Data Engineer', 'city': 'Pune'}
d3: {'name': 'Swapnil', 'role': 'Data Engineer', 'city': 'Pune'}
```

---

### ✅ Program 6 — Predict the output (Classic Interview Question)

```python
a = [10]
b = a
c = b

c.append(20)

print(a)
print(b)
print(c)
```

**Predicted Output:**
```
[10, 20]
[10, 20]
[10, 20]
```

**Explanation:**

```
Step 1: a = [10]
  a ──────► [ 10 ]

Step 2: b = a
  b ──────► [ 10 ]  (same object as a)

Step 3: c = b
  c ──────► [ 10 ]  (same object as a and b)

Step 4: c.append(20)
  → modifies the ONE object all three point to

  a ──────┐
  b ──────┼──────► [ 10 │ 20 ]
  c ──────┘
```

---

## 🎯 FAANG Interview Questions & Answers

### Q1. What is the difference between mutable and immutable objects?

> **Answer:** A **mutable** object can be modified after creation — its internal state can change while the `id()` stays the same (e.g., `list`, `dict`, `set`). An **immutable** object cannot be changed after creation — any "modification" produces a new object with a different `id()` (e.g., `int`, `str`, `tuple`).

---

### Q2. Why are strings immutable in Python?

> **Answer:** Strings are immutable for several reasons:
> 1. **Hashability** — strings can be used as dict keys because their value (and thus hash) never changes
> 2. **Performance** — Python can intern/cache string objects and reuse them safely
> 3. **Thread safety** — immutable objects can be shared across threads without locking
> 4. **Security** — critical values like file paths and config strings are protected from accidental modification

---

### Q3. What is aliasing and why is it dangerous?

> **Answer:** Aliasing occurs when multiple variables reference the **same object** in memory. It is dangerous with **mutable objects** because modifying the object through one variable unexpectedly affects all other variables pointing to it — a common source of hard-to-find bugs in production.
>
> ```python
> a = [1, 2]
> b = a           # alias — NOT a copy
> b.append(3)
> print(a)        # [1, 2, 3]  ← unexpected!
> ```

---

### Q4. What is the difference between shallow copy and deep copy?

> **Answer:**
> - **Shallow copy** creates a new outer container but **nested objects are still shared**. Use `list.copy()`, `list[:]`, `copy.copy()`.
> - **Deep copy** creates a **completely independent copy** of the object and all nested objects. Use `copy.deepcopy()`.
>
> Shallow copy is sufficient for flat (non-nested) structures. Deep copy is needed for nested mutable structures.

---

### Q5. Does Python pass arguments by value or by reference?

> **Answer:** Neither — Python uses **pass-by-object-reference** (also called pass-by-assignment). The function receives a reference to the same object. If the object is **mutable**, modifications inside the function affect the original. If the object is **immutable**, any "modification" creates a new object locally, leaving the original unchanged.

---

### Q6. What is the difference between `a += [3]` and `a = a + [3]` for lists?

> **Answer:**
> - `a += [3]` calls `list.__iadd__()` — **in-place addition** — modifies the existing list, `id()` stays the same. All aliases see the change.
> - `a = a + [3]` creates a **new list** object and rebinds `a` to it. `id()` changes. Aliases still point to the original.
>
> This distinction is important in aliasing scenarios:
> ```python
> a = [1, 2]; b = a
> a += [3]       # b is also [1, 2, 3] — same object
>
> a = [1, 2]; b = a
> a = a + [3]    # b is still [1, 2] — a rebound to new object
> ```

---

### Q7. Can a tuple contain mutable elements?

> **Answer:** Yes. A tuple is immutable — meaning you cannot add/remove/replace elements. But if a tuple contains a reference to a mutable object, that object can be modified:
>
> ```python
> t = ([1, 2], [3, 4])
> t[0].append(99)   # ✅ modifying the list inside the tuple
> print(t)          # ([1, 2, 99], [3, 4])
>
> t[0] = [9, 9]     # ❌ TypeError — cannot replace the reference
> ```
>
> The tuple's references are immutable, but the objects being referenced can be mutable.

---

## 📝 Chapter Summary

```
┌──────────────────────────────────────────────────────────────────┐
│                     CHAPTER 3 SUMMARY                            │
├──────────────────────────────────────────────────────────────────┤
│ Mutable      → list, dict, set — modified in place, same id()   │
│ Immutable    → int, float, str, tuple — new object on "change"  │
│ Aliasing     → Multiple vars → same object → dangerous w/ mut.  │
│ Copy         → Use .copy() or deepcopy() to break aliasing       │
│ Shallow copy → New container, shared nested objects              │
│ Deep copy    → Fully independent clone of all nested objects     │
│ Functions    → Pass-by-object-reference                          │
│ Mutable arg  → Original CAN be modified inside function          │
│ Immutable    → Original CANNOT be modified inside function       │
│ += on list   → In-place (same id), affects all aliases           │
│ += on str    → New object (different id), aliases unaffected     │
│ Tuple        → Immutable container, but CAN hold mutable items   │
└──────────────────────────────────────────────────────────────────┘
```

---

## ⬅️ Navigation

| | |
|---|---|
| ⬅️ Previous | [Chapter 2 — Variables, Data Types & Memory Model](../Chapter-02-Variables-and-DataTypes/README.md) |
| 🏠 Home | [Python Journey](../README.md) |
| ➡️ Next | [Chapter 4 — Control Flow](../Chapter-04-Control-Flow/README.md) |
