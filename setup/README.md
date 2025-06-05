<h1>
  <span class="headline">Designing Robust Test Suites</span>
  <span class="subhead">Setup</span>
</h1>

## Setup

This short setup prepares students to work with a mock FastAPI app that has real tests but incomplete structure. They will practice test discovery, organization, and cleanup in the following lessons.

### Starter Project Structure

```bash
fastapi-app/
├── app/
│   ├── __init__.py
│   ├── main.py
│   └── utils.py
│
├── tests/
│   ├── apitests.py
│   ├── userTest.py
│   ├── data/
│   │   └── test_utils_data.py
│   ├── login/
│   │   └── login_flow.py
│   └── e2e_tests.py
│
├── requirements.txt
└── README.md
```

This codebase includes real test shells with comments, but test names, folder structure, and naming conventions are inconsistent.

## Using the Starter code

1. **Clone this starter code repo:** [Designing Robust Test Suites - Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/designing-robust-test-suites-starter-code).
2. `cd` into the `fastapi-app` folder
3. **Review the `tests/` folder.** You’ll notice test types are mixed and some test files are poorly named.
4. Don’t change anything yet. You’ll restructure and organize this test suite in the next lessons.

## Setting up the application

1. Make sure you are in the `fastapi-app` directory.

2. Create a virtual environment:

   ```bash
   python -m venv venv
   source venv/bin/activate  # or venv\Scripts\activate on Windows
   ```

3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

4. To verify your test setup:

   ```bash
   pytest tests/
   ```

   > You should see 8+ passing tests, though the structure is intentionally disorganized.
