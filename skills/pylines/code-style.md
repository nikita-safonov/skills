# Code Style

These Python backend conventions supplement the checks configured in [pyproject.toml](pyproject.toml). The configuration
is the source of truth for tool settings; the rules below also contain conventions that the tools may not enforce.

## Setup

1. Install the reference tools with `uv add --dev ruff ty`.
2. Merge the relevant `[tool.ruff]` and `[tool.ty]` sections from [pyproject.toml](pyproject.toml) into the project's
   existing configuration. Preserve unrelated settings and resolve conflicts using the project's conventions.
3. Run the checks in the project's environment:

   ```bash
   uv run ruff check .
   uv run ruff format --check .
   uv run ty check
   ```

### Reference configuration

- Ruff uses a line length of **120** and formats code examples in docstrings.
- `extend-select` adds import sorting (`I`), Pylint checks (`PL`), line-length checks (`E501`), and the listed argument
  and return annotation checks (`ANN001`, `ANN002`, `ANN003`, `ANN201`, `ANN202`, `ANN204`, `ANN205`, `ANN206`) to
  Ruff's defaults. It does not select `ALL`.
- The configured ignore list is `FA`, `PLR0913`, `PLR0917`, `PLR2004`, `PLR2044`, `PLR5501`, `PLW1641`, and `PLW2901`.
  Treat these as explicit overrides; do not re-enable them based on prose elsewhere in the guides.
- ty enables `strict-equality-semantics` and sets `dynamic-function-decorator-return`, `missing-type-argument`,
  `unsound-return-statement`, `redundant-cast`, and `unused-type-ignore-comment` to `error`.
- The reference does not configure mypy, Flake8, a global strict mode, or a requirement for all variables to have
  explicit annotations. Additional typing and design conventions below are reviewed separately from these checks.

## Rules

1.  First and foremost, the following must be observed:

    1.  [PEP 8](https://peps.python.org/pep-0008/)
    2.  [PEP 257](https://peps.python.org/pep-0257/) (when writing docstrings)
    3.  [PEP 526](https://www.python.org/dev/peps/pep-0526/)
    4.  [PEP 484](https://www.python.org/dev/peps/pep-0484/)

2.  Line length: 120 characters.

3.  Import rules:

    1.  Import all standard-library modules as a whole: `import os`, `import typing`.
    2.  Import any module that has more than two imported items as a whole:
        `from my_module import SomeModule, AnotherModule, HelloOne` ==\> `import my_module`.

4.  Keep the code fully typed: annotate function arguments, return values, attributes, and public interfaces. Let the
    type checker infer local value types when they are clear. Use narrow types to express contracts and identify
    incompatible uses; this convention supplements the annotation checks selected in the reference configuration.

    1.  Use ty with the settings from [pyproject.toml](pyproject.toml). Do not add mypy strict mode as a requirement of
        this reference.

    2.  Let ty infer scalar value types where they are evident; use `typing.Final` for bindings intended to remain
        unchanged. Examples:

        ```python
        # ❌ Bad
        some_var: str = 'something going on'
        another_var: int = 5

        # ✅ Good
        some_var: typing.Final = 'something going on'
        another_var: typing.Final = 5
        # The type checker infers the scalar types from their values.
        ```

    3.  Try to narrow types as much as possible:

        ```python
        # ❌ Bad
        SOME_CONST: dict = {'what': 5, 'kek': 'raz'}

        # ✅ Better
        SOME_CONST: dict[str, int | str] = {'what': 5, 'kek': 'raz'}

        # ✅ Even better
        SOME_CONST: typing.Final[dict[str, int | str]] = {'what': 5, 'kek': 'raz'}

        # ✅ Good
        class SomeConstDict(typing.TypedDict):
            what: int
            kek: str

        SOME_CONST: typing.Final[SomeConstDict] = {'what': 5, 'kek': 'raz'}

        # ✅ As precise as possible
        SOME_CONST: typing.Final[dict[typing.Literal['what', 'kek'], typing.Literal[5, 'raz']]] = {'what': 5, 'kek': 'raz'}
        ```

5.  Any access by index (`item[0]`) or key (`item["something"]`) can---and will---fail. Handle such access using
    `try-except`.

    Remember the exception hierarchy and use different errors in different cases:

    ```text
    LookupError
    ├── IndexError
    └── KeyError
    ```

    When using a dictionary, you can also use the `get` method --- `item.get("something")`. It does not raise an
    exception; it returns `None`, which may be appropriate in some scenarios.

6.  How to work with exceptions:

    1.  Avoid writing `except Exception`; it is better to use the most specific exception class possible, otherwise this
        can cause problems. Use `except Exception` only in exceptional cases.

    2.  It makes sense to keep the number of lines between `try` and `except` to one. If that is not possible, you can
        include a few more. This is because the more lines there are, the more likely it is that the handler will catch
        several different kinds of errors.

    3.  Although this contradicts the Zen of Python, it is better to check than to catch an exception (the Zen tells us
        that "it is easier to ask for forgiveness than permission"). In other words, if you need a key, check whether it
        exists using the `in` operator instead of relying on a `KeyError`. This is faster and, more importantly,
        communicates clearly to other developers that nothing has gone wrong---we are following the normal execution
        path.

        Uncle Bob follows the same principle, expressing the idea roughly as follows: if an exception is part of the
        normal workflow, using it is incorrect. Exceptions should be used for exceptional cases.

    4.  Do not write your own exception classes. In a service, raise the framework's built-in exceptions
        (`litestar.exceptions.NotFoundException`, `ValidationException`, and so on); in the rest of the code, use
        standard exceptions (`ValueError`, `LookupError`). Custom classes break integrations:
        [Sentry for Litestar](https://github.com/getsentry/sentry-python/blob/master/sentry_sdk/integrations/litestar.py)
        only ignores 4xx errors for `HTTPException` and reports our custom exception before it can be handled by
        `exception_handlers`. You can create a custom class in two cases:

        1.  For a library or client for an external system: use one base class per package so that calling code can
            catch all of its exceptions with a single `except` (as with `httpx.HTTPError`).
        2.  If you need a custom message or response code: inherit from the framework's exception and do not write a
            separate handler for it.

7.  Write self-documenting code:

    1.  All variable, function, module, and class names (that is, any identifiers and names in code) must be at least 8
        characters long. Names such as `a` and `b` are prohibited.

    2.  Use meaningful variable names; encode semantics (i.e. meaning) in their names. A variable such as `data`
        (everything in this world is data) or `user`, for example, conveys no meaning because it is too generic. Be
        specific---for example, use `public_user`.

    3.  All functions must be named with verbs, since functions do things. Nouns are prohibited, except when used with
        `@property`.

    4.  Never write comments (docstrings). Most comments (and docstrings) are self-evident and unhelpful lines of code
        that make a codebase harder to maintain. Write a comment only when you actually have something to say (for
        example, about very complex, non-obvious behavior). Otherwise, do not write one, and do not feel guilty about
        it.

    5.  Avoid using the `get` prefix in function names. `get` conveys no meaning: in development, every operation is
        either a get or a set. Its meaning is also strongly associated with the concept of a "getter," which is usually
        not what you need. Use `get` when retrieving something from RAM. Examples:

        ```python
        class HttpFetcher:
            # ❌ Bad: does not convey the semantics
            def get_http_result(self):
                self._http_connection.get(...)

        class HttpFetcher:
            # ✅ Good: `fetch` is familiar to frontend developers and is generally understood
            # to describe a process that takes some time.
            def fetch_http_result(self):
                self._http_connection.get(...)

        class SomethingWithCache:
            # ❌ Bad
            def get_cache_key(self, user_id):
                return self._cache_key + str(user_id)

        class SomethingWithCache:
            # ✅ Good
            def build_cache_key(self, user_id):
                return self._cache_key + str(user_id)

        class Something:
            # ✅ Good (the “retrieve from RAM” case, where `get` is appropriate)
            def get_very_important_thing(self):
                return self._one_thing * self._another_thing + self._GIGA_CONST
        ```

        Use different words. Fetch, retrieve, download, parse, build, create, make, prepare, and so on. There are many
        words, and many of them better describe what is happening. If you want to see what happens when the word `get`
        is not restricted, take a look at the Django source code: the prefix is used so extensively that it makes the
        code less understandable.

8.  Immutability helps you write reliable code:

    1.  It makes sense to annotate all variables with `typing.Final` (the standard library module should be imported as
        a whole). Mistakes are often made when changing variables (they are harder to make when merely reading them), so
        immutability helps us automatically eliminate many errors at the static-analysis stage---that is, before the
        code runs. We have a [separate package](https://github.com/vrslev/auto-typing-final) to make this easier.

        For example, instead of:

        ```python
        some_cool_var = 10

        def do_some_important_thing(hello: str) -> None:
            hello_guys = f'{hello}{some_cool_var}'
            print(hello_guys)
        ```

        You can do this:

        ```python
        # The full type will be inferred automatically as typing.Final[typing.Literal[10]]
        some_cool_var: typing.Final = 10

        def do_some_important_thing(hello: str) -> None:
            # The full type will be inferred automatically as typing.Final[str]
            hello_guys: typing.Final = f'{hello}{some_cool_var}'
            print(hello_guys)
        ```

    2.  By default, it makes sense to decorate all classes with `typing.final` (lowercase).

    3.  By default, it makes sense to wrap all dictionaries in `types.MappingProxyType`:

        ```python
        import types
        import typing

        SOME_CONST: typing.Final = types.MappingProxyType({'key': 'value'})
        ```

9.  The [Zen of Python](https://www.python.org/doc/humor/#the-zen-of-python) is not a foundation or a set of rules; it
    is simply a collection of aphorisms on a "Humor" page. Neither Python itself nor its developers follow it in many
    places. Although its ideas are sound, they are not rules or dogmas: they do not contain clear instructions and
    merely suggest "being in favor of everything good." Therefore, quoting excerpts from the Zen is pointless; use
    narrower, more specific rules instead. For example, "explicit is better than implicit" is a slogan (and therefore
    useless). But "name functions with verbs so it is clear what they do" is a rule that follows the spirit of that
    slogan---and it can actually be applied.

10. Rules for writing classes:

    1.  Prefer composition over inheritance. It also makes sense to avoid building large class hierarchies and rely on
        composition instead. The idea is simple: hierarchies are difficult to understand and debug, adhering to the LSP
        principle is also challenging, and writing tests is painful. `typing.final` helps you keep classes smaller.

    2.  Use dataclasses. They help avoid boilerplate and make it easier to implement dependency injection. A base class
        that is suitable in many places (in classes containing business logic, working with data, and so on) looks like
        this:

        ```python
        # `frozen` provides immutability (fewer potential errors)
        # `slots` can reduce memory consumption
        # `kw_only` enforces keyword-only arguments, reducing the likelihood of errors
        @dataclasses.dataclass(kw_only=True, slots=True, frozen=True)
        class HttpClient:
            httpx_connection: httpx.Client

            def fetch_user_balance_from_crm(self, user_uuid: str) -> decimal.Decimal:
                ...
        ```

11. Try using the "invert the condition" technique. It helps reduce nesting:

    ```python
    # ❌ Bad
    def process_something_important(user_payload):
        if user_payload:
            if isinstance(user_payload, list):
                if user_payload and all(isinstance(i, int) for i in user_payload):
                    result = sum(user_payload)
                    if result > 0:
                        print(f"Result: {result}")
                    else:
                        print("Sum is non-positive")
                else:
                    print("Invalid list")
            else:
                print("Invalid data")
        else:
            print("No data")

    # ✅ Good
    def process_something_important(user_payload):
        # This is the inversion technique (we invert the condition that caused the nesting).
        if not user_payload:
            print("No data")
            return
        if not isinstance(user_payload, list):
            print("Invalid data")
            return
        if not user_payload or not all(isinstance(i, int) for i in user_payload):
            print("Invalid list")
            return

        print(f"Result: {sum(user_payload)}" if sum(user_payload) > 0 else "Sum is non-positive")
    ```

12. Regular expressions should always be checked for vulnerabilities with the
    [ReDoS checker](https://devina.io/redos-checker).

13. Anything that goes beyond accessing RAM can fail while being processed. Therefore, we use a retry or resilience
    approach (words that can be used to name such places in code include `retry`, `resilient`, and `robust`):

    1.  We use the [stamina](https://github.com/hynek/stamina) library for retries.
    2.  Standard cases where retries should be used:
        1.  Any SQL query.
        2.  Reading and writing files.
        3.  Outgoing HTTP requests.
        4.  Receiving and sending data in consumers/producers.
    3.  The number of retries should be limited, and the interval between attempts should be randomized; otherwise, you
        can bring down the service(s).
    4.  If there is a risk of "bringing down" a service with retries (for example, due to high load),
        [circuit breaker](https://github.com/community-of-python/circuit-breaker-box) can help.

14. Avoid magic, i.e. implicit behavior:

    1.  `hasattr` and `getattr` are signs of this kind of behavior, but they are not the only ones.
    2.  Magic numbers---that is, anything other than zero or one---are prohibited. They must be moved either to a global
        `settings.py` file or to module-level constants with semantic names. This applies to all data types: numbers,
        strings, arrays, and so on.

15. Do not create temporary variables without a reason. Variables are needed only when the name adds meaning or the
    value is reused.

    ```python
    # ❌ Bad (unnecessary variable creation)
    def something_kek():
        result = do_another_job()
        return result

    # ✅ Good
    def something_kek():
        return do_another_job()

    # ❌ Bad (`config_path` is not used anywhere else)
    config_path = Path("config.json")
    config_path.write_text("{}")

    # ✅ Good
    Path("config.json").write_text("{}")
    ```

16. Try to keep functions, classes, and modules small. 20--40 lines per function, 100 lines per class, and 200--300
    characters per module are the maximum, but smaller entities are preferable.
