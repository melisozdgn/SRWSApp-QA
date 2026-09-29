# Functional Test Cases — Flutter Scanner Flow

Scope: Home → Camera/Gallery → result sheet and post-scan persistence behavior (happy path). Error/negative flows are in Phase 5 (F-13, F-14, F-15, F-16).

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-SCAN-001](#tc-scan-001) | Camera opens from Home | Critical | REQ-001 | E2E / Manual | integration_test (partial) |
| [TC-SCAN-002](#tc-scan-002) | Capture photo and see classification result | Critical | REQ-001, REQ-002 | E2E / Manual | integration_test (partial) |
| [TC-SCAN-003](#tc-scan-003) | Select JPEG from gallery and see result | Critical | REQ-001, REQ-002 | E2E / Manual | integration_test (partial) |
| [TC-SCAN-004](#tc-scan-004) | Select PNG from gallery and see result | High | REQ-001, REQ-002 | E2E / Manual | integration_test (partial) |
| [TC-SCAN-005](#tc-scan-005) | Result sheet shows correct bin guidance per category | Critical | REQ-002 | Widget + Manual | Widget test (fake WasteResult) |
| [TC-SCAN-006](#tc-scan-006) | Confidence is displayed as a rounded percentage | Medium | REQ-002 | Widget | Widget test |
| [TC-SCAN-007](#tc-scan-007) | Successful scan is stored in local history | Critical | REQ-005 | E2E / Manual | integration_test |
| [TC-SCAN-008](#tc-scan-008) | Successful scan is synced to the .NET API | High | REQ-006 | E2E / Integration (manual verification) | Manual |
| [TC-SCAN-009](#tc-scan-009) | Result sheet is localized in Turkish | Medium | REQ-011, REQ-002 | Widget | Widget test (locale tr) |
| [TC-SCAN-010](#tc-scan-010) | Leaving camera screen returns to Home without crash | Medium | REQ-001 | E2E / Manual | integration_test |
| [TC-SCAN-011](#tc-scan-011) | ScannerCubit emits Loading then Success on successful analysis | High | REQ-002 | Unit | bloc_test + mocktail |
| [TC-SCAN-012](#tc-scan-012) | Repository persists result locally after successful analysis | High | REQ-005 | Unit | flutter test + mocktail |

## TC-SCAN-001

| Field | Value |
|---|---|
| **Title** | Camera opens from Home |
| **Priority** | Critical |
| **Requirement** | REQ-001 |
| **Level / Automation** | E2E / Manual / integration_test (partial) |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. Camera permission granted. |
| **Test Steps** | 1. Open the app, land on Home (Sorting).<br>2. Tap **Take Photo**. |
| **Test Data** | — |
| **Expected Result** | • The camera screen opens<br>• The preview shows and "Position item inside the frame" text is displayed |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-002

| Field | Value |
|---|---|
| **Title** | Capture photo and see classification result |
| **Priority** | Critical |
| **Requirement** | REQ-001, REQ-002 |
| **Level / Automation** | E2E / Manual / integration_test (partial) |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. |
| **Test Steps** | 1. Frame a clear plastic bottle in the camera screen.<br>2. Tap the shutter button. |
| **Test Data** | Physical/emulator virtual scene: a plastic bottle |
| **Expected Result** | • A "Scanning & Analyzing..." indicator appears<br>• The result sheet opens: localized category name, `NN% Confidence`, `Place in: <bin>`<br>• **Done** dismisses the sheet |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-003

| Field | Value |
|---|---|
| **Title** | Select JPEG from gallery and see result |
| **Priority** | Critical |
| **Requirement** | REQ-001, REQ-002 |
| **Level / Automation** | E2E / Manual / integration_test (partial) |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. |
| **Test Steps** | 1. Tap **Select Image** on Home.<br>2. Pick `plastic_bottle.jpg` from the gallery. |
| **Test Data** | `test-data/valid/plastic_bottle.jpg` |
| **Expected Result** | • Once analysis completes the result sheet opens<br>• Category is `Plastic` (localized: `Plastic`) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-004

| Field | Value |
|---|---|
| **Title** | Select PNG from gallery and see result |
| **Priority** | High |
| **Requirement** | REQ-001, REQ-002 |
| **Level / Automation** | E2E / Manual / integration_test (partial) |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. |
| **Test Steps** | 1. Select `plastic_bottle.png` via **Select Image**. |
| **Test Data** | `test-data/valid/plastic_bottle.png` |
| **Expected Result** | • The result sheet opens; the request is sent as `image/png` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-005

| Field | Value |
|---|---|
| **Title** | Result sheet shows correct bin guidance per category |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | Widget + Manual / Widget test (fake WasteResult) |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. |
| **Test Steps** | 1. Open the result sheet in sequence for 5 categories (widget test with `recyclingBin` = `Yellow bin`, `Blue bin`, `Grey bin`, `Red bin`). |
| **Test Data** | `WasteResult` inputs with server values (`recycling_bin`) |
| **Expected Result** | • For every category, the `Place in: …` text reflects **that** category's bin (Plastic→Plastic Bin, Battery→Batteries Bin, etc.)<br>• No category shows a generic "General Waste Bin" |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | **F-18 suspicion:** the code maps `recycling_bin` by keyword matching; the expected result here is a requirement interpretation and this test **may fail**. |

## TC-SCAN-006

| Field | Value |
|---|---|
| **Title** | Confidence is displayed as a rounded percentage |
| **Priority** | Medium |
| **Requirement** | REQ-002 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | — |
| **Test Steps** | 1. Render `ScanResultSheet` with `confidence = 0.91`. |
| **Test Data** | `0.91` |
| **Expected Result** | • The screen shows `91% Confidence` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-007

| Field | Value |
|---|---|
| **Title** | Successful scan is stored in local history |
| **Priority** | Critical |
| **Requirement** | REQ-005 |
| **Level / Automation** | E2E / Manual / integration_test |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. Fresh install (empty history). |
| **Test Steps** | 1. Perform TC-SCAN-003.<br>2. Switch to the **History** tab in the bottom nav. |
| **Test Data** | — |
| **Expected Result** | • The new record appears at the top of the list (category + date)<br>• Total Scans = 1 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-008

| Field | Value |
|---|---|
| **Title** | Successful scan is synced to the .NET API |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Level / Automation** | E2E / Integration (manual verification) / Manual |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. Since `deviceId` filtering is unavailable, note the record count beforehand / clear the DB before testing. |
| **Test Steps** | 1. Note the current record count via `GET http://localhost:5000/api/scan/history`.<br>2. Perform TC-SCAN-003.<br>3. A few seconds later, call `GET /api/scan/history` again. |
| **Test Data** | — |
| **Expected Result** | • The record count increases by 1; the newest record has the same `categoryName` and `confidenceScore` |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | **F-11 suspicion:** on the Android emulator the .NET call uses `127.0.0.1:5000`; the record may never arrive. Since `deviceId` is never sent (F-04), the record can't be tied to the device either. |

## TC-SCAN-009

| Field | Value |
|---|---|
| **Title** | Result sheet is localized in Turkish |
| **Priority** | Medium |
| **Requirement** | REQ-011, REQ-002 |
| **Level / Automation** | Widget / Widget test (locale tr) |
| **Precondition** | App language is Turkish. |
| **Test Steps** | 1. Render `ScanResultSheet` with locale `tr` (category Plastic). |
| **Test Data** | `lib/Core/l10n/app_tr.arb` |
| **Expected Result** | • Category name, description, `Confidence`, `Done`, and the bin text are shown using their `app_tr.arb` counterparts |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-010

| Field | Value |
|---|---|
| **Title** | Leaving camera screen returns to Home without crash |
| **Priority** | Medium |
| **Requirement** | REQ-001 |
| **Level / Automation** | E2E / Manual / integration_test |
| **Precondition** | Backend services are up; the app is installed on an Android emulator (English); test images are in the emulator gallery. |
| **Test Steps** | 1. Open the camera with Take Photo.<br>2. Use back navigation. |
| **Test Data** | — |
| **Expected Result** | • Returns to Home; the app does not crash; Take Photo still works afterwards |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-011

| Field | Value |
|---|---|
| **Title** | ScannerCubit emits Loading then Success on successful analysis |
| **Priority** | High |
| **Requirement** | REQ-002 |
| **Level / Automation** | Unit / bloc_test + mocktail |
| **Precondition** | A fake `ScannerRepository` returns a successful `WasteResult`. |
| **Test Steps** | 1. Call `cubit.analyzeFromFile(file)`. |
| **Test Data** | Fake `WasteResult(category: Plastic, confidence: 0.9 …)` |
| **Expected Result** | • State order: `ScannerAnalysisLoading` → `ScannerAnalysisSuccess(result)` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-SCAN-012

| Field | Value |
|---|---|
| **Title** | Repository persists result locally after successful analysis |
| **Priority** | High |
| **Requirement** | REQ-005 |
| **Level / Automation** | Unit / flutter test + mocktail |
| **Precondition** | A fake `IAiService` returns a result; `SharedPreferences.setMockInitialValues({})`. |
| **Test Steps** | 1. Call `ScannerRepositoryImpl.analyzeGalleryImage(file)`.<br>2. Read `LocalHistoryService().getHistory()`. |
| **Test Data** | Fake `WasteResult` |
| **Expected Result** | • `getHistory()` returns 1 record (category, confidence, timestamp populated) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | `ApiService` is static, so the background POST can't be mocked here; it's verified separately at the HTTP level (TC-SCAN-008). |
