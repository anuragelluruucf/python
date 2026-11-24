# November 16, 2026 — Files, databases and modules

Exception handling is included from the supplied lesson notebook and the November 17 recap; the detailed explanation is missing from the available November 16 audio.

## File handling

Monday connects Python to the project's ingestion stage: read source files and write their contents into tables.

### Paths and the working directory

Use your own file paths, or keep a practice file in the notebook working directory.

```python
import os
print(os.getcwd())
```

A relative path such as `practice.txt` is resolved from the process's current working directory, which is not always the folder you assume.

### Open modes

| Mode | Meaning | Main caution |
|---|---|---|
| r | Read existing content | File must exist |
| w | Write a new file or overwrite an existing one | Existing content is truncated when opened |
| a | Append at the end | Does not clear existing content |
| r+ | Read and write an existing file | Writes occur at the current position; not an insert operation |

Practise overwrite operations only on your own disposable practice files.

```python
with open("practice.txt", "w", encoding="utf-8") as file:
    file.write("Hello Python\n")
    file.write("This is line two\n")

with open("practice.txt", "r", encoding="utf-8") as file:
    content = file.read()
print(content)
```

`with` manages the file lifetime and closes it when the block exits, including when an exception occurs. The variable `file` refers to a file object, not the text itself. `read()` obtains text from it.

### Read methods

```python
with open("practice.txt", encoding="utf-8") as file:
    print(file.read(5))  # first five characters
    print(file.readline())  # continue from the current position
```

| Operation | Result |
|---|---|
| read() | Remaining file content as one string |
| read(n) | Up to n characters in text mode |
| readline() | Next line, usually including its newline |
| readlines() | Remaining lines collected into a list |
| for line in file | Iterate over lines without building that whole list |

Repeated reads advance the file position. Reading an exhausted text file returns an empty string.

```python
with open("practice.txt", encoding="utf-8") as file:
    for line in file:
        print(line.rstrip("\n"))
```

The class uses strip to remove whitespace. Remember that strip removes leading and trailing whitespace generally, not just a newline. Use rstrip("\n") when indentation or other spaces must remain.

### Append copy and seek

```python
with open("practice.txt", "a", encoding="utf-8") as file:
    file.write("An appended line\n")

with open("practice.txt", encoding="utf-8") as source:
    with open("practice-copy.txt", "w", encoding="utf-8") as destination:
        for line in source:
            destination.write(line)
```

`seek(0)` returns to the beginning. With r+, reading to the end and then writing appends at the current end position unless you reposition first. Writing in the middle replaces content rather than shifting everything to insert new characters. Text-file positions involve encoding; do not assume every arbitrary numeric offset is safe for all Unicode text.


## Dataframes and file formats

Monday's pandas demonstration is an introduction to file input/output, not the complete pandas course.

```python
import pandas as pd

data = {"Name": ["Ana", "Ben"], "Marks": [85, 90]}
df = pd.DataFrame(data)
df.to_csv("students.csv", index=False)
loaded = pd.read_csv("students.csv")
print(loaded)
```

A DataFrame is a table-like structure with rows and columns. `pd` is an alias for the pandas module. Exporting with index=False prevents the row index from being written as another column. A DataFrame index is not automatically a unique business identifier.

| Format | Read | Write |
|---|---|---|
| CSV | pd.read_csv(...) | df.to_csv(...) |
| Excel | pd.read_excel(...) | df.to_excel(...) |
| JSON | pd.read_json(...) | df.to_json(...) |
| Parquet | pd.read_parquet(...) | df.to_parquet(...) |

Excel and Parquet operations may need optional engine packages. JSON interpretation depends on its structure; a JSON document is not always a dictionary, and not every JSON shape is already a rectangular table.

The PDF discussion distinguished text-based PDFs from scanned images. Extracting image text needs OCR. PyMuPDF and pdfplumber were mentioned; scan extraction and document AI were discussed conceptually. A PowerPoint file is not inherently just an image: it may contain editable text, shapes, images and other objects.


## Python and databases

A connection object is the handle through which Python communicates with a database. SQLite is an embedded database engine and does not require a separate database server process. MySQL uses its own server and connector. Do not interchange their connection libraries.

```python
import sqlite3

connection = sqlite3.connect("practice.db")
try:
    connection.execute(
        "CREATE TABLE IF NOT EXISTS students "
        "(id INTEGER PRIMARY KEY, name TEXT, score INTEGER)"
    )
    connection.execute(
        "INSERT OR REPLACE INTO students VALUES (?, ?, ?)",
        (1, "Ana", 85)
    )
    connection.commit()

    results = connection.execute(
        "SELECT name, score FROM students WHERE score >= ?", (80,)
    )
    for row in results:
        print(row)
finally:
    connection.close()
```

`connect` opens or creates the local SQLite file. `execute` submits the specific SQL statement. `commit` commits the transaction's changes. Iteration consumes query results one row at a time. Closing a connection releases resources. Placeholders bind values without assembling untrusted input into SQL strings.

Use a separate practice database so these examples do not affect project records.

A connection does not mean Python reads every table into memory. Query execution and scanning depend on the SQL and database query plan. A query may scan a table without scanning the entire database.

### Project connection

The practical pipeline discussed Monday is source file → Python reads/cleans it → connection to your chosen database → table inserts → commit → later analytics. Separate data extraction, transformation and loading logic so it is understandable and reusable.

### Database discussion clarifications

There is no universal 100 GB maximum for relational databases, and PostgreSQL is not universally faster than MySQL or SQL Server merely because of parallel processing. Performance depends on workload, indexes, query plans, resources and configuration. PostgreSQL also uses LIMIT; TOP is associated with SQL Server syntax. Do not repeat those broad classroom comparisons as technical rules.


## Modules packages and libraries

A module is typically an importable .py file. A package groups modules under an import namespace; a regular package commonly includes an __init__.py file. A library is a broader term for reusable code, not necessarily a fixed third tier below a module.

Create two files in the same practice folder:

```python
# mymath.py
def add(a, b):
    return a + b
```

```python
# main.py
import mymath
print(mymath.add(5, 3))  # 8
```

Run main.py with the selected Python interpreter. Importing reuses code; copying the function into every file defeats that purpose. Import lookup depends on the interpreter and its search path. Avoid naming your files math.py, random.py or other names that shadow modules you want to import.

Useful standard-library examples:

```python
import math
print(math.ceil(25.7))   # 26
print(math.floor(25.7))  # 25
print(math.sqrt(16))     # 4.0
print(math.factorial(5)) # 120
```

Other examples use random, datetime, os, sys and statistics. `dir(module)` lists available names; `help(module.function)` displays documentation. Installing a third-party package makes it available in an environment; importing it makes its names available to running code. Those are different actions.


## Exceptions and handling failures

Exceptions are errors raised during execution. Handle expected failures explicitly rather than allowing a program to stop without an explanation.

```python
try:
    age = int(input("Enter your age: "))
except ValueError:
    print("Please enter a whole number")
else:
    print("You entered:", age)
finally:
    print("Input attempt finished")
```

Try contains the operation that may fail. Except handles the matching exception. Else runs when the try block completes without an exception. Finally provides cleanup on normal exception/control-flow paths; it is not a guarantee against process termination or a machine crash.

If conversion fails, age may never have been assigned. Do not use it afterward as if conversion succeeded. Prefer specific handlers such as ValueError, FileNotFoundError or OSError rather than a bare except that can hide unrelated errors.

```python
while True:
    try:
        number = int(input("Enter a whole number: "))
    except ValueError:
        print("That was not a whole number. Try again.")
    else:
        break
print(number)
```

A syntax error in the same block normally prevents that block from running; it is not the same as a catchable runtime failure inside it. `printf(Hello)` is syntactically valid but normally raises NameError because those names are undefined.
