# Week 3 In-Class Lab: Automated Unit Testing
### SDI 4213-980: DevOps – CI/CD

Starter package: https://github.com/SDIOUPI/SDI_4213_Week_3

The `.github/workflows/ci.yml` file is included as a preview for Week 4. You do not need to modify the GitHub Actions workflow during this lab unless instructed.

## Starter Package Structure

```
sdi4213-week3-testing-starter/
  README.md
  CONTRIBUTING.md
  requirements.txt
  .gitignore
  .env.example
  app/
    __init__.py
    main.py
    models.py
    services.py
  tests/
    __init__.py
    conftest.py
    test_api.py
    test_services.py
  docs/
    week3-lab-testing.md
    testing-notes.md
  .github/
    workflows/
      ci.yml
```

## Part 1: Start from the Latest main Branch

**Step 1.1: Switch to main and pull latest changes**
```
git checkout main
git pull
# Or use git switch
git switch main
git pull
```

**Step 1.2: Check status**
```
git status
```

Expected result: Your terminal should indicate that you are on main and that the working tree is clean.

Note: Do not begin editing files until your working tree is clean. If you have uncommitted changes, ask the instructor before continuing.

## Part 2: Create a Virtual Environment and Install Dependencies

**Step 2.1: Create virtual environment**
```
python -m venv .venv
```

**Step 2.2: Activate on Windows PowerShell**
```
.venv\Scripts\Activate.ps1
```

**Step 2.3: Activate on macOS/Linux**
```
source .venv/bin/activate
```

**Step 2.4: Install dependencies**
```
pip install -r requirements.txt
```

Expected result: The terminal prompt should show that the virtual environment is active, and pip should install the packages listed in requirements.txt.

Note: If PowerShell blocks activation, the instructor may provide a temporary execution policy command. Do not commit the `.venv` folder to GitHub.

## Part 3: Run the Application Locally

**Step 3.1: Start the FastAPI app**
```
uvicorn app.main:app --reload
```

**Step 3.2: Open these URLs in a browser**
```
http://127.0.0.1:8000
http://127.0.0.1:8000/health
http://127.0.0.1:8000/items
http://127.0.0.1:8000/items/1
```

Expected result: The `/health` route should return a status of `ok`. The `/items` route should return starter inventory items. The `/items/1` route should return the Laptop item from the starter data.

Note: Leave the server running while checking routes, then stop it with Ctrl+C when finished.

## Part 4: Run the Automated Tests

**Step 4.1: Run all tests**
```
pytest
```

**Step 4.2: Run tests with more detail**
```
pytest -v
```

**Step 4.3: Run only service tests**
```
pytest tests/test_services.py -v
```

**Step 4.4: Run only API tests**
```
pytest tests/test_api.py -v
```

Expected result: All tests should pass before you begin modifying code.

Note: Unit tests are located in `tests/test_services.py`. API route tests are located in `tests/test_api.py`.

## Part 5: Inspect the Unit Tests

Open `tests/test_services.py`. These tests directly check Python service functions without making HTTP requests.

Example unit test:
```python
def test_calculate_total_quantity():
    items = [
        Item(id=10, name="Keyboard", quantity=2),
        Item(id=11, name="Mouse", quantity=4),
    ]
    assert calculate_total_quantity(items) == 6
```

Expected result: This test checks one function: `calculate_total_quantity`.

Note: A unit test should usually be small, focused, and fast.

## Part 6: Inspect the API Route Tests

Open `tests/test_api.py`. These tests use FastAPI TestClient to call application routes and check HTTP responses.

Example API route test:
```python
def test_health_check_returns_ok():
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "ok"}
```

Expected result: This test checks the API route response rather than only one internal function.

Note: API route tests are helpful because they test behavior closer to what a user or client would experience.

## Part 7: Intentionally Break a Test

**Step 7.1: Open `app/main.py` and find this function**
```python
@app.get("/health")
def health_check():
    return {"status": "ok"}
```

**Step 7.2: Change it to this**
```python
@app.get("/health")
def health_check():
    return {"status": "broken"}
```

**Step 7.3: Run tests**
```
pytest
```

Expected result: At least one test should fail. The failure should point to the expected health check response.

Note: This is intentional. Do not panic when the test fails. Read the failure message and identify what value the test expected.

## Part 8: Fix the Broken Test

**Step 8.1: Change the health check back to this**
```python
@app.get("/health")
def health_check():
    return {"status": "ok"}
```

**Step 8.2: Run tests again**
```
pytest
```

Expected result: All tests should pass again.

Note: This is the basic testing feedback loop: change code, run tests, read results, fix problems, run tests again.

## Part 9: Create an Issue for a New Test

**Step 9.1: Create GitHub Issue**

Title: `Add test for low stock item`

Description: Add a unit test that confirms an item is identified as low stock when its quantity is below the selected threshold.

Expected result: The issue should be assigned to the student doing the work and moved to In Progress on the project board.

Note: Each code or documentation change should be tied to an issue.

## Part 10: Create a Branch for the New Test

**Step 10.1: Start from main**
```
git checkout main
git pull
# Or use git switch
git switch main
git pull
```

**Step 10.2: Create branch**
```
git checkout -b test/add-low-stock-test
# Or use git switch
git switch -c test/add-low-stock-test
```

**Step 10.3: Confirm branch**
```
git branch
```

Expected result: The asterisk should be next to `test/add-low-stock-test`.

Note: Stop if the asterisk is next to main. Create or switch to your testing branch before editing files.

## Part 11: Add a New Unit Test

Open `tests/test_services.py` and add a new test for low stock behavior below the existing low stock tests.

**Step 11.1: Add this test**
```python
def test_is_low_stock_true_when_quantity_below_threshold():
    item = Item(id=30, name="USB cable", quantity=1)
    assert is_low_stock(item, threshold=2) is True
```

**Step 11.2: Run tests**
```
pytest
```

Expected result: All tests should pass, and the total number of tests should increase by one.

Note: This is a unit test because it directly checks the `is_low_stock` function. The test verifies quantity below the threshold, which is a different case from quantity equal to the threshold.

## Part 12: Update Testing Notes

Open `docs/testing-notes.md` and answer the lab reflection questions.

Suggested note topics:
- Unit test vs. API route test
- Broken `/health` endpoint result
- How pytest reported the failure
- Why automated testing matters for CI/CD
- How these tests will help GitHub Actions next week
- Who served as Driver, Navigator/Tester, and Reviewer/Integrator

Expected result: The testing notes should contain enough detail for the instructor to see what your team observed and learned.

## Part 13: Commit and Push the Testing Work

**Step 13.1: Check changes**
```
git status
git diff
```

**Step 13.2: Stage files**
```
git add tests/test_services.py docs/testing-notes.md
```

**Step 13.3: Commit changes**
```
git commit -m "Add low stock unit test"
```

**Step 13.4: Push branch**
```
git push -u origin test/add-low-stock-test
```

Expected result: The branch should now be visible on GitHub.

Note: Do not commit the `.venv` folder, `__pycache__` folders, or other generated files.

## Part 14: Open a Pull Request

**Step 14.1: Pull request source and target**
```
from: test/add-low-stock-test
into: main
```

**Step 14.2: Pull request title and description**

Title: `Add low stock unit test`

Description:
```
This pull request adds a new unit test for the is_low_stock function and updates the team's testing notes.

Closes #<issue-number>
```

Expected result: The pull request should link to the GitHub Issue created earlier.

Note: Replace `<issue-number>` with the actual issue number from GitHub. Request at least one teammate as a reviewer.

## Part 15: Review, Merge, and Update Board

Reviewer checklist:
- Confirm pytest passes.
- Verify the new test checks low stock behavior.
- Confirm testing notes were updated.
- Confirm the work was completed on a branch.
- Leave a review comment or approval.
- Merge only after review is complete.

Expected result: After approval, merge the pull request into main, delete the branch, and move the issue to Done.

## Common Problems and Fixes

| Problem | Possible Fix |
|---|---|
| PowerShell blocks virtual environment activation | Ask the instructor for the approved execution policy command or use the terminal option recommended for class. |
| pytest command not found | Confirm the virtual environment is active and run `pip install -r requirements.txt`. |
| ModuleNotFoundError for app | Make sure you are running pytest from the project root folder. |
| Uvicorn cannot start | Confirm dependencies installed correctly and no other process is using the same port. |
| The wrong branch is active | Run `git branch`. If needed, switch to main, pull, and create the correct branch before editing. |
| Accidentally edited main | Stop before committing. Ask the instructor how to move changes to a branch. |

## Final Reminder

The main workflow for this lab is: Run app → Run tests → Break test → Read failure → Fix test → Add test → Branch → Pull request → Review → Merge.
