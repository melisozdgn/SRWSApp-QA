# Project Progress

| Phase | Status | Deliverable |
|---|---|---|
| 0 — Application Analysis | ✅ Done (v1.1) | [`requirements.md`](requirements.md) |
| 1 — Test Strategy | ✅ Done | [`test-strategy.md`](test-strategy.md) |
| 2 — Test Plan | ✅ Done | [`test-plan.md`](test-plan.md) |
| 3 — Requirements & Acceptance Criteria | ✅ Done | [`requirements.md`](requirements.md) §5, §4.1 |
| 4 — Test Cases (Functional) | ✅ Written, 54 cases, **not yet executed** | [`../test-cases/`](../test-cases/) |
| 5 — Negative / Edge Cases | ✅ Written, 113 cases (39 API, 22 AI, 27 Flutter, 25 boundary/edge), **not yet executed** | [`../test-cases/negative/`](../test-cases/negative/), [`../test-cases/edge-cases/`](../test-cases/edge-cases/) |
| 6 — API Testing (Postman) | ⏳ Not started | |
| 7 — Newman Automation | ⏳ Not started | |
| 8 — Backend Integration Tests (xUnit) | ⏳ Not started | |
| 9 — Flutter Tests | ⏳ Not started | |
| 10 — Regression Suite | ⏳ Not started | |
| 11 — Bug Reports | ⏳ Not started | |
| 12 — Test Data | ⏳ Not started | |
| 13 — GitHub Actions (CI/CD) | ⏳ Not started | |
| 14 — Traceability Matrix | ⏳ Not started | |
| 15 — Final Test Report | ⏳ Not started | |
| 16 — Final README | ⏳ Not started | |

## Static findings status

F-01…F-18 are **static-analysis hypotheses**. None is a confirmed bug until reproduced by an executed test (see Phase 11).

## Open questions for the product owner

Q-1…Q-5 (ambiguous expected behavior — size unit, category as a closed list, pagination max, deviceId-less query, language persistence) are tracked in [`../test-cases/README.md`](../test-cases/README.md#open-questions-for-the-product-owner). Answers will convert the affected test cases from *Interpretation/Exploratory* to *Requirement*-based.
