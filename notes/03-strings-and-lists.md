# November 11, 2026 — Strings and lists

## Containers at a glance

| Container | Syntax | Mutable? | Access | Duplicate handling |
|---|---|---|---|---|
| String | `"abc"` | No | Integer index/slice | Repeated characters allowed |
| List | `[1, 2]` | Yes | Integer index/slice | Repeated values allowed |
| Tuple | `(1, 2)` | No | Integer index/slice | Repeated values allowed |
| Set | `{1, 2}` | Yes | Membership/iteration; no positional index | Unique elements |
| Dictionary | `{"name": "Ana"}` | Yes | Key | Unique keys; values may repeat |

Strings, lists and tuples are sequences. Dictionaries preserve insertion order in modern Python but remain key-based mappings. Sets provide no positional-order guarantee.


## Indexing and slicing

```python
text = "Python"
text[0]   # 'P'
text[1]   # 'y'
text[-1]  # 'n'
text[-2]  # 'o'
```

Positive positions start at 0. Negative positions count from the end. An invalid single index raises IndexError.

Slicing uses `sequence[start:stop:step]`:

```python
text[1:4]   # 'yth'
text[:3]    # 'Pyt'
text[3:]    # 'hon'
text[::2]   # 'Pto'
text[::-1]  # 'nohtyP'
text[-1:-4:-1]  # 'noh'
```

Stop is excluded. Step controls direction as well as spacing. `text[-1:-4]` is empty because the default positive step moves the wrong way. Slices tolerate out-of-range boundaries; single indexing does not.


## Strings: operations and methods

Strings are immutable: you cannot replace an individual character in the existing string.

```python
text = "hello"
# text[0] = "H"  # TypeError
text = "H" + text[1:]  # creates a new string and rebinds the name
```

Concatenation uses `+`; repetition uses `*`:

```python
"Py" + "thon"  # 'Python'
"ha" * 3       # 'hahaha'
```

### Methods discussed

```python
text = "Hello World"
text.upper()       # 'HELLO WORLD'
text.lower()       # 'hello world'
text.split()       # ['Hello', 'World']
text.count("o")    # 2
text.find("o")     # 4; first matching position
text.find("z")     # -1; not found
text.find("o", 5)  # 7; start searching at position 5
```

A method such as upper returns a result; it does not change the original string. Save it if needed: `text = text.upper()`.

```python
"a,b,c".split(",")     # ['a', 'b', 'c']
"a,b,c".split(",", 1)  # ['a', 'b,c']
"a=b=c".partition("=") # ('a', '=', 'b=c')
```

Split removes the separator from its pieces. Partition splits at the first match and preserves the separator as the middle item of a three-item tuple. If not found, partition returns the original string followed by two empty strings.

### Character checks

```python
"Python".isalpha()   # True
"123".isdigit()      # True
"Python3".isalnum()  # True
"Python 3".isalnum() # False: space is not alphanumeric
"hello".islower()    # True
"HELLO".isupper()    # True
"Hello World".istitle()  # True
```

These return Booleans; they do not convert text. `isalnum` does not require both letters and digits: all letters or all digits also qualifies. These character tests are not complete password-validation rules.


## Lists

Lists are ordered, mutable sequences. They may contain different types and repeated values.

```python
values = [10, "hello", 3.5, 10]
values[0] = 20
print(values)  # [20, 'hello', 3.5, 10]
```

### Main list methods

| Operation | Meaning |
|---|---|
| items.append(x) | Add x as one item at the end |
| items.extend(iterable) | Add each item from the iterable |
| items.insert(i, x) | Insert x before position i |
| items.pop() | Remove and return the last item |
| items.pop(i) | Remove and return item at position i |
| items.remove(x) | Remove the first equal value; ValueError if absent |
| items.index(x) | First matching position; ValueError if absent |
| items.count(x) | Count matching values |
| items.reverse() | Reverse in place |
| items.sort() | Sort in place |

```python
a = [1, 2]
a.append([3, 4])
print(a)  # [1, 2, [3, 4]]

b = [1, 2]
b.extend([3, 4])
print(b)  # [1, 2, 3, 4]
```

This distinction matters: append keeps the supplied list as one nested object; extend adds its elements individually.

```python
numbers = [30, 10, 20]
removed = numbers.pop(1)
print(removed)  # 10
numbers.sort()
print(numbers)  # [20, 30]
```

Clarifications: pop's result needs saving only if you want to use the removed value. Methods such as append, extend, sort and reverse modify in place and return None: do not write `numbers = numbers.sort()`. Arbitrary mixed strings and integers cannot normally be sorted together in Python 3.


## Nested lists

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]
print(matrix[1])     # [4, 5, 6]
print(matrix[1][2])  # 6
```

Read left to right: select the row, then select within it. Ordinary nested Python lists use `matrix[1][2]`, not `matrix[1, 2]`.

```python
for row in matrix:
    for value in row:
        print(value)
```

For deeper nesting, each additional bracket selects another level.


## List comprehensions

A comprehension constructs a new list using an expression and iteration, optionally with a filter.

```python
squares = []
for number in range(1, 5):
    squares.append(number ** 2)

# Equivalent:
squares = [number ** 2 for number in range(1, 5)]
# [1, 4, 9, 16]
```

```python
evens = [number for number in range(10) if number % 2 == 0]
# [0, 2, 4, 6, 8]

second_column = [row[1] for row in matrix]
# [2, 5, 8]
```

The expression comes first, then the for clause, then an optional filter. Use an ordinary loop when a comprehension becomes difficult to read. Shorter code is not automatically better.
