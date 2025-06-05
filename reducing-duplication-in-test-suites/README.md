<h1>
  <span class="headline">Designing Robust Test Suites</span>
  <span class="subhead">Reducing Duplication in Test Suites</span>
</h1>

**Learning objective:** Use test fixtures, setup/teardown logic, and reusable helper functions to reduce duplication.

## Introduction to the benefits of utilizing pytest fixtures to streamline test code

As you take on larger Python projects with frameworks like FastAPI, you'll often see the same setup or teardown steps copied between many tests—creating users, starting necessary services, or cleaning up files. This is not just tedious to maintain; it can also lead to errors and inconsistencies if a test setup changes and those changes aren’t reflected everywhere.

This is where **pytest fixtures** shine. A pytest fixture is a reusable function, marked by the `@pytest.fixture` decorator, that prepares a resource or state your tests need. When you use a fixture, you only need to define that setup logic once. Pytest takes care of making it available to any test that needs it.

> 💡 Think of fixtures as having a well-stocked toolbox. Instead of scattering the same screwdriver across every drawer (test), you keep just one, in a single, reliable place. Each time you need it, you know exactly where it is—reducing clutter and confusion.

**Benefits of using fixtures:**

- **Single source of truth:** Shared setup lives in one fixture, so updates only need to happen in one spot.
- **Consistency:** Every test relying on a fixture gets the exact same setup, avoiding hidden bugs.
- **Maintainability:** Refactoring or adding features is faster and safer when you only need to change setup or teardown logic in a fixture.
- **Scalability:** As your codebase grows, you spend less time copy-pasting code and more time writing meaningful new tests.

### Example: Database session fixture

<code class="filepath">tests/conftest.py</code>

```python
import pytest
from myapp import create_test_db_session

@pytest.fixture
def db_session():
    # Set up a temporary database session for testing
    session = create_test_db_session()
    yield session
    session.close()
```

<code class="filepath">tests/integration/test_models.py</code>

```python
def test_create_user(db_session):
    user = User(name="Alex")
    db_session.add(user)
    db_session.commit()
    assert db_session.query(User).count() == 1
```

Here, the `db_session` fixture is created only once and shared across all relevant tests.

## Steps for creating and implementing setup/teardown functions effectively

Many tests require both a setup (preparing data or state) and a teardown (cleaning up after the test). With pytest, the recommended way to manage this is by placing your setup code before the `yield` statement in your fixture, and the teardown logic after `yield`.

> 🧠 A well-implemented setup and teardown flow keeps your tests reliable and your environment clean—just like leaving your workspace tidy after you finish a task ensures it's ready for the next person (or test).

**How to create effective setup/teardown with fixtures:**

- **Choose the right scope:**
  - `function` (default): Runs for every test.
  - `class`, `module`, or `session`: Runs less often, great for expensive or slow setup.
- **Place setup before `yield` and teardown after.**
- **Share common fixtures in `conftest.py`:** This special file in your <code class="filepath">tests/</code> directory allows pytest to automatically discover your shared fixtures.

### Example: Cleaning up temporary files

```python
import pytest
import tempfile
import os

@pytest.fixture
def temp_file():
    file = tempfile.NamedTemporaryFile(delete=False)
    yield file.name
    file.close()
    os.remove(file.name)

def test_write_to_file(temp_file):
    with open(temp_file, 'w') as f:
        f.write("Hello pytest!")
    with open(temp_file) as f:
        content = f.read()
    assert content == "Hello pytest!"
```

Here, the file is created before the test runs and always removed afterward—ensuring no leftover files affect later tests.

## Methods for designing and applying reusable helper functions across test cases

Sometimes, your tests repeat logic that isn’t setup or teardown—like validating an API response or formatting sample data. Instead of re-writing these steps everywhere, extract them into helper functions. These helpers act as small tools that your tests can call when needed, keeping your code DRY ("Don’t Repeat Yourself").

> 🏆 Helper functions are like templates you can fill in or use anywhere, like a global formula for checking if an API returns what you expect.

**Best practices for helpers:**

- **Logical location:** Place shared helpers in a <code class="filepath">utils.py</code> (or similarly named) file inside <code class="filepath">tests/</code>.
- **Descriptive names:** Name helpers based on what they actually do.
- **Purity:** Helpers should not modify global state or rely on anything outside their arguments.

### Example: Reusable API response checker

<code class="filepath">tests/utils.py</code>

```python
def assert_valid_response(response, expected_status=200):
    assert response.status_code == expected_status
    assert response.headers['Content-Type'] == 'application/json'
```

<code class="filepath">tests/integration/test_api.py</code>

```python
from tests.utils import assert_valid_response

def test_login_success(api_client):
    response = api_client.post('/login', json={'user': 'alex', 'password': 'pw'})
    assert_valid_response(response)
    data = response.json()
    assert 'token' in data
```

Now, if you ever need to change your criteria for “valid” responses, you only update the helper function. All tests benefit from the update immediately.

## Strategies to efficiently share test data among multiple tests

Duplicated test data can create chaos in large projects. Imagine updating user details in ten different places if a schema changes. Instead, centralize and share test data to keep things maintainable.

There are a few ways to do this in pytest:

- **Fixtures as data providers:** Have a fixture return dictionaries or objects with sample data.
- **Separate test data files:** For large or complex data, keep it in a <code class="filepath">.json</code>, <code class="filepath">.yml</code>, or <code class="filepath">.csv</code> file, and load it from a fixture.
- **Parameterized tests:** Use `@pytest.mark.parametrize` to run a test with several sets of data inputs.

**Example: Using a fixture to provide user data**

```python
@pytest.fixture
def sample_user_data():
    return {"username": "sky", "password": "rainbow123"}

def test_register_user(api_client, sample_user_data):
    response = api_client.post('/register', json=sample_user_data)
    assert response.status_code == 201

def test_login_user(api_client, sample_user_data):
    response = api_client.post('/login', json=sample_user_data)
    assert response.status_code == 200
```

Both tests share the exact same user data, so any changes need to be made only once.

**Example: Parameterizing a test**

```python
import pytest

@pytest.mark.parametrize("username,password", [
    ("alice", "wonderland"),
    ("bob", "builder"),
])
def test_login_valid(api_client, username, password):
    response = api_client.post('/login', json={"username": username, "password": password})
    assert response.status_code == 200
```

Parameterization helps you quickly check similar logic across several data points—without duplicating your test code.

<div class="activity discussion">
  <h2 class="title">What Fixtures Might We Need?</h2>
  <span class="minutes">10 min</span>
</div>

You’ve been given a FastAPI test suite full of placeholder tests. The tests all pass, but their names suggest what real functionality might eventually live inside them.

Imagine you’re preparing this codebase for real development—where duplication and setup complexity will grow.

Let’s look at these test names:

- `test_user_can_register_and_login`
- `test_checkout_flow_shows_order_confirmation`
- `test_database_returns_correct_order_history`
- `test_get_user_profile_route_returns_expected_fields`

1. What kinds of shared setup might these need?
2. Would any of these tests require:
   - a registered user?
   - a logged-in session?
   - mocked order data or a stubbed DB call?
   - a pre-configured TestClient instance?
3. How might you use fixtures to provide those setups automatically?

## Knowledge checks

1. Which of the following is NOT a benefit of using pytest fixtures?

   - A) Centralized and easily updated setup logic
   - B) Reduces code duplication across tests
   - C) Ensures that every test has its own independent setup code, even if duplicated
   - D) Makes tests more maintainable and scalable

2. When should you extract repeated assertion or formatting logic into a helper function?

   - A) Only if the logic is used five or more times
   - B) When the logic appears in more than one test and improves clarity or maintainability
   - C) Only when parameterization cannot be used
   - D) You should not use helper functions in tests
