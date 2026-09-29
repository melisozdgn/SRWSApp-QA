# Functional Test Cases — Flutter History (HIST-LOCAL)

Scope: on-device (SharedPreferences) history. This group is the **HIST-LOCAL** data path; the backend history is **HIST-API** (`scan-history-api.md`). The app does not synchronize these two paths (F-02). The 50-record cap is covered as a boundary value in Phase 5.

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-HLOC-001](#tc-hloc-001) | Empty state on fresh install | High | REQ-007 | Widget / Manual | Widget test |
| [TC-HLOC-002](#tc-hloc-002) | History shows stats after first scan | High | REQ-007 | E2E / Manual | integration_test |
| [TC-HLOC-003](#tc-hloc-003) | History lists newest scan first | High | REQ-007 | Widget | Widget test (3 records) |
| [TC-HLOC-004](#tc-hloc-004) | Rank thresholds are calculated correctly | Medium | REQ-007 | Widget | Widget test (parametric) |
| [TC-HLOC-005](#tc-hloc-005) | History persists after app restart | High | REQ-005 | E2E / Manual | integration_test (partial) / Manual |
| [TC-HLOC-006](#tc-hloc-006) | Clear-history dialog can be cancelled | Medium | REQ-008 | Widget | Widget test |
| [TC-HLOC-007](#tc-hloc-007) | Clear-history deletes all records | High | REQ-008 | Widget / E2E | Widget test + integration_test |
| [TC-HLOC-008](#tc-hloc-008) | LocalHistoryService.saveRecord inserts newest at index 0 | High | REQ-005 | Unit | flutter test |
| [TC-HLOC-009](#tc-hloc-009) | HistoryCubit.loadHistory emits Loading then Loaded | High | REQ-007 | Unit | bloc_test |

## TC-HLOC-001

| Field | Value |
|---|---|
| **Title** | Empty state on fresh install |
| **Priority** | High |
| **Requirement** | REQ-007 |
| **Level / Automation** | Widget / Manual / Widget test |
| **Precondition** | App installed; SharedPreferences is clean (fresh install). |
| **Test Steps** | 1. Open the **History** tab. |
| **Test Data** | — |
| **Expected Result** | • "No scans yet" and "Take a photo to get started!" empty-state text is shown<br>• Total Scans = 0 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-002

| Field | Value |
|---|---|
| **Title** | History shows stats after first scan |
| **Priority** | High |
| **Requirement** | REQ-007 |
| **Level / Automation** | E2E / Manual / integration_test |
| **Precondition** | App installed; SharedPreferences is clean (fresh install). |
| **Test Steps** | 1. Perform 1 successful scan.<br>2. Open the History tab. |
| **Test Data** | — |
| **Expected Result** | • Total Scans = 1, Today = 1<br>• Rank = Beginner<br>• The record's category and date are shown |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-003

| Field | Value |
|---|---|
| **Title** | History lists newest scan first |
| **Priority** | High |
| **Requirement** | REQ-007 |
| **Level / Automation** | Widget / Widget test (3 records) |
| **Precondition** | SharedPreferences pre-loaded with 3 records (different timestamps). |
| **Test Steps** | 1. Render the History screen. |
| **Test Data** | 3 records, increasing timestamps |
| **Expected Result** | • Listed newest to oldest |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-004

| Field | Value |
|---|---|
| **Title** | Rank thresholds are calculated correctly |
| **Priority** | Medium |
| **Requirement** | REQ-007 |
| **Level / Automation** | Widget / Widget test (parametric) |
| **Precondition** | — |
| **Test Steps** | 1. Render the History screen with 4, 5, 14, 15, 29, and 30 records respectively. |
| **Test Data** | Record counts: 4,5,14,15,29,30 |
| **Expected Result** | • 4→Beginner; 5→Recycler; 14→Recycler; 15→Eco-Hero; 29→Eco-Hero; 30→Planet Savior |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Thresholds derived from the code (`<5`, `<15`, `<30`). |

## TC-HLOC-005

| Field | Value |
|---|---|
| **Title** | History persists after app restart |
| **Priority** | High |
| **Requirement** | REQ-005 |
| **Level / Automation** | E2E / Manual / integration_test (partial) / Manual |
| **Precondition** | App installed; SharedPreferences is clean (fresh install). |
| **Test Steps** | 1. Perform 1 scan.<br>2. Fully close and reopen the app.<br>3. Open the History tab. |
| **Test Data** | — |
| **Expected Result** | • The record is still listed; Total Scans = 1 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-006

| Field | Value |
|---|---|
| **Title** | Clear-history dialog can be cancelled |
| **Priority** | Medium |
| **Requirement** | REQ-008 |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | At least 1 record exists. |
| **Test Steps** | 1. Tap the clear (delete) icon.<br>2. Choose **Cancel** in the dialog. |
| **Test Data** | — |
| **Expected Result** | • The dialog opens with the title "Clear History" and body "All scan history will be permanently deleted from this device."<br>• Records are preserved after Cancel |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-007

| Field | Value |
|---|---|
| **Title** | Clear-history deletes all records |
| **Priority** | High |
| **Requirement** | REQ-008 |
| **Level / Automation** | Widget / E2E / Widget test + integration_test |
| **Precondition** | At least 3 records exist. |
| **Test Steps** | 1. Tap the clear icon.<br>2. Choose **Delete**. |
| **Test Data** | — |
| **Expected Result** | • The list becomes empty; the empty-state text is shown<br>• Total Scans = 0, Today = 0, Rank = Beginner<br>• The `scan_history` key is removed from SharedPreferences |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-008

| Field | Value |
|---|---|
| **Title** | LocalHistoryService.saveRecord inserts newest at index 0 |
| **Priority** | High |
| **Requirement** | REQ-005 |
| **Level / Automation** | Unit / flutter test |
| **Precondition** | `SharedPreferences.setMockInitialValues({})`. |
| **Test Steps** | 1. Add records A, then B via `saveRecord` in order.<br>2. Call `getHistory()`. |
| **Test Data** | Two `WasteResult` records |
| **Expected Result** | • `getHistory()[0]` = B, `[1]` = A<br>• Each record contains `category, confidence, description, recycling_bin, color_hex, timestamp` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HLOC-009

| Field | Value |
|---|---|
| **Title** | HistoryCubit.loadHistory emits Loading then Loaded |
| **Priority** | High |
| **Requirement** | REQ-007 |
| **Level / Automation** | Unit / bloc_test |
| **Precondition** | SharedPreferences pre-loaded with 2 records. |
| **Test Steps** | 1. Call `cubit.loadHistory()`. |
| **Test Data** | 2 records |
| **Expected Result** | • State order: `HistoryLoading` → `HistoryLoaded` (2 items) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Since `HistoryCubit` builds its own dependency (F-05), `SharedPreferences.setMockInitialValues` is used instead of a mock. |
