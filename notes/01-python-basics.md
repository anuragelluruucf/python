# November 9, 2026 — Python basics

## Python and SQL

Python is a general-purpose programming language used for automation, applications, data processing and analysis. SQL is primarily a language for querying and managing relational data. They work together: SQL retrieves records; Python can transform them and apply application logic.

Ordinary Python statements execute in order, but conditions, loops and function calls alter the execution flow. Python supports multiple programming styles, not only procedural programming.

```python
print("Hello, Python")
print(10 + 5)  # 15
```

A comment starts with `#`. It explains code and is not executed. Indentation defines blocks; consistently use four spaces.


## Variables, assignment and names

A variable name refers to an object. `=` assigns a reference; it is not an equality comparison.

```python
age = 25
name = "Anurag"
age = 26
print(age)  # 26
```

Names are case-sensitive. Use meaningful snake_case names such as `total_marks`. A name can contain letters, underscores and digits, but cannot begin with a digit. Keywords such as `if` and `for` cannot be variable names. Avoid shadowing built-ins such as `list`, `sum`, `str` and `print`; it is technically possible but makes those built-ins inconvenient to use afterward.

### Dynamic typing versus strong typing

```python
value = 10
value = "ten"  # Rebinding the name is allowed.
```

Dynamic typing means you do not declare a fixed variable type. Objects still have types. Strong typing means incompatible operations are not automatically made compatible:

```python
# "Age: " + 25  # TypeError
message = "Age: " + str(25)
```


## Basic data types

| Type | Example | Meaning |
|---|---|---|
| int | `42` | Integer |
| float | `3.5` | Floating-point number |
| complex | `2 + 3j` | Complex number; Python uses j |
| bool | `True`, `False` | Boolean value; spelling/capitalization matter |
| str | `"Python"` | Text |

```python
print(type(42))    # <class 'int'>
print(type(3.5))   # <class 'float'>
print(type("42"))  # <class 'str'>
```

`42` and `"42"` are different objects with different types. Arithmetic and text concatenation therefore behave differently.


## Type conversion / casting

### Implicit conversion

Python automatically converts compatible numeric operands where needed:

```python
answer = 4 + 2.5
print(answer)        # 6.5
print(type(answer))  # <class 'float'>
```

It does not automatically convert every string to a number.

### Explicit conversion

```python
int("25")     # 25
float("2.5")  # 2.5
str(25)       # '25'
int(3.9)      # 3; truncates toward zero, does not round
```

`int("2.5")` raises ValueError. If appropriate, use `float("2.5")` first, then consider whether discarding its fractional part is acceptable.

### Boolean conversion

```python
bool(0)        # False
bool("")       # False
bool([])       # False
bool("False")  # True: this is a nonempty string
```

An empty collection is false in a condition; a nonempty collection is generally true. The text `"False"` is not the Boolean `False`.


## Strings, quotes and printing

Single and double quotes both create strings. Choose the outer quotes to accommodate quotes inside, or escape them.

```python
print("It's Python")
print('He said "hello"')
print('It\'s Python')
print("first\nsecond")  # newline
print("name\tmarks")    # tab
```

### Formatting values

```python
name = "Anurag"
marks = 85
print("{} scored {}".format(name, marks))
print(f"{name} scored {marks}")
```

Both produce `Anurag scored 85`. In an f-string, expressions inside braces are evaluated. Formatting avoids manually converting each numeric value when constructing readable messages.


## Operators

### Arithmetic

For `a = 10`, `b = 3`:

| Expression | Result | Meaning |
|---|---|---|
| a + b | 13 | Addition |
| a - b | 7 | Subtraction |
| a * b | 30 | Multiplication |
| a / b | approximately 3.3333 | Division |
| a // b | 3 | Floor division |
| a % b | 1 | Remainder |
| a ** b | 1000 | Exponentiation |

Note: floor division rounds toward negative infinity: `-7 // 2` is `-4`. It does not simply remove the decimal part. Use parentheses to make intended calculation order clear.

### Comparison and assignment

`==`, `!=`, `<`, `<=`, `>`, `>=` compare values. `=` assigns a value.

```python
score = 75
print(score >= 50)  # True
score += 5          # score = score + 5
```

### Logical operators

| A | B | A and B | A or B |
|---|---|---|---|
| True | True | True | True |
| True | False | False | True |
| False | True | False | True |
| False | False | False | False |

`not True` is False; `not False` is True.

```python
age = 22
has_id = True
eligible = age >= 18 and has_id
```

Note: with arbitrary objects, `and` and `or` return an operand, not necessarily a Boolean. For introductory conditions, focus first on Boolean comparisons.


## Conditional branching

Use conditions to select which block runs.

```python
score = 72
if score >= 80:
    print("Distinction")
elif score >= 50:
    print("Pass")
else:
    print("Needs improvement")
```

Only the first matching branch in this chain executes. Put more restrictive conditions first. Every header ends with a colon; its body is indented.

### Nested conditions

```python
age = 22
has_id = True
if age >= 18:
    if has_id:
        print("Eligible")
    else:
        print("Bring identification")
else:
    print("Age requirement not met")
```

Indentation determines which `if` an `else` belongs to.
