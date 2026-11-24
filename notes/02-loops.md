# November 10, 2026 — Loops

## Loops: repeating work

### for: iterate over an iterable

```python
numbers = [10, 20, 30]
total = 0
for number in numbers:
    total += number
print(total)  # 60
```

Execution: total starts at 0, becomes 10, then 30, then 60. The final print is outside the loop, so it executes once.

A for loop consumes an iterable; its length does not need to be known beforehand.

### range(start, stop, step)

```python
list(range(5))         # [0, 1, 2, 3, 4]
list(range(1, 6))      # [1, 2, 3, 4, 5]
list(range(2, 11, 2))  # [2, 4, 6, 8, 10]
list(range(5, 0, -1))  # [5, 4, 3, 2, 1]
```

The stop value is excluded. Defaults: start 0, step 1. A zero step is invalid. `range` is a range object, not a prebuilt list; `list(...)` above makes its values visible.

### while: repeat while a condition is true

```python
number = 1
total = 0
while number <= 5:
    total += number
    number += 1
print(total)  # 15
```

The condition is checked before each iteration. The body may run zero times. Forgetting to update the condition's state can create an infinite loop. A while loop normally stops when the condition becomes false—not when it “gets an error.”

### break, continue and pass

```python
for number in range(1, 6):
    if number == 3:
        break
    print(number)  # 1, 2
```

`break` exits the nearest enclosing loop.

```python
for number in range(1, 6):
    if number == 3:
        continue
    print(number)  # 1, 2, 4, 5
```

`continue` skips the rest of the current iteration. In a while loop, ensure it does not skip the update needed for termination.

```python
for number in range(3):
    pass  # placeholder; performs no operation
```

`pass` neither exits a loop nor skips its remaining statements.
