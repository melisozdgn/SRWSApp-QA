# Test Plan — Smart Recycling Waste System (SRWS)

**Phase:** 2 — Test Plan
**Depends on:** [`requirements.md`](requirements.md) (Phase 0), [`test-strategy.md`](test-strategy.md) (Phase 1)
**Status:** Draft v1.0

> The strategy document answers "what and why." This plan answers "how, when, who, with what deliverables."

---

## 1. Test Objective

To verify, with repeatable and automatable tests, the correctness of SRWS's core flow (image → AI classification → result display → history record), its behavior in error conditions, and the consistency of its storage layers; and to report any real defects found, with evidence.

**Success criteria:**
- All of REQ-001…REQ-014 are traceable to at least one test case.
- Static findings F-01…F-18 have been dynamically **confirmed or refuted**.
- API and backend tests run automatically in CI on every push/PR.
- Results are produced from actual test runs.

---

## 2. Scope

Scope and out-of-scope items are defined in `test-strategy.md` §2 and are binding for this plan. Summary:

- **In:** FastAPI `/api/ai/classify` + `/health`; .NET `/api/scan/history` (POST/GET) + `/api/categories`; PostgreSQL constraints; Flutter Scanner/History/Settings/Guide flows; EN/TR localization; Privacy Policy consistency.
- **Out:** Authentication (does not exist in the app), actual push delivery, load/performance, formal security audit, YOLO model accuracy, native desktop/iOS platform testing.

---

## 3. Test Approach and Technical Decisions

| Topic | Decision | Rationale |
|---|---|---|
| Application code | **Will not be modified.** The application repos are checked out in CI and run as-is. | The QA project is independent; the SUT is not touched. |
| API tests | Postman collection + Newman | Strategy decision; runs via CLI in CI. |
| Backend integration | xUnit + `WebApplicationFactory`; a real PostgreSQL (Docker service container) for the DB | Consistent with the actual architecture; the CHECK constraint (F-01) can only be verified against a real Postgres — an in-memory provider won't catch it. |
| Flutter tests | Test files are kept in the QA repo; in CI, the Flutter repo is checked out and the test files are **copied** into `test/` and `integration_test/` | Flutter tests depend on the app's own `pubspec.yaml`; the overlay approach is how to run them without modifying the app repo. |
| Flutter test dependencies | `bloc_test`, `mocktail`, `integration_test` are required but currently **absent** from the app's `dev_dependencies` (only `flutter_test`, `flutter_lints`). Added temporarily via `flutter pub add --dev ...` during the CI overlay step, never committed to the app repo. | Setting up a test harness without modifying the application. |
| Flakiness risk (YOLO) | Range assertion instead of exact equality on confidence; verify by category | Model output is image-dependent. |
| Test data | Synthetic/controlled; no real user data | Privacy. |

---

## 4. Test Environment

| Component | Configuration |
|---|---|
| Services | `docker compose up` (Backend-SRWS): PostgreSQL 16-alpine (5432), .NET 8 API (5000→8080), FastAPI / Python 3.11 (8000) |
| API tools | Postman (manual exploration), Newman (automation) |
| Backend testing | .NET 8 SDK, xUnit |
| Flutter | SDK `>=3.0.0 <4.0.0` (pubspec), Android emulator |
| CI | GitHub Actions `ubuntu-latest` |
| Environment variables | Postman environment files: `local` (localhost), `ci` (inside the runner) — `api-tests/postman/environments/` |

**Health check (before every test session):**
1. `GET http://localhost:8000/health` → 200
2. `GET http://localhost:5000/api/categories` → 200, 5 records
3. PostgreSQL connectivity verified indirectly through the .NET API.

**Note on Linux CI and the `Core`/`core` folder name (F-12):** after checking out the Flutter repo in CI, a **temporary** `ln -s Core lib/core` step is applied (never committed to the app repo). Code imports `lib/core/...`, but the folder is `lib/Core/`; the build fails on Linux without this step.

---

## 5. Test Schedule

Rather than calendar dates, a realistic **effort-based** plan for a single-person QA project:

| Sprint | Phases | Output | Estimated effort |
|---|---|---|---|
| S1 | Phase 3–4 | Requirement finalization, manual functional test cases | ~1 week |
| S2 | Phase 5 | Negative / boundary / edge-case test cases, `test-data/` | ~1 week |
| S3 | Phase 6–7 | Postman collection + Newman run, first real findings | ~1 week |
| S4 | Phase 8 | xUnit integration tests (real Postgres) | ~1 week |
| S5 | Phase 9 | Flutter unit/widget/integration tests | ~1.5 weeks |
| S6 | Phase 10–12 | Regression suite, bug reports, finalized test data | ~1 week |
| S7 | Phase 13–15 | GitHub Actions, traceability, final report, README | ~1 week |

**Execution note:** the test documents and automation code are written in this repo, but tests are *actually run* on a local machine with Docker, the .NET SDK and the Flutter SDK, or in GitHub Actions. Consequently every test case's `Actual Result` and `Status` fields remain `—` / `Not Run` until it has genuinely been executed; results are never pre-filled.

---

## 6. Test Data

| Category | Folder | Contents |
|---|---|---|
| Valid | `test-data/valid/` | Small JPEG/PNG samples for each class (Battery, Cardboard, Glass, Metal, Paper, Plastic); valid history JSON payloads |
| Invalid | `test-data/invalid/` | A `.txt`-content file named `.jpg`, a corrupted/truncated JPEG, a PDF, a GIF/WebP, a 0-byte file |
| Boundary | `test-data/boundary/` | Files sized exactly at the boundary (10 MiB−1, 10 MiB, 10 MiB+1); boundary payloads for `confidenceScore` (`-0.0001, 0, 1, 1.0001`); payload for the 50/51-record history scenario |
| Mock | `test-data/mock/` | Fake API responses used in Flutter tests (success, 422, 500, timeout) |

**Note:** classification test images must be royalty-free/self-captured; source and license information is tracked in `test-data/README.md`.

---

## 7. Roles and Responsibilities

| Role | Responsibility |
|---|---|
| QA Engineer | Strategy/plan, test-case design, manual execution, automation, bug reporting, reporting |
| Application owners (SRWS team) | Accepting/fixing bugs, environment-related support (informational, in this portfolio context) |

---

## 8. Defect Management

Bugs are filed to GitHub Issues **only** for findings that have been **dynamically reproduced**.

**Severity**

| Level | Definition |
|---|---|
| Critical | Core flow completely broken / data loss / a seriously misleading privacy statement |
| High | An important function is broken, hard to work around |
| Medium | A function is partially broken or returns the wrong error code/message, a workaround exists |
| Low | Cosmetic, documentation, code hygiene |

**Priority:** High / Medium / Low (fix order, can be set independently of severity).

**Bug template:** ID, Title, Severity, Priority, Environment, Steps to Reproduce, Expected, Actual, Evidence (log/response/screenshot), Related REQ/TC, Status.

**Test case statuses:** `Not Run`, `Pass`, `Fail`, `Blocked`, `Skipped`.

---

## 9. Risks

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | YOLO output is flaky | Medium | Medium | Range/category-based assertions, fixed reference images |
| R2 | The FastAPI image is heavy in CI (YOLO/torch dependencies), long build times/timeouts | Medium | High | Docker layer cache, generous health-wait timeouts; a fast suite on PRs and a full suite nightly if needed |
| R3 | The Flutter overlay approach can break on app pubspec changes | Medium | Medium | Pin the app repo's commit SHA; update deliberately |
| R4 | Android emulator is slow/unstable in CI | High | Medium | Widget tests on PRs; emulator-based integration tests in a separate job (nightly/manual) |
| R5 | The two disconnected history data paths (F-02) complicate test design | Certain | Medium | Explicit tags in test-case IDs: `HIST-LOCAL` / `HIST-API` |
| R6 | Findings may turn out to be non-issues (a static finding that doesn't reproduce dynamically) | Medium | Low | Refuted findings are documented too; bugs are never fabricated |
| R7 | Single-person team, scope creep | Medium | Medium | Sprint-based scope, critical-path priority |
| R8 | The Flutter app fails to build on a Linux runner due to F-12 | Certain | High | Symlink step (§3); alternatively run the Flutter job on `windows-latest`/`macos-latest` |

---

## 10. Entry / Exit Criteria

Phase-to-phase entry/exit criteria are defined in `test-strategy.md` §6–7 and apply to this plan. Additionally, for **test execution**:

**Suspension criterion:** if one of the services fails to start or `/health` fails 3 consecutive attempts → execution is suspended; results are marked `Blocked` until the environment issue is resolved.

**Resumption:** execution resumes once the health check (§4) fully passes.

---

## 11. Deliverables

| Deliverable | Location | Phase |
|---|---|---|
| Application Analysis & Requirements | `docs/requirements.md` | 0/3 |
| Test Strategy | `docs/test-strategy.md` | 1 |
| Test Plan | `docs/test-plan.md` | 2 |
| Manual test cases | `test-cases/` | 4–5 |
| Postman collection + environments | `api-tests/postman/` | 6 |
| Newman run/report configuration | `api-tests/newman/` | 7 |
| xUnit integration tests | `backend-tests/` | 8 |
| Flutter unit/widget/integration tests | `flutter-tests/` | 9 |
| Regression suite definition | `test-cases/regression/` + CI job | 10 |
| Bug reports (GitHub Issues + summary) | `bug-reports/` | 11 |
| Test data | `test-data/` | 12 |
| CI workflow | `.github/workflows/qa.yml` | 13 |
| Traceability Matrix | `docs/traceability-matrix.md` | 14 |
| Final Test Report | `reports/final-test-report.md` | 15 |

---

## 12. Next Step

**Phase 3–4:** Finalize requirements and write manual test cases. Since the REQ list was already produced in Phase 0, Phase 3 becomes the step of reviewing it for testability and enriching it with acceptance criteria; then we move on to Functional test cases.
