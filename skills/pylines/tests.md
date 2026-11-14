# How to Write Tests

1.  The pattern used by most tests is usually AAA: arrange (set things
    up), act (do something), assert (check the result).

    ``` python
    import typing

    import pytest
    from litestar.testing import TestClient

    from myapp import create_application
    from myapp.services import UserService


    def test_should_create_user_successfully() -> None:
        # Arrange
        client: typing.Final = TestClient(app=create_application())
        user_email: typing.Final = "test@example.com"
        user_payload: typing.Final = {"email": user_email, "password": "securepass123"}

        # Act
        response: typing.Final = client.post("/api/users", json=user_payload)

        # Assert
        assert response.status_code == 201
        assert response.json()["email"] == user_email
    ```

2.  **Writing unit tests is prohibited, except for the rare exceptions
    listed below.** A test must go through the highest-level interface
    available: an API endpoint, a CLI command, or a message handler. The
    application must be assembled in full in the test (such tests are
    also called integration tests or sociable tests).

    -   The test calls an endpoint and checks the response and
        observable outcome: returned data, records in storage, and
        outgoing calls.
    -   Give priority to tests with maximum coverage. A single test
        should exercise as much of the code as possible: through all
        endpoint layers, down to storage and external calls. Do not
        write several narrow tests if they can be replaced with one
        broad test.
    -   Do not test individual components of an endpoint (services,
        repositories, validators, helpers) separately. Their behavior
        must be checked through the endpoint.
    -   If a code branch cannot be reached through an endpoint, first
        determine whether that branch is needed at all.
    -   A unit test is acceptable only when testing through the
        highest-level interface is impossible or disproportionately
        expensive. Example: a complex, pure algorithm with dozens of
        edge cases. Such a test is an exception, and its necessity must
        be justified during code review.

    **Reason:** Unit tests are tied to the internal structure of the
    code. Any structural change requires rewriting the tests, even if
    the behavior has not changed. Tests through an endpoint check what
    the client can see and do not break during refactoring.

    ``` python
    import typing

    from litestar.testing import TestClient

    from myapp import create_application


    # ❌ Bad: a unit test for an individual method
    def test_should_validate_email_format() -> None:
        from myapp.validators import EmailValidator

        validator: typing.Final = EmailValidator()
        assert validator.is_valid_email("test@example.com")


    # ✅ Good: an integration test through the API
    def test_should_reject_invalid_email_via_api() -> None:
        client: typing.Final = TestClient(app=create_application())
        invalid_payload: typing.Final = {"email": "invalid-email", "password": "pass123"}

        response: typing.Final = client.post("/api/users", json=invalid_payload)

        assert response.status_code == 422
        assert "email" in response.json()["detail"]
    ```

    This testing approach can be called a testing "diamond" rather than
    a testing "pyramid." Unit tests provide little value, while
    end-to-end tests are very expensive and heavy. Integration tests, on
    the other hand, cover most needs quite well (at least in backend
    development for web applications).

3.  Use data generation instead of writing static scalar values (such as
    strings or numbers). Use `faker` wherever possible. Also, where
    possible, use
    [Hypothesis](https://github.com/HypothesisWorks/hypothesis) for key
    cases. It makes your tests significantly more reliable.

    ``` python
    import typing

    import hypothesis.strategies as st
    from faker import Faker
    from hypothesis import given
    from litestar.testing import TestClient

    from myapp import create_application

    fake: typing.Final = Faker()


    # ❌ Bad: static data
    def test_should_create_user_with_static_data() -> None:
        client: typing.Final = TestClient(app=create_application())
        response: typing.Final = client.post("/api/users", json={"email": "test@test.com", "age": 25})
        assert response.status_code == 201


    # ✅ Good: using Faker
    def test_should_create_user_with_faker() -> None:
        client: typing.Final = TestClient(app=create_application())
        user_email: typing.Final = fake.email()
        user_age: typing.Final = fake.random_int(min=18, max=99)

        response: typing.Final = client.post("/api/users", json={"email": user_email, "age": user_age})

        assert response.status_code == 201


    # ✅ Excellent: using Hypothesis for property-based testing
    @given(email=st.emails(), age=st.integers(min_value=18, max_value=99))
    def test_should_accept_any_valid_user_data(email: str, age: int) -> None:
        client: typing.Final = TestClient(app=create_application())

        response: typing.Final = client.post("/api/users", json={"email": email, "age": age})

        assert response.status_code in {201, 409}  # Created or already exists
    ```

4.  Use parametrization. The best tests are those that run the code many
    times, so try to exercise the same code paths across multiple test
    runs.

    ``` python
    import typing

    import pytest
    from litestar.testing import TestClient

    from myapp import create_application


    # ❌ Bad: a separate test for each case
    def test_should_reject_short_password() -> None:
        client: typing.Final = TestClient(app=create_application())
        response: typing.Final = client.post("/api/users", json={"email": "test@test.com", "password": "123"})
        assert response.status_code == 422


    def test_should_reject_long_password() -> None:
        client: typing.Final = TestClient(app=create_application())
        response: typing.Final = client.post("/api/users", json={"email": "test@test.com", "password": "a" * 1000})
        assert response.status_code == 422


    # ✅ Good: a parametrized test
    @pytest.mark.parametrize(
        ("password", "expected_status"),
        [
            ("123", 422),  # Too short
            ("validpass123", 201),  # Valid
            ("a" * 1000, 422),  # Too long
            ("", 422),  # Empty
            ("   ", 422),  # Whitespace
        ],
    )
    def test_should_validate_password_length(password: str, expected_status: int) -> None:
        client: typing.Final = TestClient(app=create_application())

        response: typing.Final = client.post("/api/users", json={"email": "test@example.com", "password": password})

        assert response.status_code == expected_status
    ```

5.  By default, all tests should always run in parallel, even if there
    are only a few of them. This ensures that tests are parallelizable
    from the very beginning and protects us from a situation where tests
    only work in a single thread. To do this, use `pytest-xdist` with
    the `-n auto` option.

6.  Coverage must also be measured for the test code itself. Any
    uncovered lines in tests mean that those parts of the tests do not
    work and should be removed or changed.
