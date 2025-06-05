<h1>
  <span class="headline">Designing Robust Test Suites</span>
  <span class="subhead">Structuring Test Suites for Automatic Discovery</span>
</h1>

**Learning Objective:** Structure a test suite using folders and naming conventions that enable automatic discovery by tools like pytest.

## Overview of pytest test discovery mechanisms

Writing and running automated tests is essential in software development, but true efficiency comes when testing fits effortlessly into your development workflow. `Pytest`, a widely used Python testing framework, excels at this by automatically discovering your tests using simple, predictable patterns.

_Automatic discovery_ allows you to run the entire test suite with a single command like `pytest`, ensuring no important tests are missed—even as your project grows and more contributors join your team. This reliability is crucial for continuous integration and smooth collaboration.

> 💡 Think of automatic test discovery as a librarian who can instantly find every book in a library because each book follows a clear labeling and shelving system. If the books are scattered randomly or mislabeled, vital titles go missing, and the value of the library drops. In the same way, disorganized tests are likely to be skipped or duplicated, eroding your team’s confidence in the codebase.

### How does pytest find your tests?

When you invoke `pytest` in your project root, the framework looks for tests using some well-defined rules:

- **Folders:** Pytest searches the current directory and its subdirectories for test files.
- **File names:** Looks for Python files named starting with `test_` or ending with `_test.py`.
- **Functions and classes:** Inside those files, runs functions and methods that start with `test_`.
- **Test classes:** Needs classes named beginning with `Test`, and those should not use an `__init__` method.

> Following these conventions makes your test suite predictable for both humans and automated tools, reducing configuration overhead and the risk of missing critical tests.

## Exploration of naming conventions for test files and functions

A consistent naming system is at the core of reliable test discovery. Let’s clarify each layer of naming from files to functions to classes.

### Test file naming

Test files must follow the patterns pytest expects:

- Begin with `test_` or end with `_test.py`.
  - ✅ `test_models.py`
  - ✅ `auth_test.py`
  - ❗ Avoid: `models_tests.py`, `tests_auth.py` (these may not be discovered by pytest)

> 🏆 Consistency here is your safety net: each contributor knows exactly how to add or find tests for any part of your project.

### Test function naming

Test functions must start with `test_`:

- ✅ `def test_create_user():`
- ✅ `def test_login_failure():`
- ❗ Avoid: `def login_test():` (pytest will skip this function)

### Test class naming

If grouping test functions in classes:

- Start the class name with `Test` (ex: `class TestUserModel:`).
- Do not define an `__init__` method.

Example:

tests/test_user_api.py

```python
def test_create_user_success():
    # Test creating a new user returns 201 Created
    ...

class TestUserLogin:
    def test_login_valid_credentials(self):
        # Test that a user can login with valid credentials
        ...
```

> 💡 These practices are not just rules to follow—they make the difference between a test that quietly vanishes and one that reliably protects your software.

## Guidelines for optimal folder structure in test suites

Clear folder structures benefit both teams and tools. For teams, they provide a map of exactly what is tested and how to add new tests. For automation tools like pytest, they guarantee every relevant file is included without tedious manual configuration.

### Key folder structure rules

- Use a top-level <code class="filepath">tests/</code> directory to keep all tests separate from production code.
- Group test files into subfolders such as <code class="filepath">unit/</code>, <code class="filepath">integration/</code>, or <code class="filepath">e2e/</code>, or by module/feature.
- Avoid mixing tests and production code to prevent accidental deployment of test-only files.
- Add <code class="filepath">**init**.py</code> in test subfolders only if you need to make them a Python package (most projects do not need this).
- Centralize shared fixtures and setup in a <code class="filepath">conftest.py</code> file, either in <code class="filepath">tests/</code> or its subfolders.

Example scalable test folder layout:

```bash
project/

    src/
        users.py
        auth.py
    tests/
        conftest.py
        unit/
            test_users.py
            test_auth.py
        integration/
            test_database.py
        e2e/
            test_signup_flow.py
```

> ♻️ This standardized structure means new test types can be added without reorganizing the entire suite, and running tests for a particular layer (such as only integration tests) is straightforward.

## Configuration tips for customizing pytest's test discovery process

Pytest’s default discovery rules work for most scenarios. However, large, legacy, or complex projects sometimes need adjustments.

### Using pytest options

- To find non-standard test file names, use the <code class="filepath">--python-files</code> option or configure in <code class="filepath">pytest.ini</code>.

Example: If files end with <code class="filepath">\_spec.py</code> and you want pytest to recognize them:

```bash
pytest --python-files="\*\_spec.py"
```

- Store these options in <code class="filepath">pytest.ini</code> or <code class="filepath">pyproject.toml</code> so everyone on your team uses the same setup.

pytest.ini

```bash
ini
[pytest]
testpaths = tests
python*files = test*_.py \_\_test.py
python_classes = Test\*
python_functions = test_\*
```

> 🧠 Using configuration files keeps discovery reliable and prevents local differences from breaking your test suite.

- Ignore files or folders that should not be tested using <code class="filepath">--ignore=path/</code> or by listing them in <code class="filepath">norecursedirs</code>.

Example command:

```bash
pytest --ignore=tests/legacy/

Add to pytest.ini:

ini
[pytest]
testpaths = tests
norecursedirs = .git venv legacy
```

<div class="activity group-exercise">
  <h2 class="title">Refactor a Disorganized Test Suite</h2>
  <span class="minutes">20 min</span>
</div>

## Goal

You are given a FastAPI project with a messy `tests/` directory. Your job is to clean up the structure to make the suite more maintainable and compatible with automatic test discovery tools like `pytest`. These files already contain passing tests, there is no need to change the test functions or add new functions. The primary goal is to practice good file organization and structure.

### Step 1: Explore the Mess

Open the project and explore the current file structure:

```bash
cd fastapi-app
tree
```

- You'll find files like `apitests.py` and `userTest.py`.
- Notice that some files are deeply nested or have incorrect naming.

### Step 2: Create a Clean Test Structure

Inside the `tests/` folder, create subfolders to group test types:

```bash
cd tests
mkdir unit integration e2e
```

### Step 3: Rename and Relocate Test Files

Use the comments in each file as hints.

1. Reorganize:

   - Move and rename `userTest.py` → `unit/test_user_logic.py`
   - Move and rename `data/test_utils_data.py` → `unit/test_utils.py`
   - Move and rename `apitests.py` → `integration/test_api_routes.py`
   - Move and rename `e2e_tests.py` → `integration/test_db_connections.py` _(split tests if needed, some may be e2e, unit, or integration)_
   - Move and rename `login/login_flow.py` → `e2e/test_login_flow.py`

2. Review and recategorize tests:
   - Read each test’s name and comment to determine whether it’s a unit, integration, or end-to-end test. If a test is in the wrong file or folder, move it to the appropriate location that matches its purpose.

### Step 4: Clean Up Old Directories

Once files are relocated, remove the empty folders:

```bash
rm -r data login
```

### Step 5: Create a `conftest.py` for Shared Fixtures

Create an empty fixture file (you'll fill this out in later lessons):

```bash
touch conftest.py
```

### Step 6: Add a Test README

Create a new `README.md` in the `tests/` folder that includes:

```Bash
# Test Suite Overview

## Structure

- `unit/`: Tests for individual functions (Ex: logic in utils or services)
- `integration/`: Tests for route logic and FastAPI behavior
- `e2e/`: Full user-flow tests, like logging in or submitting forms

## Running Tests

Run all tests:

`pytest`


Run only unit tests:

`pytest tests/unit`


Run only integration tests:

`pytest tests/integration`


Run end-to-end tests:

`pytest tests/e2e`

```

### ✅ Final Structure (Your Goal)

Your final `tests/` directory should look like:

```bash
tests/
├── README.md
├── conftest.py
├── unit/
│   ├── test_utils.py
│   └── test_user_logic.py
├── integration/
│   ├── test_api_routes.py
│   └── test_db_connections.py
└── e2e/
    └── test_login_flow.py
```

### 💡 Tips

- All test files must start with `test_` or end with `_test.py`.
- All test functions must begin with `test_`.
- `pytest` automatically discovers tests using this pattern.

Let your test suite be clean, discoverable, and scalable!
