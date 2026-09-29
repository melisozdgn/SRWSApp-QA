# Smart Recycling Waste System (SRWS) — Application Analysis & Requirements

**Phase:** 0 — Application Analysis
**Analysis method:** Static source code review (no live/dynamic execution performed in this phase)
**Revision:** v1.1 — code re-verified during Phase 3–4; the `[ApiController]` automatic-validation claim was corrected, findings F-11…F-18 were added, acceptance criteria (§5.1) were added.
**Sources analyzed:**
- `https://github.com/Smart-Recycling-Waste-System-SRWS/Backend-SRWS` (`main` branch)
- `https://github.com/Smart-Recycling-Waste-System-SRWS/Frontend-FlutterApp` (`main` branch)

**Purpose:** to base the test strategy, test plan and test cases on the **actual** SRWS codebase rather than an assumed or idealized version of the application. This document is the reference for every subsequent QA phase (Test Strategy, Test Plan, Test Cases, Automation).

---

## 1. Verified System Architecture

```text
                         ┌────────────────────────────┐
                         │   Flutter App (srws_app)    │
                         │  Home / Camera / Gallery    │
                         └──────────────┬───────────────┘
                                        │
                 ┌──────────────────────┼──────────────────────┐
                 │ (1) multipart/form-data: image               │ (2) fire-and-forget JSON
                 ▼                                              ▼
     ┌───────────────────────────┐                  ┌───────────────────────────┐
     │  FastAPI AI Service       │                  │  .NET 8 API               │
     │  POST /api/ai/classify    │                  │  POST /api/scan/history   │
     │  (port 8000)              │                  │  GET  /api/scan/history   │
     │  YOLO (best.pt) inference │                  │  GET  /api/categories     │
     └───────────────────────────┘                  │  (port 5000→8080)         │
                                                     └──────────────┬────────────┘
                                                                    ▼
                                                        ┌────────────────────┐
                                                        │  PostgreSQL 16     │
                                                        │  waste_categories  │
                                                        │  scan_history      │
                                                        └────────────────────┘
```

**Critical observation:** these two backend calls are **independent and not synchronous**:
1. Flutter sends the image **directly to FastAPI**, waits for the result (90 s timeout), and shows it to the user.
2. Once the result arrives, Flutter posts the same result to the .NET API **in the background, fire-and-forget** (`ApiService.saveScanHistory`, `scanner_repository_impl.dart`). Whether this call succeeds is **never checked**, and no warning is shown to the user on failure (see Section 6, F-03).
3. The History screen **never calls** the .NET `GET /api/scan/history` endpoint; it only reads `SharedPreferences` on the device (see Section 6, F-02).

This directly affects test design: "scan history" has **two separate, disconnected data paths** that must be tested independently — one UI/local (SharedPreferences), one API/DB (PostgreSQL). Consistency between the two **cannot be assumed**.

---

## 2. Backend Analysis — `Backend-SRWS`

### 2.1 .NET 8 API (`dotnet_api/`)

| File | Role |
|---|---|
| `Program.cs` | Startup, DI, CORS (`AllowAll` — any origin/method/header), Swagger, automatic `db.Database.Migrate()` |
| `Controllers.cs` | `ScanController`, `CategoriesController` |
| `Models.cs` | EF Core entities: `ScanHistory`, `WasteCategory` |
| `ScanHistoryService.cs` | DTOs (`GuestScanRequest`, `ScanHistoryResponse`) + business logic |
| `AppDbContext.cs` | EF Core context, table mapping, seed data (5 categories) |
| `appsettings.json` | Connection string (placeholder password, overridden via env var in docker-compose) |

**Endpoint inventory (verified from code):**

| Method | Path | Request | Response | Validation observed in code |
|---|---|---|---|---|
| `POST` | `/api/scan/history` | `GuestScanRequest { categoryName, confidenceScore, aiDescription?, deviceId? }` | `200 OK` + `ScanHistoryResponse` | **Partial.** The controller is marked `[ApiController]` with `Nullable` enabled, so malformed JSON, type mismatches and `null`/missing `categoryName` are expected to trigger an automatic `400` (to be confirmed dynamically). However, the DTO has no `[Range]`/`[MaxLength]`/allow-list: the `confidenceScore` range, an empty-string `categoryName`, an unknown category name, and field lengths are **not** checked at the application layer; a missing `confidenceScore` may default to the value-type default `0`. |
| `GET` | `/api/scan/history` | Query: `deviceId?`, `page=1`, `pageSize=20` | `200 OK` + list | No lower/upper bound checks on `page`/`pageSize` (e.g. `page<=0` produces a negative `Skip`, `pageSize=100000` is not capped). |
| `GET` | `/api/categories` | — | `200 OK` + 5 fixed categories | — |

**Database-level constraint with no application-layer counterpart:**
```sql
confidence_score DECIMAL(5,4) NOT NULL
    CHECK (confidence_score >= 0 AND confidence_score <= 1)
```
This directly produces an **API testing scenario**: what does the API return when `confidenceScore = 1.5` is sent? Based on the code, if .NET does not intercept this, the request reaches PostgreSQL and the **CHECK constraint violation** fails at the DB level — this typically surfaces in EF Core as an unhandled exception → **500 Internal Server Error**, not **400 Bad Request**. This is a concrete test case to verify in Phase 5/6 (see Section 6, F-01).

### 2.2 FastAPI AI Service (`fastapi_ai/`)

| File | Role |
|---|---|
| `main.py` | App init, CORS `*`, `/health` endpoint, router include |
| `routers/classify.py` | `POST /api/ai/classify` |
| `services/ai_service.py` | YOLO (Ultralytics) model loading + inference |
| `models/best.pt`, `models/class_names.txt` | Trained model + 6 classes: `Battery, Cardboard, Glass, Metal, Paper, Plastic` |

**Endpoint inventory:**

| Method | Path | Request | Response | Validation observed in code |
|---|---|---|---|---|
| `GET` | `/health` | — | `{status, service}` | — |
| `POST` | `/api/ai/classify` | `multipart/form-data`, field name `file` | `200` → `ClassificationResult{category, confidence, description, recycling_bin, color_hex}` | 1) `content_type` allow-list (`image/jpeg`, `image/png`, `image/jpg`) — **trusts the client-declared header, no real file sniffing** → whether a spoofed/fake `Content-Type` bypasses this must be tested. 2) Size check (**>10 MB**) happens **after the entire file has been read into memory** (`contents = await file.read()`), i.e. a large file is fully processed even before being rejected. 3) No detections (`len(boxes)==0`) → `422` with a user-facing message. |

**Classification logic note:** the model predicts `Cardboard` and `Paper` as separate classes; the service merges them in code into `"Paper & Cardboard"` (`ai_service.py`). `CONF_THRESHOLD = 0.25` is a fixed constant — a reference value for boundary testing.

### 2.3 PostgreSQL (`database/01_schema.sql`)

- `waste_categories` (5 seeded rows, fixed data)
- `scan_history` (UUID PK, DB-level CHECK on `confidence_score` — noted above)
- Index: `device_id`, `scanned_at DESC`

---

## 3. Frontend Analysis — `Frontend-FlutterApp`

### 3.1 Actual screen inventory (current `lib/features/` structure)

| Screen | Location | Note |
|---|---|---|
| Splash | `features/splash/` | Fixed 3-second delay, then navigates to `MainLayout` |
| Home (Sorting) | `features/home/` | Open camera / pick from gallery — **default tab** of the bottom nav (`_currentIndex = 1`) |
| Camera / Scanner | `features/scanner/` | `camera_screen .dart` (note the **trailing space** in the filename — see F-06), `scan_result_sheet.dart` |
| History | `features/history/` | Shows local (SharedPreferences) data only |
| Guide | `features/guide/` | Static recycling guide (`guide_data.dart`) |
| Settings | `features/setting/` | Language switch, About, Sustainability Goals, How to Use, Privacy Policy sub-screens |

> **There is no login/register screen.** The app runs entirely in an anonymous/guest model; a device-based identifier (`deviceId`) was apparently intended in place of user identity but is not consistently used — it is **never passed** in the `saveScanHistory` call (see F-04).

### 3.2 Architecture / layers

- **State management:** `flutter_bloc` (Cubit) — `ScannerCubit`, `HistoryCubit`
- **DI:** `get_it` (`injection_container.dart`) — only the Scanner side is wired into DI; `HistoryCubit` instantiates its own dependency (`LocalHistoryService()`) directly (outside DI, an inconsistent pattern that affects testability, see F-05)
- **Local persistence:** `shared_preferences`, a single key (`scan_history`), JSON string list, **capped at 50 records** (the oldest is dropped beyond 50 — a boundary-value test candidate)
- **Localization:** `flutter gen-l10n`, `en` + `tr`, `AppLocalizations` — but **the History screen is not localized** (all strings are hardcoded English: `"YOUR DEVICE LOG"`, `"Total Scans"`, etc.) — inconsistent with the other 5 screens (see F-07)
- **Network layer:** `http` package, platform-dependent base URL (`10.0.2.2` for Android emulator, `localhost` for iOS) — `AppConstants.baseUrl` is defined but **never used** (dead code; `ApiService` hardcodes its own URLs)

---

## 4. Findings From Static Review (REQUIRE DYNAMIC CONFIRMATION — not bugs yet)

> The following are risk points identified through **code reading**. None has been confirmed by execution yet. In the Bug Tracking phase (Phase 11), only findings **reproduced by a dynamic test** will be filed as official bugs; they are noted here purely to inform test design.

| ID | Layer | Finding | Why it matters |
|---|---|---|---|
| F-01 | .NET API | No application-level `[Range(0,1)]` on `confidenceScore`; only the DB CHECK exists. | Expected behavior is probably `400 Bad Request`; in practice it may return `500`. To be confirmed via negative/boundary testing. |
| F-02 | Flutter ↔ .NET | The History screen never calls `GET /api/scan/history`; it only shows local data. | "Scan history" E2E testing and "API history" testing must be **two separate test scenarios** — one does not substitute for the other. |
| F-03 | Flutter | `ApiService.saveScanHistory`'s `catch (_) {}` silently swallows all errors. | With the backend down/timing out, the user sees no warning at all; a "silent data loss" scenario must be tested. |
| F-04 | Flutter | `deviceId` is never passed in the `saveScanHistory` call (defaults to `null`). | Backend device-based filtering (`GET /api/scan/history?deviceId=`) may in practice never receive a populated value. |
| F-05 | Flutter | `HistoryCubit` bypasses the DI container (`get_it`) and instantiates `LocalHistoryService()` directly. | Makes injecting a mock in widget/unit tests harder; a testability risk. |
| F-06 | Flutter | The filename `camera_screen .dart` has a trailing space. | Can cause path-quoting issues in build/CI scripts; a small but real hygiene finding. |
| F-07 | Flutter | `HistoryScreen` is not localized while the other 5 screens are. ARB keys such as `yourDeviceLog`, `localImpact`, `totalScans`, `recentScans` **exist but are unused**; rank labels (`Beginner`…) and dialog text are also hardcoded. | Must be covered by localization/regression testing. |
| F-08 | Flutter | The Privacy Policy text ("all image processing happens on-device, nothing is sent to a server") **contradicts** the real architecture — the image is in fact sent to FastAPI as multipart form data. | Not a cosmetic UI bug; a potential **misrepresentation / data-privacy-statement inconsistency** — should be noted separately and with priority in the test report. |
| F-09 | FastAPI | The `content_type` check relies on the client's declaration; there is no real magic-byte check. | Sending a `.txt` file with `Content-Type: image/jpeg` is likely accepted → a negative-test candidate. |
| F-10 | .NET API | `GetHistory` has no bounds on `page`/`pageSize`. | Behavior for `page=-1`, `pageSize=0`, or a very large `pageSize` must be tested. |
| F-11 | Flutter ↔ .NET | Android uses `10.0.2.2:8000` for FastAPI but `127.0.0.1:5000` for .NET (inconsistent). | On the emulator, the history record may never reach .NET, and the error is silently swallowed anyway (F-03). |
| F-12 | Flutter (build) | Code imports via `package:srws_app/core/...` and `lib/core/l10n` (lowercase), but the repo folder is `lib/Core/` (capital C). On a case-sensitive filesystem (Linux/CI) `lib/core/...` **cannot be found** (verified with a filesystem check in this environment). | Builds fine on Windows/macOS, fails to compile on Linux CI. Directly affects CI design (see test-plan §3). |
| F-13 | Flutter | In the gallery flow (`_pickImageFromGallery`) only `ScannerAnalysisSuccess` is handled; on failure the user sees no message at all, and there is no loading indicator during analysis. | The camera flow has an error SnackBar, the gallery flow does not → inconsistent error handling (REQ-004). |
| F-14 | Flutter | `CameraScreen` translates the string values `'CameraAccessDenied'` / `'CameraUnknownError'` into localized messages, but nothing in the codebase produces those values; `ScannerCubit` instead emits a fixed Turkish `'Kamera başlatılamadı.'` or the raw `e.toString()`. | REQ-012 (a clear, localized error) is likely not satisfied. |
| F-15 | Flutter | `ApiService` returns the server's `detail` message (on 400/422), but `ScannerRepositoryImpl` discards it and throws the same generic message for every failure ("Could not identify this item. Make sure the backend is running…"). | Wrong format/size (400), "not recognized" (422), and service-down cases are indistinguishable to the user. |
| F-16 | Flutter | The client determines `Content-Type` purely from the file extension (`png` → `image/png`, everything else → `image/jpeg`). | A `.gif`/`.webp`/`.txt` file is sent as `image/jpeg` and may bypass the server's header check due to F-09. |
| F-17 | .NET API | Possible invalid inputs accepted by `POST /api/scan/history`: missing `confidenceScore` (→ `0`), empty `categoryName`, a category name not in the fixed list; a `categoryName` >100 or `deviceId` >255 characters may cause a DB error (→ possible `500`). | Data-quality / correct-status-code issue; to be confirmed via boundary + negative testing. |
| F-18 | Flutter | `ScanResultSheet.getLocalizedBin` matches the server's `recycling_bin` value ("Yellow bin", "Blue bin", "Grey bin", "Red bin") against the keywords `plastic/paper/glass/metal/batter`; none of these keywords appear in those values. | Every category may show "Place in: General Waste Bin" — incorrect recycling guidance (especially for Battery) — high impact, to be confirmed dynamically. |

### 4.1 Acceptance Criteria

| REQ | Acceptance criteria |
|---|---|
| REQ-001 | AC1: Home screen has "Take Photo" and "Select Image" actions. AC2: Camera preview opens if permission is granted. AC3: A JPEG/PNG picked from the gallery is submitted for analysis. |
| REQ-002 | AC1: A valid image returns `200` + `category, confidence, description, recycling_bin, color_hex`. AC2: `category` ∈ {Plastic, Paper & Cardboard, Metal, Battery, Glass}. AC3: `confidence` ∈ [0.25, 1]. AC4: Cardboard/Paper always return as "Paper & Cardboard". AC5: The result screen shows the localized category name, percentage confidence, and the correct recycling bin. |
| REQ-003 | AC1: Anything other than `image/jpeg`/`image/png` → `400` "Only JPEG or PNG images are accepted." AC2: >10 MiB → `400` "File size exceeds 10MB." AC3: Exactly 10 MiB is accepted. AC4: Non-image content sent with a spoofed header is rejected (to be confirmed, F-09). |
| REQ-004 | AC1: An unrecognizable image returns `422` with "Could not identify this item. Please take a clearer photo." AC2: **Both** the camera and gallery flows show the user an understandable error and allow retry; the app does not crash (F-13, F-15). |
| REQ-005 | AC1: Every successful scan is prepended to the list. AC2: A record contains `category, confidence, description, recycling_bin, color_hex, timestamp`. AC3: Capped at 50 records; the oldest is dropped on the 51st. AC4: Records survive an app restart. |
| REQ-006 | AC1: After a successful scan, `POST /api/scan/history` is attempted. AC2: The result screen is unaffected even if the .NET API is down/slow (10 s timeout). AC3: While the service is reachable, the record is found in the DB with the same `categoryName`/`confidenceScore` (F-11). |
| REQ-007 | AC1: Records are listed newest-first. AC2: Total Scans, Today, and Rank (Beginner <5, Recycler <15, Eco-Hero <30, Planet Savior ≥30) are computed correctly. AC3: An empty-state message is shown when there are no records. |
| REQ-008 | AC1: The clear icon opens a confirmation dialog. AC2: Cancel preserves the data. AC3: Delete resets the list and stats. |
| REQ-009 | AC1: `GET /api/categories` → `200`, 5 records. AC2: Each record contains `id, name, colorHex, iconName, recyclingBinColor, description`. AC3: Names are consistent with the category names returned by the AI service. |
| REQ-010 | AC1: The DB rejects `confidence_score` values <0 or >1. AC2: The API returns `400` for an out-of-range value (not `500`). AC3: 0 and 1 are accepted. |
| REQ-011 | AC1: EN and TR can be selected. AC2: The language change applies without a restart. AC3: All user-facing text is localized (except History for now, due to F-07). |
| REQ-012 | AC1: A localized "Camera access denied…" message is shown on permission denial. AC2: A localized generic message is shown on an unknown camera error. AC3: The raw exception text is never shown to the user (F-14). |
| REQ-013 | AC1: Guide lists 7 categories (Plastic, Paper & Cardboard, Glass, Metal, Batteries, E-Waste, Organic Waste). AC2: A category's detail shows an info note and instructions. |
| REQ-014 | AC1: Settings includes a reminder toggle, language, About, Sustainability Goals, How to Use, Privacy Policy. AC2: Sub-screens open and can be navigated back from. AC3: The Privacy Policy content is consistent with actual data flow (F-08). |

---

## 5. Requirements (REQ) — Revised to Match the Real Application

> Note: the original draft's "user can authenticate" requirement was **removed** — the app has no login/register mechanism.

| ID | Requirement |
|---|---|
| REQ-001 | The user shall be able to start a waste-scanning operation by taking a photo with the camera or picking an image from the gallery. |
| REQ-002 | The system shall send the scanned image to the AI service and return the waste category, confidence score, description, and recommended recycling bin. |
| REQ-003 | The AI service shall reject file formats other than JPEG/PNG and files larger than 10 MB. |
| REQ-004 | The AI service shall return a clear error message (422) when no recognizable object is found. |
| REQ-005 | Every successful scan shall be persisted on-device (SharedPreferences), capped at the most recent 50 records. |
| REQ-006 | Every successful scan shall be sent to the .NET API (`POST /api/scan/history`) in the background; failure of this call must not block the main user flow (UI). |
| REQ-007 | The user shall be able to view all past on-device scans from the History screen. |
| REQ-008 | The user shall be able to clear all history, with a confirmation prompt. |
| REQ-009 | The system shall return the 5 fixed waste categories (Plastic, Paper & Cardboard, Metal, Battery, Glass) via `GET /api/categories`. |
| REQ-010 | `confidence_score` shall be constrained to the 0–1 range at the database level; whether this constraint is also enforced at the API level (with a 400 Bad Request) must be verified. |
| REQ-011 | The app shall support English and Turkish, and the language change shall apply immediately, without restarting the app. |
| REQ-012 | A clear error message (SnackBar) shall be shown when camera access is denied or no camera is found. |
| REQ-013 | The user shall be able to access the static recycling guide from the Guide screen. |
| REQ-014 | The user shall be able to change notification and language preferences from Settings, and access About/Privacy Policy information. |

---

## 6. Testable Functions Inventory (the basis of the Traceability Matrix)

| Area | Test level | Automation candidate |
|---|---|---|
| `POST /api/ai/classify` (format/size/success/failure) | API | Postman + Newman |
| `POST /api/scan/history` (validation, confidence boundaries) | API | Postman + Newman |
| `GET /api/scan/history` (pagination, deviceId filter) | API | Postman + Newman |
| `GET /api/categories` | API | Postman + Newman |
| `ScanHistoryService` (DB persistence) | Integration | xUnit + WebApplicationFactory |
| `ScannerCubit` state transitions | Unit | Flutter `bloc_test` |
| `LocalHistoryService` (50-record cap, JSON encode/decode) | Unit | Flutter `test` |
| Camera/Gallery → Result Sheet flow | Widget/Integration | Flutter `integration_test` |
| History listing, empty state, clearing | Widget | Flutter `test` (widget) |
| Language switching (EN↔TR) | Widget | Flutter `test` (widget) |
| Privacy Policy content vs. actual network behavior | Manual / Exploratory | — (manual verification + F-08) |

---

## 7. Environment Information (verified from `docker-compose.yml`)

| Service | Port (host) | Note |
|---|---|---|
| `postgres` | 5432 | `postgres:16-alpine`, healthcheck present |
| `dotnet_api` | 5000 → container 8080 | Swagger: `http://localhost:5000/swagger` |
| `fastapi_ai` | 8000 | Docs: `http://localhost:8000/docs` |

On Android emulator, Flutter attempts to reach `dotnet_api` at **`http://127.0.0.1:5000`** (`api_service.dart`) — this **may be the wrong address** for reaching the host machine from an Android emulator (the host is normally reached via `10.0.2.2`; the FastAPI call correctly uses `10.0.2.2:8000` while the .NET call uses `127.0.0.1:5000` — noted as F-11, to be verified dynamically on the Android emulator).

---

## 8. Out of Scope (as of Phase 0)

- User authentication/authorization (does not exist in the app)
- Push notifications (a switch exists in Settings but appears to have no functional wiring behind it — static/mock, to be confirmed dynamically)
- iOS/Android native performance testing (this QA project's scope is functional + API + integration; performance testing is marked `out-of-scope` as a possible future phase)

---

## 9. Conclusion and Next Step

Phase 0 is complete. This document:
- Reflects the real endpoints, models, screens and data flow,
- Removed the imaginary "auth" scenarios from the original plan,
- Produced 18 static findings (F-01…F-18) — to be dynamically tested in Phase 5/6/9,
- Produced 14 revised requirements (REQ-001…REQ-014) with acceptance criteria.

**Next step:** `docs/test-strategy.md` (Phase 1) — scope/out-of-scope, test levels, test types, environment, entry/exit criteria; written against these findings and requirements.

---

## 10. Revision History

| Version | Change |
|---|---|
| v1.0 | Initial Phase 0 analysis (F-01…F-10, REQ-001…REQ-014) |
| v1.1 | Correction: accounted for `[ApiController]`'s automatic `400` validation (v1.0's "no validation at all" wording was overgeneralized). Added F-11 to the table; added F-12…F-18; added acceptance criteria to the REQs. |
