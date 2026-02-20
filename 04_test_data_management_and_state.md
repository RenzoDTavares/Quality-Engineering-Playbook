# 🗄️ Test Data Management & Compliance: The Lifeblood of Automation

Test data is the most common cause of flaky tests and false negatives. If multiple tests compete for the same user account or inventory item, the suite becomes non-deterministic. This document outlines our Test Data Management (TDM) strategy, focusing on isolation, compliance, and programmatic state creation.

---

## 1. The "Zero Production Data" Policy (GDPR/LGPD Compliance)
Data privacy is a non-negotiable architectural constraint. We strictly adhere to global privacy frameworks, particularly GDPR (Europe) and LGPD (Brazil).

* **No Production Dumps:** We absolutely prohibit the use of production databases (even if obfuscated or anonymized) in staging, QA, or development environments.
* **Synthetic Data Only:** All test data (users, credit cards, addresses) must be synthetically generated using libraries like `Faker` or internal factory patterns.
* **Security First:** Test credentials must never be committed to the repository in plain text. We use GitHub Secrets or environment variable managers (AWS Secrets Manager) injected at runtime.

---

## 2. Just-In-Time (JIT) Data Strategy (Ephemeral Data)
We do not rely on static data residing in the QA database. Static data decays, expires, and gets corrupted by manual testing.

* **Create on the Fly:** Every automated test must programmatically generate the exact state it needs to run. 
* **API-Driven Generation:** We use backend APIs to create this state instantly. 
    * *Example:* To test the "Cancel Order" UI, the test first calls the `POST /orders` API to create a brand new, unique order specifically for that test instance.



---

## 3. Managing State with Pytest Fixtures
In our Python ecosystem, we leverage `pytest` fixtures to handle the heavy lifting of state management and dependency injection.

### Scope Strategy
* **`scope="function"` (Default):** Used for data that must be strictly isolated per test (e.g., a new user account).
* **`scope="session"`:** Used for expensive, read-only setup operations that do not mutate state (e.g., retrieving a static configuration dictionary from the database once per CI run).

### The "Clean Your Room" Mandate (Teardown)
Tests must not leave garbage behind. A bloated database slows down API responses and increases cloud costs. We use the `yield` statement in Pytest fixtures to guarantee teardown, even if the test assertion fails.

**Example (Pytest Data Fixture):**
```python
import pytest
from core.api_client import APIClient

@pytest.fixture(scope="function")
def new_active_user(api_client: APIClient):
    # SETUP: Generate synthetic data and create via API
    user_payload = generate_synthetic_user()
    user_id = api_client.create_user(user_payload)
    
    # INJECT: Pass the data to the test
    yield user_id  
    
    # TEARDOWN: Clean up the database regardless of test pass/fail
    api_client.delete_user(user_id)
```

---

## 4. The Mocking Boundary: When NOT to use Real Data
While we strive for End-to-End realism, certain boundaries must be mocked to ensure pipeline stability.

* **Third-Party Services:** We do not test external vendors. If our checkout flow uses Stripe or PayPal, we intercept these calls and mock the responses (using tools like `responses` in Python or WireMock in the cluster) during standard E2E runs.
* **Why?** If Stripe's sandbox environment goes down, our CI/CD pipeline should not turn red. We only test *our* integration logic, not their uptime.

---

## 5. Idempotency in Testing
Whenever possible, our test teardown scripts are designed to be idempotent. This means the teardown can run multiple times safely without throwing errors, ensuring that if a pipeline is manually aborted halfway, the next run can still clean up orphaned records.