# November 17, 2026 — Classes and objects

## Object oriented programming

A class describes a kind of object. An object is an instance of that class. Attributes store state; methods implement behavior.

```python
class Person:
    def __init__(self, name, birth_year):
        self.name = name
        self.birth_year = birth_year

    def age_in(self, year):
        return year - self.birth_year

    def __str__(self):
        return f"{self.name}, born in {self.birth_year}"

person = Person("Ana", 2000)
print(person.name)         # Ana
print(person.age_in(2026)) # 26
print(person)             # Ana, born in 2000
```

### Reading the syntax

Class names conventionally use CapWords, such as Person or StudentRecord—not necessarily all capitals. `__init__` initializes an instance after creation. `self` refers to the instance receiving the method call. `self.name` is an attribute; the parameter name supplies its value. Dot notation accesses an attribute or method. Calling `person.age_in(2026)` passes person as self automatically.

The name self is a convention, not a reserved keyword. Use self consistently for readability. `__str__` must return a string; it should not merely print. `__repr__` is typically a more diagnostic representation.

### Initialization and missing attributes

An empty class can receive attributes later, but accessing an attribute before it is assigned raises AttributeError. Initialize required state predictably. Classes without a custom __init__ are valid; they are not automatically poor design.

### Instance and class attributes

```python
class Student:
    school = "ABC School"

    def __init__(self, name):
        self.name = name

first = Student("Ana")
second = Student("Ben")
Student.school = "XYZ School"
print(first.school)   # XYZ School
print(second.school)  # XYZ School

first.school = "Different School"
print(first.school)   # Different School
print(second.school)  # XYZ School
```

A class attribute belongs to the class namespace; it is not the same as a module-level global variable. The class attribute supplies a shared default. Assigning first.school normally creates an instance attribute that shadows it. Be especially careful with mutable class attributes such as lists, since instances may accidentally share their contents.

### Method types

| Method | Automatic first argument | Typical use |
|---|---|---|
| Instance method | self, the instance | Operate on that object's state |
| Class method | cls, the class | Class-level behavior or alternate constructors |
| Static method | None | Related utility without an implicit instance/class |

```python
class Counter:
    created = 0

    def __init__(self):
        Counter.created += 1

    @classmethod
    def total(cls):
        return cls.created

    @staticmethod
    def add(a, b):
        return a + b

one = Counter()
print(Counter.total()) # 1
print(Counter.add(2, 3)) # 5
```

A class method cannot obtain an arbitrary instance-only attribute through cls. A static method can access objects explicitly passed to it; saying it can never access instance or class data is too broad.
