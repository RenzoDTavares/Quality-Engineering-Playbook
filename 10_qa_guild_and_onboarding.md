# 👥 QA Guild & Onboarding: Scaling Quality Culture

A test automation framework is only as good as the engineers maintaining it. As we scale the engineering organization, we must ensure that our quality standards, technical practices, and culture scale with it. This document outlines how we onboard new talent and foster continuous learning through the QA Guild.

---

## 1. The QA Guild (Community of Practice)
In a squad-based (agile) organization, QA Engineers are often distributed across different cross-functional teams. To prevent silos and knowledge fragmentation, we maintain a **QA Guild**.

* **Bi-Weekly Syncs:** A recurring meeting where engineers share new tools, present refactored code, or dissect a recent complex bug.
* **Standardization:** The Guild is the governing body for this very Playbook. Any proposal to change our core tech stack (e.g., moving from Selenium to Playwright) must be presented as an RFC (Request for Comments) to the Guild.
* **InnerSource:** We treat our test framework as an internal open-source project. Anyone (Devs or QAs) can contribute, but the Guild maintains the architectural vision and approves the PRs.

---

## 2. The 30-60-90 Day Onboarding Plan
When a new Quality Engineer joins the team, we do not expect them to write 50 E2E tests in their first week. We focus on context, architecture, and value delivery.

### First 30 Days (Context & Foundation)
* Set up the local environment and successfully run the entire test suite.
* Read and understand this Strategic Quality Playbook.
* Shadow a Senior QA/Developer during a PR review and a "Three Amigos" session.
* **Goal:** Merge their first Pull Request (e.g., adding a simple API test or refactoring a locator in a Page Object).

### 60 Days (Autonomy & Delivery)
* Take ownership of the testing strategy for a specific epic or feature.
* Independently design and implement the automated tests (API and UI) for that feature.
* Participate in incident triaging and monitor the CI/CD pipeline health.
* **Goal:** Consistently deliver automated checks that pass the PR gates without major structural revisions.

### 90 Days (Leadership & Optimization)
* Identify and fix a flaky test in the quarantine backlog.
* Lead a "Three Amigos" session.
* Propose an improvement to the automation framework or CI/CD pipeline.
* **Goal:** Transition from a consumer of the framework to an active contributor and optimizer.

---

## 3. Continuous Learning & Engineering Excellence
The technology landscape evolves rapidly. We actively invest in the professional development of our engineering team.

* **Advanced Certifications:** We encourage and support engineers in pursuing industry-recognized validations that align with our architectural standards, such as the **ISTQB CTAL-TAE (Test Automation Engineer)**. This ensures our team speaks a unified, globally recognized language regarding test architecture and metrics.
* **Tech Talks & Conferences:** Engineers are encouraged to allocate dedicated time for learning and to present their findings back to the Guild.
* **Mentorship:** Every Junior/Mid-level engineer is paired with a Senior Leader for technical mentoring, focusing on code design patterns (Clean Code, SOLID) and strategic thinking (Risk-Based Testing).