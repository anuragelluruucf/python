# November 18, 2026 — Inheritance and OOP design

The inheritance example below continues the `Person` class from the previous class.

## Inheritance and overriding

Inheritance models an is-a relationship: a Student is a Person. It allows reuse and specialization.

```python
class Student(Person):
    def __init__(self, name, birth_year, student_id):
        super().__init__(name, birth_year)
        self.student_id = student_id

    def __str__(self):
        return f"{super().__str__()}, student ID {self.student_id}"

student = Student("Ana", 2000, 101)
print(student.age_in(2026))
print(student)
print(isinstance(student, Person))  # True
```

Super delegates according to the method resolution order. In this simple case it invokes the parent implementation. Overriding defines a method in the subclass with the same name as an inherited method.

`*args` collects extra positional arguments; `**kwargs` collects extra keyword arguments. They collect a tuple and dictionary respectively. Learn the straightforward explicit-parameter version first.


## Inheritance patterns and method resolution

Wednesday also distinguishes single inheritance (one parent), multiple inheritance (several direct parents), multilevel inheritance (a chain), hierarchical inheritance (several subclasses share a parent), and hybrid combinations. These describe class relationships, not different keywords.

A student asked what happens when two parents define the same method. Python resolves lookup using the class's method resolution order, or MRO:

```python
class A:
    def show(self):
        return "A"

class B:
    def show(self):
        return "B"

class C(A, B):
    pass

print(C().show())  # A
print(C.__mro__)   # C, A, B, object in this simple hierarchy
```

For this simple example reversing the bases to C(B, A) selects B's method instead. In more complex inheritance, inspect the MRO rather than assuming a universal left-to-right shortcut. Super follows that MRO; it does not always mean “call my one direct parent.” If C defines show itself, its override is found first. Defining a method in two different parent classes is valid and is not the same as traditional overloads selected by argument signatures.

### Extra positional and keyword arguments

```python
def describe(a, *args, **kwargs):
    print(a)
    print(args)    # tuple of extra positional arguments
    print(kwargs)  # dictionary of keyword arguments

describe(1, 2, 3, city="Orlando")
```

Here a is 1, args is (2, 3), and kwargs is {"city": "Orlando"}. In a call such as parent_method(*args), the star expands a sequence into individual positional arguments. Collecting arguments in a definition and unpacking them in a call are different uses of the same notation.


## Encapsulation composition and polymorphism

Encapsulation organizes state and operations behind an interface. `_name` signals an internal attribute by convention. `__name` triggers name mangling, usually to `_ClassName__name`; it is not encryption or an absolute privacy boundary. `__dict__` exposes many ordinary instance attributes for inspection.

Composition models a has-a relationship: a Car has an Engine.

```python
class Engine:
    def start(self):
        return "Engine started"

class Car:
    def __init__(self):
        self.engine = Engine()

    def start(self):
        return self.engine.start()

print(Car().start())
```

A text wrapper example adds bold or italic rendering around another object's render method. This is an object-wrapper/decorator pattern; distinguish it from Python's @function_decorator syntax.

Polymorphism lets the same operation work with different objects. For example, + adds numbers but concatenates strings or lists. Duck typing means using an object's supported behavior instead of requiring one exact concrete type. Dynamic typing and duck typing are related concepts, not identical definitions.


## Decorators dataclasses and responsibility

These topics extend the OOP material from November 18.

```python
def announce(function):
    def wrapper():
        print("Before")
        function()
        print("After")
    return wrapper

@announce
def greet():
    print("Hello")

greet()
```

The decorator receives a function and returns a replacement. `@announce` is equivalent here to `greet = announce(greet)`. A general-purpose wrapper often also needs *args, **kwargs and functools.wraps; this example intentionally keeps the classroom shape.

```python
from dataclasses import dataclass

@dataclass
class Profile:
    name: str
    age: int

    def __post_init__(self):
        if self.age < 0:
            raise ValueError("Age cannot be negative")
        self.name = self.name.title()

print(Profile("ana", 25))
```

Dataclass can generate initialization and representation methods. Post-init runs after its generated initializer. Type annotations alone do not enforce types at runtime; validation must be added where needed.

For validation, string methods such as isdigit test characters in a string, not the type of an arbitrary integer object. Use explicit type and value checks appropriate to your inputs.

The Single Responsibility Principle asks a class to have one coherent responsibility. The notebook separates generating a Report from saving it with ReportSaver. It is about reasons to change, not a maximum number of lines.
