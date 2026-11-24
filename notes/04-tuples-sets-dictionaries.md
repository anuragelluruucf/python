# November 12, 2026 — Tuples, sets, dictionaries and copying

## Tuples

Tuples are ordered, immutable sequences.

```python
record = (101, "Ana", 85)
record[0]    # 101
record[1:]   # ('Ana', 85)
record.count(101)  # 1
record.index("Ana")  # 1
single = (5,)  # trailing comma makes a one-item tuple
```

You cannot append, remove or reassign a tuple element. To produce a modified version:

```python
original = (1, 2, 3)
working = list(original)
working.append(4)
updated = tuple(working)
# original remains (1, 2, 3); updated is (1, 2, 3, 4)
```

Clarifications: this creates another tuple, not a mutation of the original. Immutability is not security or access control; a tuple does not protect passwords or database records. A tuple can also contain a mutable list, whose contents may still change.


## Sets

A set is a mutable collection of unique hashable elements. It is useful for membership checks and removing duplicates when order is unimportant.

```python
numbers = {1, 2, 2, 3}
print(numbers == {1, 2, 3})  # True
empty_set = set()  # {} instead creates an empty dictionary
numbers.add(4)
numbers.add(4)     # no second 4 is added
print(2 in numbers)  # True
```

Sets do not support positional indexing or slicing. Do not rely on their printed order. “Unordered” does not mean the order must randomly change every time you print the same unchanged set.

```python
numbers.remove(4)   # removes 4; KeyError if absent
numbers.discard(9)  # absent item is harmless
numbers.clear()    # empty the set
```

Choose remove when absence indicates a problem; choose discard when absence is acceptable. Neither is universally better. Lists and dictionaries cannot be set elements because they are unhashable.

Supplementary revision: the following standard set operations are useful, but their detailed classroom coverage is not established by the reviewed transcript:

```python
a = {1, 2, 3}
b = {3, 4}
a | b  # {1, 2, 3, 4}: union
a & b  # {3}: intersection
a - b  # {1, 2}: difference
a ^ b  # {1, 2, 4}: symmetric difference
```


## Dictionaries

Dictionaries are mutable mappings from unique hashable keys to values.

```python
student = {"name": "Ana", "marks": 85}
print(student["name"])  # Ana
student["marks"] = 90
student["city"] = "Orlando"
```

They are accessed by key, not position. A numeric key, if present, is still a key, not a positional index.

```python
example = {"a": 1, "a": 5}
print(example)  # {'a': 5}
```

Repeating a key overwrites its value; equal values under different keys are allowed. Modern dictionaries preserve insertion order. They are not unordered in the same sense as sets.

### Views and iteration

```python
student = {"name": "Ana", "marks": 90}
print(list(student.keys()))    # ['name', 'marks']
print(list(student.values()))  # ['Ana', 90]
print(list(student.items()))   # [('name', 'Ana'), ('marks', 90)]

for key, value in student.items():
    print(key, value)
```

Iteration over `student` alone yields keys. Use `.items()` when unpacking key/value pairs. Checking `"name" in student` checks keys. Accessing a missing key with brackets raises KeyError.

### Nested dictionaries and lists

```python
data = {
    "student": {
        "name": "Ana",
        "scores": [70, 80, 90]
    }
}
print(data["student"]["scores"][1])  # 80
```

At each level, check the type: dictionaries need keys; lists need positions.

### Dictionary comprehension

```python
squares = {number: number ** 2 for number in range(1, 4)}
# {1: 1, 2: 4, 3: 9}
```

Unlike a list comprehension, this produces key/value pairs with a colon. Repeated generated keys retain the last assigned value.


## Classroom counting example: character frequencies

```python
text = "aabcba"
counts = {}
for character in text:
    if character in counts:
        counts[character] += 1
    else:
        counts[character] = 1

print(counts)  # {'a': 3, 'b': 2, 'c': 1}

summary = ""
for character, count in counts.items():
    summary += f"{character}{count}"
print(summary)  # a3b2c1
```

Logic: the first occurrence initializes a count; later occurrences increment it. The second loop formats the accumulated results.

Important distinction: this counts characters across the entire string. It is not consecutive run-length encoding. For `"aabcba"`, a consecutive-run representation would be `a2b1c1b1a1`. Global frequencies lose the original arrangement and cannot reconstruct the input. The class mentioned both formulations; establish which one a question asks for before coding.


## Assignment, shallow copy and deep copy

This needs special attention: direct assignment and shallow copying are different, even when both expose changes to nested objects.

### Assignment: another reference to the same object

```python
original = [[1, 2], [3, 4]]
alias = original
alias.append([5, 6])
print(original)  # [[1, 2], [3, 4], [5, 6]]
print(alias is original)  # True
```

No new list is created.

### Shallow copy: new outer container, shared nested objects

```python
import copy
original = [[1, 2], [3, 4]]
shallow = copy.copy(original)
print(shallow is original)        # False
print(shallow[0] is original[0])  # True

shallow.append([5, 6])
print(original)  # [[1, 2], [3, 4]]; outer append is independent

shallow[0][0] = 99
print(original)  # [[99, 2], [3, 4]]; inner list was shared
```

For a list, `.copy()` and `[:]` also make shallow copies. Replacing an outer element is different from modifying a shared inner list.

### Deep copy: recursively copy nested contents

```python
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)
deep[0][0] = 99
print(original)  # [[1, 2], [3, 4]]
print(deep)      # [[99, 2], [3, 4]]
```

For these nested built-in lists, deep copying separates the mutable inner lists. It can consume more time and memory; do not use it automatically when a shallow copy is sufficient.

| Method | New outer list? | Inner lists shared with original? |
|---|---|---|
| alias = original | No | Yes |
| shallow = copy.copy(original) | Yes | Yes |
| deep = copy.deepcopy(original) | Yes | No, in this example |
