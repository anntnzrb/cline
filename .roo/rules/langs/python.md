# Python Development Guidelines: python.md

## 1. Guiding Principles

- **Idiomatic Python:** Write Pythonic code that leverages the language's strengths and common patterns. Favor built-in functions and standard library modules like `functools` and `itertools`.

## 2. Project Setup & Dependency Management

### 2.1. Preferred Tool: `uv`
*   **Primary Tool:** We prefer `uv` for Python packaging and project management. It combines the functionality of `pip`, `pip-tools`, and `venv` into a single, fast tool.
*   **Project Initialization:** New projects should be set up using `uv`.
    ```bash
    # example: create and activate a virtual environment
    uv venv
    source .venv/bin/activate # or .venv\Scripts\activate on Windows
    ```
*   **Dependency Management:**
    *   Use `uv add <pkg1> <pkg2>` to add dependencies.
    *   Dependencies should be managed in `pyproject.toml`.
    *   `uv pip freeze > requirements.txt` can be used for compatibility if needed, but `pyproject.toml` is primary.
    *   `uv lock` should generate a `uv.lock` file.
*   **Identifying `uv`-enabled Projects:**
    *   A project is considered `uv`-enabled if a `uv.lock` file is present at the top-level directory.
    *   The `pyproject.toml` file should also reflect `uv`-compatible configurations if specific build backends or project metadata are managed by it.

### 2.2. Virtual Environments
*   **Mandatory:** Always use virtual environments to isolate project dependencies. `uv venv` is the preferred way to create them.
*   **Naming:** The virtual environment directory should be named `.venv` and added to `.gitignore`.

### 2.3. `pyproject.toml`
*   **Central Configuration:** Use `pyproject.toml` (PEP 518, PEP 621) for project metadata (name, version, authors), dependencies, and tool configuration (e.g., `ruff`, `pytest`).

## 3. Code Style, Formatting & Linting

### 3.1. Formatter: `ruff format`
*   **Standard:** `ruff format` is the official code formatter.
*   **Enforcement:** Code should be formatted with `ruff format` before committing.
*   **Configuration:** Configure `ruff format` options in `pyproject.toml` or `ruff.toml` if deviations from defaults are necessary.
    ```toml
    # pyproject.toml example
    [tool.ruff]
    line-length = 88 # Example

    [tool.ruff.format]
    quote-style = "double"
    # ... other format-specific options
    ```

### 3.2. Linter: `ruff check`
*   **Standard:** `ruff check` is the linter for identifying potential errors, style issues, and anti-patterns.
*   **Enforcement:** Code should pass `ruff check` (with configured rules) without errors or warnings before committing.
*   **Configuration:**
    *   Select and configure rules in `pyproject.toml` or `ruff.toml`. Start with a sensible default set (e.g., `ruff`'s defaults, `flake8` compatibility) and extend as needed.
    *   Enable `fix = true` for auto-fixable issues where appropriate.
    ```toml
    # pyproject.toml example
    [tool.ruff]
    select = ["E", "F", "W", "I", "N", "UP", "B", "A", "C4", "T20", "PYI"] # Example rule set
    ignore = [] # Specific rules to ignore
    # ... other ruff options

    [tool.ruff.lint]
    # ... lint-specific options
    ```

### 3.3. Naming Conventions
*   Follow PEP 8 naming conventions:
    *   `lowercase` for functions, methods, variables, and modules.
    *   `UPPERCASE` for constants.
    *   `CapWords` (PascalCase) for classes.
    *   Protected members prefixed with a single underscore (`_protected_member`).
    *   Private members (name-mangled) prefixed with a double underscore (`__private_member`).

## 4. Idiomatic Python & Abstractions

### 4.1. Leverage `functools` and `itertools`
*   **`functools`:**
    *   Use `functools.lru_cache` for caching results of expensive function calls.
    *   Use `functools.partial` to create specialized versions of functions.
    *   Use `functools.wraps` in decorators to preserve metadata of the wrapped function.
    *   Explore other utilities like `total_ordering`, `singledispatch`.
*   **`itertools`:**
    *   Prefer `itertools` functions (e.g., `chain`, `islice`, `cycle`, `tee`, `zip_longest`, `product`, `combinations`, `permutations`) for efficient iteration and complex looping logic over manual implementations.

### 4.2. Comprehensions & Generators
*   **Comprehensions:** Use list, dictionary, and set comprehensions for creating collections concisely and readably.
    ```python
    # Good
    squares = [x*x for x in range(10)]
    squares_dict = {x: x*x for x in range(10)}
    unique_letters = {char for char in "hello world"}
    ```
*   **Generator Expressions:** Use generator expressions for memory-efficient iteration, especially with large datasets.
    ```python
    # Good
    sum_of_squares = sum(x*x for x in range(1000000))
    ```
*   **Generator Functions:** Use `yield` to create generator functions for custom iterable sequences, especially for lazy evaluation.

### 4.3. Context Managers (`with` statement)
*   Always use the `with` statement for resources that need to be managed (e.g., files, locks, database connections) to ensure proper acquisition and release.

### 4.4. Type Hinting (PEP 484)
*   **Strongly Recommended:** Use type hints for all new code to improve code clarity, catch errors early, and aid tooling.

### 4.5. Avoid Mutable Default Arguments
*   Be cautious with mutable default arguments (e.g., `list`, `dict`) in function definitions. Use `None` as a default and initialize the mutable type inside the function.
    ```python
    # Bad
    # def foo(a, L=[]):
    # L.append(a)
    # return L

    # Good
    def foo(a, L=None):
      if L is None:
        L = []
      L.append(a)
      return L
    ```

## 5. Testing

### 5.1. Framework: `pytest`
*   **Standard:** `pytest` is the preferred testing framework for its conciseness, powerful features (fixtures, parametrization), and extensive plugin ecosystem.
*   **Test Location:** Store tests in a `tests/` directory, mirroring your application's structure.
*   **Naming:** Test files should be named `test_*.py` or `*_test.py`. Test functions should be prefixed with `test_`.

### 5.3. Types of Tests
*   **Unit Tests:** Test individual functions and classes in isolation.
*   **Integration Tests:** Test interactions between components.

## 6. Error Handling & Logging

### 6.1. Exceptions
*   **Be Specific:** Catch specific exceptions rather than generic `Exception`.
*   **Avoid Swallowing Exceptions:** Do not silently pass exceptions unless explicitly intended and documented.

### 6.2. Logging
*   **Standard Library `logging`:** Use the built-in `logging` module.
*   **Configuration:** Configure logging levels and handlers appropriately for different environments (development, production).
*   **Contextual Logging:** Include relevant contextual information in log messages.

## 7. Documentation

### 7.1. Docstrings
*   **Mandatory:** Write clear and concise docstrings for all public modules, classes, functions, and methods.
*   **Style:** Follow a consistent docstring style (e.g., Google, NumPy, or reStructuredText/Sphinx). `ruff` can lint for some docstring conventions (e.g., `D` rules from `pydocstyle`).
    ```python
    def my_function(param1: int, param2: str) -> bool:
        """
        Summarize the function's purpose in one line.

        Provide a more detailed explanation if necessary.

        Args:
            param1: Description of the first parameter.
            param2: Description of the second parameter.

        Returns:
            Description of the return value.
        """
        # ... function body ...
        return True
    ```
