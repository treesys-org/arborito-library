
@title: Certification Exam: Applied Python
@exam
@icon: 🐍
@description: Covers syntax, OOP, the data stack, and production APIs with Python 3.11+. Pass the majority to certify the track.
@order: 5

# Final exam: Applied Python

This exam spans all four modules: core language, object-oriented design, numerical stack, and APIs in production.

> **Instructions:** Pick the single best answer per question. You need a majority correct to pass.


## Block 1: Foundations (module 1)

@quiz: In Python 3, what defines code blocks instead of curly braces `{}`?
@option: `begin` / `end` keywords.
@correct: Consistent indentation.
@option: `#region` markers.
@option: The colon `:` alone with no indentation.

@quiz: What does `if __name__ == "__main__":` do in a script?
@option: Imports the module as a package.
@correct: Runs code only when the file is executed directly, not when imported.
@option: Declares the C interpreter entry point.
@option: Enables the `-O` optimized mode.

@quiz: What is an idiomatic way to interpolate variables in a modern Python string?
@option: `"Hello " + name + "!"` exclusively.
@correct: f-strings (`f"Hello {name}!"`).
@option: Only `str.format` with no alternatives.
@option: Native C-style `printf`.

@quiz: Which statement about lists and tuples is correct?
@option: Tuples always use less memory than lists of the same length.
@correct: Lists are mutable; tuples are immutable (the tuple object is not changed in place).
@option: Neither can hold mixed types.
@option: Both only accept numbers.

@quiz: What is risky about using a mutable list as a default argument value?
@option: It raises `SyntaxError` when defining the function.
@correct: The same object is reused across calls, accumulating unexpected state.
@option: Python silently converts it to a tuple.
@option: It only matters in the REPL.

@quiz: In Python 3.7+, a `dict` typically preserves:
@option: Random key order every run.
@correct: Insertion order of keys.
@option: Forced alphabetical key order.
@option: Only sorted numeric keys.


## Block 2: OOP and quality (module 2)

@quiz: Which decorator defines a method that does not receive an instance `self`?
@option: `@instancemethod`
@correct: `@staticmethod` (or `@classmethod` if it receives `cls`).
@option: `@abstract`
@option: `@private`

@quiz: With multiple inheritance, what determines method lookup order?
@option: Alphabetical order of base class names.
@correct: MRO (Method Resolution Order), inspectable via `ClassName.__mro__`.
@option: Only the first listed base class.
@option: Random at runtime.

@quiz: What is the main purpose of `dataclass`?
@option: Make the class always immutable.
@correct: Reduce boilerplate for classes that are mostly data containers.
@option: Replace Pydantic for runtime validation.
@option: Compile the class to a C extension.

@quiz: What does `raise ... from e` do when chaining exceptions?
@option: Drops the original exception.
@correct: Preserves context (`__cause__`) for clearer tracebacks.
@option: Downgrades the exception to a warning.
@option: Only works inside `async def`.

@quiz: What best describes a Python decorator?
@option: A special comment for the linter.
@correct: A callable that wraps another function or method to add behavior.
@option: A mandatory type annotation.
@option: An access modifier like Java.

@quiz: Python type hints are primarily:
@option: Required at runtime for the interpreter.
@correct: Optional annotations used by tools (mypy, IDEs) and documentation.
@option: Runtime-enforced generics like Java.
@option: The same as C `typedef` with automatic enforcement.


## Block 3: NumPy, Pandas, environment (module 3)

@quiz: Which Pandas structure models a single labeled, homogeneous column?
@option: Always a `DataFrame`.
@correct: A `Series`.
@option: Only a `Panel`.
@option: An `ndarray` with implicit index.

@quiz: In NumPy, what is the main benefit of vectorization over pure Python loops on large arrays?
@option: Always lower memory use.
@correct: Work is delegated to optimized C/Fortran-like code and runs much faster.
@option: Mandatory GPU compatibility.
@option: Per-element dynamic typing.

@quiz: What is the standard way to create an isolated virtual environment in Python 3?
@option: `pip env create`
@correct: `python -m venv .venv` (or another path).
@option: `virtualenv` is the only valid tool today.
@option: `conda init` is mandatory.

@quiz: Which function is commonly used to read a CSV in Pandas?
@option: `pd.load_csv`
@correct: `pd.read_csv`.
@option: `pd.open_csv`
@option: `pd.import_table`

@quiz: For ethical scraping, what practice is essential?
@option: Ignore `robots.txt` if the site is public.
@correct: Respect terms of use, `robots.txt`, and avoid hammering servers (rate limits).
@option: Always use unlimited threads.
@option: Store personal data without a legal basis.

@quiz: Matplotlib is typically used via:
@option: `plt.begin()` / `plt.commit()`
@correct: `pyplot` helpers or the object-oriented API with `Figure` and `Axes`.
@option: Embedded SVG only in HTML.
@option: Jupyter exclusively.


## Block 4: APIs, async, testing (module 4)

@quiz: In FastAPI, what role does Pydantic play in routes?
@option: Static documentation only.
@correct: Validation and parsing of bodies and parameters into typed models.
@option: It replaces the ASGI server.
@option: It only manages DB connection pools.

@quiz: Which HTTP status commonly signals successful creation of a new resource in REST?
@option: Always `200 OK`.
@correct: `201 Created` (common when an entity is created).
@option: `204 No Content` for every write.
@option: `302 Found`

@quiz: In `asyncio`, which keyword yields control while waiting for I/O without blocking the thread?
@option: `yield` in any function.
@correct: `await` inside an `async def` function.
@option: Go-style `defer`.
@option: Bare `wait` without `async`.

@quiz: Which tool is very common for unit tests in modern Python?
@option: Only plain `unittest` with no extensions.
@correct: `pytest` (among others; `unittest` remains valid).
@option: `maven test`
@option: Native Python `jest`.

@quiz: SQLAlchemy 2 ORM typically maps tables to:
@option: Plain dicts only.
@correct: Python classes with mapped columns and sessions.
@option: Only raw SQL strings.
@option: Intermediate JSON files.

@quiz: In REST design, which HTTP method is idempotent and classically used to replace an entire resource?
@option: `POST`
@correct: `PUT` (full replacement; `PATCH` is partial).
@option: `CONNECT`
@option: `TRACE`
