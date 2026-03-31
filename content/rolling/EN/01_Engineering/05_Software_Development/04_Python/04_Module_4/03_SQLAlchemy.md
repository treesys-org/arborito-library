@title: SQLAlchemy 2 ORM
@icon: 🗄️
@description: Engine, Session, declarative models.
@order: 3

# SQLAlchemy 2 ORM

This lesson deepens **SQLAlchemy 2 ORM** with runnable Python 3.11+ examples.

@section: Context

Python favors readability: code should express intent clearly, from scripts to production services.

@section: Minimal example

```python
def demo() -> None:
    """Docstring helps tools and humans."""
    print("Hello from Python")

if __name__ == "__main__":
    demo()
```

@section: Practices

* Use virtual environments (`python -m venv`) to isolate dependencies.
* Format with **ruff** or **black**; type gradually with **mypy** on larger projects.
* Avoid import-time side effects; initialize in `main()`.

@section: Pitfalls

* Mutable default arguments (`def f(x=[])`).
* Late-binding closures in lambdas inside loops without explicit defaults.
* Mixing `bytes` and `str` without explicit decoding.

@section: Lab

1. Create a small module and run it as `python -m package.module`.
2. Add a tiny `pytest` if the topic fits.
@quiz: Which standard tool creates an isolated virtual environment?
@option: conda only
@correct: python -m venv
@option: global pip install
