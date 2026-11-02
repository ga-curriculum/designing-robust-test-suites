<h1>
  <span class="headline">Designing Robust Test Suites</span>
  <span class="subhead">Instructor Guide</span>
</h1>

## Test Organization and Project Health

### Delivery guidance

- Emphasize the real-world impact of good test organization through personal stories or recent examples from open-source projects.
- Use the included scenarios and analogies to engage learners with different experiences and cultural backgrounds.
- When facilitating the activity, demonstrate how to identify naming issues and duplicate code by walking through an intentionally messy sample.
- Encourage knowledge sharing by having learners present their reorganized structures.
- Facilitate the discussion prompt after the activity and prompt learners to reflect on benefits and obstacles.

### Knowledge check answers

1. B) Naming test files and functions consistently using "test\_" prefixes
2. A) To reduce code duplication and simplify updates

---

## Structuring Test Suites for Automatic Discovery

### Delivery guidance

- Set the tone by describing real-world scenarios (such as onboarding challenges or continuous integration mishaps) that highlight the importance of test suite organization.
- Walk learners through concrete examples from the lesson, connecting to their own experiences with Python projects and pytest.
- Emphasize the rationale behind each naming and structure guideline, and connect to previous content to show progression from foundational project organization to scalable, maintainable test suites.
- For the hands-on activity, provide the unorganized starter project and, if possible, demo the process of renaming, moving files, and verifying with pytest before learners complete their own refactor.
- Foster inclusive, open discussion after the activity: encourage everyone to share difficulties, lessons learned, and arguments for establishing consistent test practices in teams.
- Use the knowledge checks as interactive polling questions or quick quizzes; debrief the correct answers and clarify any misconceptions.

### Knowledge check answers

1. C) test*helper.py  
   \_This matches pytest’s default file name pattern, ensuring automatic discovery.*

2. B) It reduces code duplication and ensures consistency across multiple tests  
   _Putting shared setup in conftest.py keeps tests DRY (Don’t Repeat Yourself) and easy to update._

### Activity sample solution

A strong solution will show:

```bash
tests/
├── README.md
├── conftest.py
├── unit/
│   ├── test_user_logic.py
│   └── test_utils.py
├── integration/
│   ├── test_api_routes.py
│   └── test_db_connections.py
└── e2e/
    └── test_login_flow.py
```

---

## Isolating Different Test Types

### Delivery guidance

- Start by summarizing how test suite organization was handled in previous lessons, building context for why further separation by test type matters in larger projects.
- Use relatable analogies provided in the lesson (such as roles in sports teams or a well-organized workbench) to help learners internalize the distinct purposes of each test type.
- Demonstrate the hands-on value of using both folder structures and pytest markers by running example test selections live.
- Encourage learners to question and share their rationales during the activity and discussion prompt.
- Use before-and-after visuals of test suite organization to highlight the impact of good practices.

### Knowledge check answers

1. B) To enable flexible test selection and maintenance as projects scale
2. B) Documenting commands for running each test group in <code class="filepath">tests/README.md</code>

### Activity sample solution

Students should add markers to the various types of tests in the provided starter code.

---

## Reducing Duplication in Test Suites

### Delivery guidance

- Connect this microlesson to earlier content about test organization and separation of test types. Emphasize how reducing duplication is the next step in ensuring long-term health of a test suite.
- Begin with a quick poll or discussion about frustrations learners have experienced when updating many similar tests or fixing copy-paste errors in large test suites.
- Use the analogies and graphics to reinforce concepts for both visual and logical learners.
- Walk through the process of turning repeated logic into a fixture or helper live, or with a sample project.
- Emphasize that while DRY principles are important, over-abstraction can reduce readability; encourage learners to strike a balance.
- As learners work through the exercise, prompt them to consider the maintainability of their solution—would another contributor be able to easily understand and extend it?
- In the group discussion, invite learners to debate when it is appropriate to add a fixture or helper versus when direct test logic is better for clarity.

### Knowledge check answers

1. C) Ensures that every test has its own independent setup code, even if duplicated

2. B) When the logic appears in more than one test and improves clarity or maintainability

---

🏗️ **Under Construction**

We are constantly working to improve our resources for instructors and students.

**Have something to contribute to this Instructor Guide?** [Let us know](https://ga-curriculum.github.io/universal-resources-internal/module-feedback.html).
