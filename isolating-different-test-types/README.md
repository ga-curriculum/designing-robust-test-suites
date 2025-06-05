<h1>
  <span class="headline">Designing Robust Test Suites</span>
  <span class="subhead">Isolating Different Test Types</span>
</h1>

**Learning objective:** Differentiate between unit, integration, and E2E tests and isolate them using directories and/or markers.

## Understanding the characteristics of unit, integration, and E2E tests

Distinguishing these test types is essential for keeping them organized, effective, and scalable—especially as your project and team grow.

Let's review key elements of each test type that will help you identify and organize your test suite structure.

### Unit tests

_Unit tests_ check individual "units" of code—usually a single function or method—in isolation. They run rapidly and rely only on the code being tested, without touching databases, networks, or external APIs.

**Example:**

<code class="filepath">tests/unit/test_math_utils.py</code>

```python
def test_add_numbers():
    assert add(2, 3) == 5
```

This test examines the `add` function to verify its output, without needing any outside system.

### Integration tests

_Integration tests_ ensure that different components of your application work together as intended. They might use databases, file systems, or even talk to remote services.

**Example:**

<code class="filepath">tests/integration/test_db_integration.py</code>

```python
def test_create_user_in_database(db_session):
    user = User(name="Kai")
    db_session.add(user)
    db_session.commit()
    assert db_session.query(User).filter_by(name="Kai").one()
```

Here, the test interacts with a (usually temporary or mocked) database to confirm that the user is created and stored correctly.

### E2E tests

_E2E tests_ mimic real user actions, running through your entire application to validate workflows from start to finish. These often use tools like Selenium and may spin up the whole stack, including user interfaces and backend services.

**Example:**

<code class="filepath">tests/e2e/test_signup_flow.py</code>

```python
def test_user_can_sign_up_and_login(live_server, selenium):
    selenium.get(f"http://{live_server}/signup")
    selenium.find_element(...).send_keys("newuser")
    selenium.find_element(...).click()
    assert "Welcome, newuser!" in selenium.page_source
```

In this test, the application is started locally, and Selenium simulates a real person signing up and logging in through the user interface.

## Usage of `pytest` markers for categorizing and isolating tests

Subdirectories are one strategy—but sometimes, you need more fine-grained control. This is where _pytest markers_ help by tagging tests with useful labels, even if they appear in the same file.

### How to use `pytest` markers

Markers let you tag individual tests (or entire test classes) for selective runs.

**Applying markers:**

```python
import pytest

@pytest.mark.unit
def test_add_numbers():
    ...

@pytest.mark.integration
def test_database_access():
    ...

@pytest.mark.e2e
def test_signup_user_flow():
    ...
```

**Running tests using markers:**

You can use the `-m` flag to run only certain tests:

```
pytest -m unit
pytest -m integration
```

**Registering markers:**

Be sure to register custom markers to avoid warnings from pytest.

<code class="filepath">pytest.ini</code>

```ini
[pytest]
markers =
    unit: unit tests
    integration: integration tests
    e2e: end-to-end tests
```

> Markers are powerful: they allow you to select and exclude tests flexibly, even mixing strategies—such as “run all unit tests except those tagged as slow.”

## Best practices for executing and managing isolated test suites

By separating your test types both by folder and by marker, you gain flexibility and maintainability. Here are practical tips to keep these boundaries strong over time.

### Keep test dependencies explicit

- _Unit tests_: No databases, file systems, or network calls. Use mocks and stubs.
- _Integration tests_: Set up and tear down real (or temporary) dependencies, like test databases.
- _E2E tests_: Run in an isolated, controlled environment (often using containers) to prevent side effects.

### Make running subsets easy and well-documented

Keep your test suite accessible to everyone by documenting commands and approaches.

- In your <code class="filepath">tests/README.md</code>, describe how to run each group:
  - `pytest tests/unit`
  - `pytest -m integration`
  - `pytest tests/e2e`
- Consider adding helper scripts or Makefile targets for convenience.

> 🏆 Well-documented conventions remove barriers for new contributors and make test practices reliable across teams.

### Automate in continuous integration (CI)

Integrate with your CI pipeline for efficiency and trust:

- Unit tests: Run on every pull request.
- Integration tests: Run on pull request or merges, as fits your workflow.
- E2E tests: Run in staging or before production releases to check the end-user experience.

### Minimize cross-pollution between test types

- Do not let tests in one layer "leak" into another (for example, a unit test that unintentionally accesses the network).
- Enforce boundaries with code review and periodic audits.

> ⚠ Poor boundaries can make tests unreliable and slow, draining both trust and productivity.

## Organizing and running isolated test suites

Let’s look at a practical project structure you might use in your own work.

**Directory example:**

<code class="filepath">project/</code>

```bash
    app/
        auth.py
        main.py
    tests/
        unit/
            test_auth.py
            test_main.py
        integration/
            test_db.py
        e2e/
            test_signup.py
        conftest.py
    pytest.ini
```

**Applying markers in different files:**

<code class="filepath">tests/unit/test_auth.py</code>

```python
import pytest

@pytest.mark.unit
def test_check_password_hash():
    ...
```

<code class="filepath">tests/integration/test_db.py</code>

```python
import pytest

@pytest.mark.integration
def test_can_save_user_to_db(db_session):
    ...
```

<code class="filepath">tests/e2e/test_signup.py</code>

```python
import pytest

@pytest.mark.e2e
def test_user_can_signup_via_ui(selenium):
    ...
```

<code class="filepath">pytest.ini</code>

```ini
[pytest]
markers =
    unit: unit tests
    integration: integration tests
    e2e: end-to-end tests
```

**How to run specific test suites:**

- All unit tests: `pytest tests/unit/` or `pytest -m unit`
- All integration tests: `pytest tests/integration/` or `pytest -m integration`
- All E2E tests: `pytest tests/e2e/` or `pytest -m e2e`

> Combining folder structure and markers means you can flexibly manage, run, and scale your test suite as your application—and your team—grow.

<div class="activity solo-exercise">
  <h2 class="title">Tag Your Tests with pytest Markers</h2>
  <span class="minutes">15 min</span>
</div>

In this activity, you'll learn how to apply **pytest markers** to categorize and selectively run tests. You'll work with a your existing test suite and use markers to distinguish between different test types.

This is a great way to practice organizing your tests using `@pytest.mark`, `pytest.ini`, and command-line filtering.

## Goals

- Understand how to apply custom markers
- Use `pytest.ini` to register and manage markers
- Filter tests by marker using `pytest -m`

## Step 1: Inspect Your Test Files

You should have the following messy structure already set up and reorganized from the last activity (see your starter files):

```bash
fastapi-app/
├── app/
│   ├── __init__.py
│   ├── main.py
│   └── utils.py
│
├── tests/
│   ├── README.md
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_utils.py
│   │   └── test_user_logic.py
│   ├── integration/
│   │   ├── test_api_routes.py
│   │   └── test_db_connections.py
│   └── e2e/
│       └── test_login_flow.py
│
├── requirements.txt
└── README.md
```

Each file should already contain **default passing test functions**. You'll now label these using markers.

## Step 2: Add `@pytest.mark` Tags

Open each test file and add a marker to the test function:

### Example:

```python
import pytest

@pytest.mark.unit
def test_user_flags_are_parsed_correctly():
    assert True
```

Use the following guidelines:

- **Unit tests**: Tag with `@pytest.mark.unit`
- **Integration tests**: Tag with `@pytest.mark.integration`
- **End-to-end tests**: Tag with `@pytest.mark.e2e`

## Step 3: Create `pytest.ini`

To avoid warnings about unknown markers, register them in a `pytest.ini` file in your project root:

### `pytest.ini`

```ini
[pytest]
markers =
    unit: marks unit tests
    integration: marks integration tests
    e2e: marks end-to-end tests
```

> 🔧 This file tells `pytest` which markers are valid, so it doesn't treat them as typos or errors.

## Step 4: Run Tests by Marker

Use the `-m` flag to run only tests with a specific marker:

```bash
pytest -m unit
pytest -m integration
pytest -m e2e
```

You can also combine markers using `or` and `not`:

```bash
pytest -m "unit or integration"
pytest -m "not e2e"
```

## Can You...

- Add the correct marker to each file?
- Run just the unit tests?
- Exclude the e2e tests?

✅ If yes, you've successfully implemented pytest markers to organize your suite.

## Knowledge checks

**Question 1:**  
What is the main reason for using both directories and markers to isolate test types?

- A) So tests can be renamed automatically
- B) To enable flexible test selection and maintenance as projects scale
- C) To discourage adding new tests
- D) So that E2E tests always run first

**Question 2:**  
Which action would be considered a best practice when managing isolated test suites?

- A) Allowing unit tests to directly access a production database
- B) Documenting commands for running each test group in <code class="filepath">tests/README.md</code>
- C) Randomly assigning markers to tests for variety
- D) Mixing test types in the same files for convenience
