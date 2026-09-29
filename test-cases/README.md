# Test Cases

Manual + automation-candidate test cases. All are written against the real SRWS codebase (see [`docs/requirements.md`](../docs/requirements.md)).

## Format

**ID, Title, Priority, Requirement, Expected-result basis, Related finding, Level/Automation, Precondition, Test Steps, Test Data, Expected Result, Actual Result, Status** (+ Notes). Data-driven cases include an iteration data-set table underneath.

## ID scheme

| Prefix | Area |
|---|---|
| `TC-AI-###` | FastAPI classification (functional) |
| `TC-HAPI-###` | .NET Scan History API — **HIST-API** (functional) |
| `TC-CAT-###` | Categories API (functional) |
| `TC-SCAN-###`, `TC-HLOC-###` | Flutter scanning flow; on-device history **HIST-LOCAL** (functional) |
| `TC-NAV/GUIDE/SET/LOC-###` | Navigation, Guide, Settings, Localization (functional) |
| `TC-NAI-###` | AI service negative |
| `TC-NHAPI-###` | .NET API negative |
| `TC-NFL-###` | Flutter negative |
| `TC-BND-###` | Boundary value analysis |
| `TC-EDG-###` | Unusual input / concurrency / UI robustness / observation |

## Expected-result basis

Shows where the expected result comes from, so the test writer never invents "expected" behavior just by reading the code.

| Value | Meaning |
|---|---|
| Requirement | Derived directly from a REQ and its acceptance criterion |
| Framework | An ASP.NET/FastAPI framework contract (405, 415, 404…) |
| Interpretation | A reasonable inference from a requirement; **to be confirmed with the product owner** |
| Exploratory | An observation; an unexpected outcome is treated as a finding |

## Priority and status

Critical (core flow) · High · Medium · Low. Statuses: `Not Run`, `Pass`, `Fail`, `Blocked`, `Skipped`.

> **Honesty rule:** `Actual Result` and `Status` are filled in only after a test has actually been executed. Cases expected to possibly fail (F-01, F-07, F-14, F-18, etc.) define the expected result based on the **requirement**, not on the current code.

## Counts (Phase 4–5)

| File | Case count |
|---|---|
| `functional/ai-classification.md` | 10 |
| `functional/categories-api.md` | 4 |
| `functional/flutter-history-local.md` | 9 |
| `functional/flutter-navigation-settings-guide-localization.md` | 11 |
| `functional/flutter-scanner.md` | 12 |
| `functional/scan-history-api.md` | 8 |
| `negative/ai-classify-negative.md` | 22 |
| `negative/flutter-negative.md` | 27 |
| `negative/scan-history-negative.md` | 39 |
| `edge-cases/boundary-values.md` | 8 |
| `edge-cases/edge-inputs.md` | 17 |
| **Total** | **167** |

Priority breakdown: Critical: 23, High: 55, Low: 29, Medium: 60.

## Open questions for the product owner

| # | Question | Affected cases |
|---|---|---|
| Q-1 | Is the size limit 10 MB (10,000,000) or 10 MiB (10,485,760)? The code uses MiB, the message says "10MB". | TC-BND-002, TC-NAI-014 |
| Q-2 | Is `categoryName` a closed list (5 categories) or free text? | TC-NHAPI-010…012, TC-BND-003 |
| Q-3 | What is the upper bound for `pageSize`? | TC-NHAPI-033, TC-BND-005 |
| Q-4 | Should all records be returned when `deviceId` is omitted? | TC-NHAPI-039 |
| Q-5 | Should the selected language persist across a restart? | TC-EDG-011 |

Until these are answered, the affected cases remain *Interpretation/Exploratory*, and their results are reported as a question/observation, not a bug.
