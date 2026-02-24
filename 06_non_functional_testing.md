# 🚀 Non-Functional Testing: Performance, Scalability, and Accessibility

Functional testing verifies that the software *works*. Non-Functional Testing (NFT) verifies how *well* it works, how it behaves under stress, and whether it is usable by everyone. As a Quality Engineering practice, we treat NFT not as an afterthought, but as a core pillar of our Definition of Done (DoD).



---

## 1. Performance & Load Testing Strategy
We do not wait for a "Black Friday" or a major marketing launch to discover that our database locks up under heavy traffic. We shift performance testing left into our continuous delivery cycle.

### The Testing Spectrum
* **Load Testing:** Verifies system behavior under *expected* peak conditions.
* **Stress Testing:** Pushes the system *beyond* normal conditions to find the breaking point (bottlenecks) and observe how it recovers.
* **Spike Testing:** Simulates sudden, massive bursts of traffic (e.g., a viral push notification).
* **Soak Testing:** Runs a moderate load over a long period (e.g., 24 hours) to detect memory leaks and resource degradation.

### Tooling & Execution (Infrastructure as Code)
We treat performance tests as code. Heavy, UI-based recording tools are legacy.
* **Tool of Choice:** `k6` (by Grafana) or `Gatling`. We write test scenarios in JavaScript/Go or Scala, allowing them to live in the same repository as the application code.
* **CI/CD Integration:** We run lightweight performance checks (e.g., asserting that the 95th percentile response time for the login API is `< 300ms`) on every Pull Request for Tier 1 services. 
* **Observability First:** A load test is blind without metrics. We run these tests while monitoring the infrastructure via APM tools (Datadog, New Relic, or Prometheus) to pinpoint exactly which microservice or database query caused the degradation.

---

## 2. Accessibility Testing (a11y) & Compliance
Accessibility is a fundamental human right and a strict legal requirement globally (e.g., the ADA in the US, and the **European Accessibility Act - EAA**). Ignorance is a legal and financial risk.

### The Automation Boundary for a11y
We automate the baseline, but we acknowledge that true accessibility requires human empathy. Automated tools can catch about 30-40% of WCAG (Web Content Accessibility Guidelines) violations.

* **Automated Gates:** We integrate tools like `axe-core` or `Lighthouse` directly into our UI automation framework (via Selenium/Playwright) and CI pipeline.
    * *Example:* The pipeline will automatically fail if an image is missing an `alt` attribute, or if color contrast ratios fall below WCAG AA standards.
* **Manual & Exploratory a11y:** The remaining 60% requires manual testing. Our exploratory sessions include:
    * Keyboard-only navigation (Can the user checkout without a mouse?).
    * Screen reader compatibility testing (NVDA, VoiceOver).
    * Screen magnification and responsive reflow validation.

---

## 3. Frontend Performance (Web Vitals)
Backend API speed is only half the battle. If the UI takes 5 seconds to render, the user experiences a slow product. We measure and enforce Google's Core Web Vitals:

* **LCP (Largest Contentful Paint):** Must be < 2.5 seconds.
* **FID (First Input Delay) / INP (Interaction to Next Paint):** Must be < 100 milliseconds.
* **CLS (Cumulative Layout Shift):** Must be < 0.1 to prevent UI jumps.

We automate these checks during our UI testing phase to ensure frontend regressions (like an unoptimized 5MB banner image) do not merge into `main`.

---

## 4. The SRE Handshake
Quality Engineering does not operate in a vacuum. The results of our Non-Functional Tests are directly shared with the Site Reliability Engineering (SRE) and DevOps teams. This handshake ensures that our auto-scaling policies, database connection pools, and CDN caching strategies are tuned based on empirical test data, not guesswork.