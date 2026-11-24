# November 13, 2026 — Functions, iterators and generators

## Functions and reusable logic

A function groups instructions so you can call the same logic with different inputs. Defining a function does not execute its body. Calling it does.

```python
def add_numbers(a, b):
    return a + b

answer = add_numbers(4, 6)
print(answer)  # 10
```

`def` introduces the definition; `add_numbers` is the name; `a` and `b` are parameters. The values `4` and `6` are arguments supplied during the call. The indented body calculates a result. `return` sends the result back to the caller and ends that function call.

### Print versus return

```python
def show_total(a, b):
    print(a + b)

def calculate_total(a, b):
    return a + b

first = show_total(2, 3)       # displays 5
second = calculate_total(2, 3)
print(first)   # None
print(second)  # 5
```

Print is for displaying output. Return is for passing a value back for storage, calculation or use by another function. A function without an explicit return produces None. Returning an expression does not automatically print it in a Python script; notebooks may display a final expression automatically.

### A function with conditions and loops

```python
def is_prime(number):
    if number < 2:
        return False
    for divisor in range(2, number):
        if number % divisor == 0:
            return False
    return True

print(is_prime(7))  # True
print(is_prime(9))  # False
```

`%` finds the remainder; `== 0` checks whether it is zero. Finding a divisor other than 1 and the number itself proves the number is not prime. The final return must sit outside the loop: return True only after all candidate divisors have been checked. This deliberately uses the straightforward beginner approach.


## Iterables iterators and generators

An iterable can supply values one at a time. Lists and strings are examples. An iterator tracks progress through those values.

```python
numbers = [10, 20, 30]
iterator = iter(numbers)
print(next(iterator))  # 10
print(next(iterator))  # 20
print(next(iterator))  # 30
# next(iterator) now raises StopIteration.
```

`iter` obtains an iterator. `next` obtains its next value. A for loop handles this progression and normal exhaustion automatically. You do not normally need to call next manually in an ordinary loop.

A generator function uses yield to produce values lazily:

```python
def cubes(limit):
    for number in range(limit):
        yield number ** 3

for result in cubes(4):
    print(result)  # 0, 1, 8, 27 on separate lines
```

Yield pauses the function and saves its state. The next request resumes it. Return ends the call instead. Calling `cubes(4)` creates a generator object; it does not immediately build a list of all cubes. An exhausted generator must be recreated to traverse the sequence again.


## Lambda map filter and reduce

A lambda creates a small function containing one expression.

```python
add_five = lambda number: number + 5
print(add_five(3))  # 8
```

Named functions are usually clearer for complicated logic. Lambda is useful when passing a short operation to another function.

### Map transforms each value

```python
numbers = [1, 2, 3, 4]
squares = list(map(lambda number: number ** 2, numbers))
print(squares)  # [1, 4, 9, 16]
```

In Python 3, map returns an iterator. `list(...)` collects its output. When mapping over several iterables, ordinary map stops at the shortest; equal lengths are not universally required.

### Filter retains selected values

```python
numbers = [1, 2, 3, 4]
evens = list(filter(lambda number: number % 2 == 0, numbers))
print(evens)  # [2, 4]
```

Map of this condition produces `[False, True, False, True]`; filter produces the original values that satisfy it. Compare the results to decide whether you need transformed values or selected original values.

### Reduce combines values

```python
from functools import reduce

total = reduce(lambda a, b: a + b, [1, 2, 3, 4])
print(total)  # 10
```

Think of the steps as 1+2=3, then 3+3=6, then 6+4=10. Reduce returns one accumulated value. In Python 3, import it from functools. Prefer a clearer built-in such as sum when it expresses your task directly.
