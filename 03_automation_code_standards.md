# 🧹 Automation Code Standards: Clean Code for QA

Test code is production code. If our test suite is difficult to read, hard to maintain, or fragile, it becomes a liability rather than an asset. This document defines the coding standards and best practices required to contribute to our Quality Engineering repository.

---

## 1. The Golden Rule: Tests as First-Class Citizens
We treat our automation framework with the same architectural respect as the main application. This means:
* **No "Quick and Dirty" Scripts:** Technical debt in the test framework leads to flaky tests, which destroys team trust in the CI/CD pipeline.
* **Peer Reviews are Mandatory:** Every Pull Request in the QA repository must be reviewed by at least one peer. We review for logic, readability, and adherence to these standards.

---

## 2. The Anatomy of a Perfect Test (The AAA Pattern)
Every automated test must follow the **Arrange, Act, Assert (AAA)** pattern. This structure makes the test's intent instantly clear to any developer or engineer reading it.

* **Arrange:** Set up the exact state required for the test (via API fixtures, database seeding, or navigation).
* **Act:** Perform the specific business action being tested.
* **Assert:** Verify the outcome.

**Example (Python/Pytest):**
```python
def test_user_can_apply_valid_discount_code(api_client, cart_page):
    # 1. ARRANGE
    api_client.create_cart_with_items(item_id="12345", quantity=1)
    valid_coupon = "SUMMER20"
    
    # 2. ACT
    cart_page.open()
    cart_page.apply_coupon(valid_coupon)
    
    # 3. ASSERT
    assert cart_page.get_discount_message() == "Coupon applied successfully!"
    assert cart_page.get_total_price() == expected_discounted_price
```

---

## 3. Naming Conventions: Revealing Intent
Test names must act as living documentation. If a test fails in the CI pipeline, the name alone should tell the developer exactly what broke.

* **❌ Bad:** `test_login_1()` or `test_cart_logic()`
* **✅ Good:** `test_user_cannot_login_with_invalid_password()` or `test_cart_total_updates_when_item_quantity_increases()`

**Format:** `test_<feature>_<scenario>_<expected_result>`

---

## 4. The "Zero Tolerance" Anti-Patterns
To maintain pipeline stability and execution speed, the following practices are strictly forbidden:

### A. Hardcoded Sleeps (`time.sleep()`)
* **Why it's banned:** It forces the pipeline to wait unnecessarily and causes flakiness depending on network/environment speed.
* **The Solution:** We use **Explicit/Smart Waits** (e.g., Selenium's `WebDriverWait`). The framework must wait *only* until a specific condition is met (e.g., element is clickable), then immediately proceed.

### B. Conditional Logic in Tests (`if/else`)
* **Why it's banned:** Tests must be deterministic. If a test has multiple paths, it is testing more than one thing and becomes a nightmare to debug.
* **The Solution:** Split the conditions into separate, focused tests. Use Pytest `@pytest.mark.parametrize` for data variations.

### C. UI Navigation for State Setup
* **Why it's banned:** UI interaction is the slowest and most brittle part of testing.
* **The Solution:** As defined in our Architecture, use API calls in the `Arrange` phase to bypass the UI until the exact screen you need to test.

---

## 5. Design for Debuggability (Observability in Tests)
When a test fails in the CI/CD pipeline, the developer should not need to re-run it locally to understand why. The test framework must provide immediate, actionable context.

* **Failing Gracefully:** We do not accept vague error messages like `ElementNotFound`. Assertions must include custom failure messages explaining the business impact.
    * *❌ Bad:* `assert current_status == expected_status`
    * *✅ Good:* `assert current_status == "Shipped", f"Expected order status to be 'Shipped', but found '{current_status}'. API Payload ID: {order_id}"`
* **Artifact Collection:** On any test failure, the framework must automatically capture and attach:
    1. A screenshot of the exact moment of failure.
    2. The DOM snapshot (HTML source).
    3. The Browser Console Logs (to catch JS exceptions).

---

## 6. Parallel Execution Readiness (Thread Safety)
To maintain a fast feedback loop, our test suite must be capable of running in parallel (using `pytest-xdist`). 

* **No Shared State:** Tests must be 100% isolated. A test cannot rely on data created by the previous test. 
* **Global Variables are Banned:** Using global variables or singleton patterns that store state across tests will cause race conditions during parallel execution. Every test gets its own fresh instance of data and session.

---

## 7. Code Review Checklist for QA
Before approving a QA Pull Request, reviewers must verify:
- [ ] Does the test follow the AAA pattern?
- [ ] Is the test independent? (Can it run alone or in parallel without failing?)
- [ ] Are locators abstracted in the Page Object/Component layer?
- [ ] Is there exactly ONE logical assertion focus?
- [ ] Are there zero hardcoded waits (`sleep`)?
- [ ] Are custom failure messages implemented for assertions?