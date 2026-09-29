# Test Strategy — Smart Recycling Waste System (SRWS)

**Phase:** 1 — Test Strategy
**Based on:** [`docs/requirements.md`](requirements.md) (Phase 0 — Application Analysis)
**Owner:** QA Engineer (independent test project)
**Status:** Draft v1.0

---

## 1. Purpose

This document defines the overall approach, scope, test levels/types, environment, and entry/exit criteria for testing SRWS (Flutter + .NET 8 + FastAPI + PostgreSQL). It is grounded in the **actual** architecture, endpoint inventory, and static findings (F-01…F-18) produced in Phase 0, not in an assumed application.

This document answers "what will we test and why." "How, when, who" is addressed in `docs/test-plan.md` (Phase 2).

---

## 2. Scope

### 2.1 In Scope

| Area | Rationale |
|---|---|
| FastAPI AI Classification service (`POST /api/ai/classify`, `GET /health`) | The system's core value proposition; format/size/no-detection scenarios are critical |
| .NET 8 Scan History & Categories API (`POST/GET /api/scan/history`, `GET /api/categories`) | Validation gaps (F-01, F-10) carry direct risk |
| PostgreSQL data layer | CHECK-constraint behavior (F-01) and seed-data correctness |
| Flutter — Scanner flow (Camera + Gallery → Result Sheet) | The main user flow; the state machine (`ScannerCubit`) is non-trivial |
| Flutter — History flow (local + background sync) | Two disconnected data paths (F-02) require special attention |
| Flutter — Localization (EN/TR) | Regression risk due to F-07 (History screen not localized) |
| Flutter — Settings, Guide (static screens) | Functionally simpler but part of the user journey |
| Cross-service integration (all 3 services running via Docker Compose) | Real network/timeout behavior is only visible in an integrated environment |
| Consistency between the Privacy Policy text and actual network behavior (F-08) | Risk of misleading the user; covered manually/exploratorily |

### 2.2 Out of Scope

| Area | Rationale |
|---|---|
| Authentication / Authorization | No login/register mechanism exists (confirmed in Phase 0) |
| Actual delivery of push notifications | The Settings switch currently has no functional backing (static/mock); only its UI state is in scope, actual notification delivery is out of scope |
| Load/performance testing (load, stress, soak) | This project is functional + API + integration + regression focused; performance testing could be a separate future phase but is not part of this strategy |
| Penetration testing / formal security audit | Observations like the `AllowAny*` CORS config *will be noted*, but an OWASP-level security assessment is out of scope |
| iOS/macOS/Windows/Linux native platform testing | The app was built Android-first (emulator IPs like `10.0.2.2`); other platforms are out of scope |
| ML accuracy (precision/recall) of the YOLO model | This is not an ML model-evaluation project; the model is treated as a black box — only the API contract (response shape, error codes) is tested |
| Testing with real user data | Only synthetic/controlled test data will be used (`test-data/`) |

---

## 3. Test Levels

```text
        ┌─────────────────────────────┐
        │   E2E / Manual Exploratory   │  ← User flow, Privacy Policy consistency (F-08)
        ├─────────────────────────────┤
        │  Flutter Integration Tests   │  ← Splash→Home→Camera→Result→History
        ├─────────────────────────────┤
        │        API Testing          │  ← Postman/Newman: FastAPI + .NET
        ├─────────────────────────────┤
        │   Backend Integration Tests  │  ← xUnit: Controller→Service→DB (real Postgres)
        ├─────────────────────────────┤
        │  Unit Tests (Flutter + .NET) │  ← Cubit, LocalHistoryService, ScanHistoryService
        └─────────────────────────────┘
```

| Level | Component covered | Tooling |
|---|---|---|
| Unit | `ScannerCubit`, `LocalHistoryService`, `ScanHistoryService` (mocked DbContext) | `flutter_test`/`bloc_test`, xUnit + Moq |
| Integration | .NET Controller → Service → real Postgres (Testcontainers or a Docker Compose test instance) | xUnit + `WebApplicationFactory` |
| API | FastAPI `/api/ai/classify`, .NET `/api/scan/history`, `/api/categories` | Postman → Newman (CI) |
| UI / Widget | Isolated render/interaction testing of screens (Result Sheet, History list, Settings language switch) | `flutter_test` (widget) |
| E2E | Splash → Home → Camera/Gallery → Result → History end-to-end on device/emulator | `integration_test` (Flutter) |
| Regression | Re-running critical flows after a change | Newman + xUnit + the full Flutter test suite, run with a single CI command |

---

## 4. Test Types

| Type | Application area | Example |
|---|---|---|
| **Functional** | The "happy path" for each REQ-XXX | Send a valid JPEG → 200 + correct category |
| **Negative** | Invalid input, missing field, wrong type | Empty `categoryName`, missing `file` field |
| **Boundary** | Limit values | `confidence = 0`, `1`, `-0.0001`, `1.0001`; file size `10MB - 1B` / `10MB` / `10MB + 1B`; history's 50th and 51st record |
| **Error Handling** | Behavior when a service is down / times out | The message shown when FastAPI is down; the silent behavior of `saveScanHistory` when .NET is down (F-03) |
| **Regression** | Confirming critical flows aren't broken | The suite run automatically in CI on every PR |
| **Localization** | EN/TR string correctness and consistency | The History screen not being localized (F-07), tracked as a regression item |
| **Data Integrity / Consistency** | Consistency between the two data paths | Local history vs. .NET DB history in sync? (F-02, F-04) |
| **Security (limited, not a formal audit)** | Observational | Noting the `AllowAny*` CORS config; content-type spoofing (F-09) |
| **Exploratory / Manual** | Undocumented scenarios, Privacy Policy consistency | Dynamically confirming F-08 |

---

## 5. Test Environment

| Component | Version/Configuration | Source |
|---|---|---|
| OS (test machine) | Windows / Linux (any Docker-Desktop-capable machine) | — |
| Orchestration | Docker & Docker Compose | `Backend-SRWS/docker-compose.yml` |
| .NET API | .NET 8, port `5000→8080` | `dotnet_api/Dockerfile` |
| FastAPI AI | Python 3.11, port `8000` | `fastapi_ai/Dockerfile` |
| PostgreSQL | `postgres:16-alpine`, port `5432` | `docker-compose.yml` |
| Flutter | SDK `>=3.0.0 <4.0.0` (pubspec.yaml), Android emulator (API level to be finalized in Phase 2) | `Frontend-FlutterApp/pubspec.yaml` |
| API test client | Postman (manual) + Newman CLI (automation, CI) | — |
| CI runner | GitHub Actions (`ubuntu-latest`) | `.github/workflows/qa.yml` (Phase 13) |

**Known environment risk (F-11):** on the Android emulator, the .NET API call uses `http://127.0.0.1:5000`, while the FastAPI call correctly uses `10.0.2.2:8000`. This inconsistency may mean the .NET history record never arrives at all in the emulator environment — to be verified dynamically during Phase 9 (Flutter Integration Tests) and reported as a bug if confirmed.

---

## 6. Entry Criteria

Before starting a test phase:
- [ ] The relevant service(s) are up via Docker Compose and `/health` (FastAPI) or `/swagger` (.NET) is reachable.
- [ ] The REQ-XXX items to be tested are defined in `docs/requirements.md` and added to the traceability matrix.
- [ ] Test data (`test-data/valid`, `invalid`, `boundary`) is prepared.
- [ ] The previous phase's output (document/code) has been reviewed and merged into `main`.

## 7. Exit Criteria

A test phase is considered complete when:
- [ ] All planned test cases have been executed (**100%** for Critical/High priority, **≥90%** for Medium/Low priority).
- [ ] All **Critical/High** severity bugs found are either resolved or documented as a `Known Issue` in `reports/final-test-report.md`.
- [ ] The CI pipeline (if present) is green (`PASS`).
- [ ] The traceability matrix is up to date (every REQ maps to at least one TC, and every TC to an automation layer where possible).
- [ ] Relevant static findings (F-01…F-18) have either been dynamically **confirmed** (→ bug filed) or **refuted** (→ documented and closed).

---

## 8. Risks and Mitigation

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| YOLO model output may be non-deterministic, causing flaky test results | Medium | Medium | Use range/threshold assertions instead of exact-equality on confidence; prioritize category-name verification |
| Docker environment may fail to start in CI (image size, YOLO dependencies) | Medium | High | Health-check wait steps in CI; generous timeouts |
| Local (SharedPreferences) and API (Postgres) history must be tested separately (F-02) | High (certain) | Medium | Design explicitly labeled, separate test-case groups for each |
| Flutter integration tests require real camera access | High | Medium | Camera-dependent scenarios run against the mock/emulator camera; scenarios requiring a real device are kept as manual tests |

---

## 9. Conclusion and Next Step

This strategy is grounded in the real architecture and findings produced in Phase 0. Next step: **Phase 2 — Test Plan** (`docs/test-plan.md`) — operational details: schedule, responsibilities, deliverables, a more detailed risk/mitigation plan.
