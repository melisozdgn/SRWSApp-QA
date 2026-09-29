# Negative Test Cases — Flutter App

Scope: service outages, network errors, malformed server responses, permission/camera errors, gallery edge cases, and corrupted local data. Service outages are induced via `docker compose stop/pause`, malformed responses via a stub server; the application code is never modified.

## Index

| ID | Title | Priority | REQ | Basis | Finding | Level | Automation |
|---|---|---|---|---|---|---|---|
| [TC-NFL-001](#tc-nfl-001) | FastAPI down — camera flow | Critical | REQ-004 | Requirement | — | E2E / Manual | Manual (integration_test candidate) |
| [TC-NFL-002](#tc-nfl-002) | FastAPI down — gallery flow | Critical | REQ-004 (AC2) | Requirement | F-13 | E2E / Manual | Manual (integration_test candidate) |
| [TC-NFL-003](#tc-nfl-003) | Gallery analysis shows a progress indicator | Medium | REQ-004 | Interpretation | F-13 | E2E / Manual | Manual |
| [TC-NFL-004](#tc-nfl-004) | No network connectivity | High | REQ-004 | Requirement | — | E2E / Manual | Manual |
| [TC-NFL-005](#tc-nfl-005) | FastAPI hangs — timeout handling | Medium | REQ-004 | Requirement | — | E2E / Manual | Manual |
| [TC-NFL-006](#tc-nfl-006) | Scan still succeeds when the .NET API is down | Critical | REQ-006 (AC2), REQ-005 | Requirement | — | E2E / Manual | Manual (integration_test candidate) |
| [TC-NFL-007](#tc-nfl-007) | Result is shown immediately when the .NET API is slow | High | REQ-006 (AC2) | Requirement | — | E2E / Manual | Manual |
| [TC-NFL-008](#tc-nfl-008) | Failed backend sync is not observable to the user and is not later reconciled | Low | REQ-006 | Exploratory | F-02, F-03 | E2E / Manual | Manual |
| [TC-NFL-009](#tc-nfl-009) | Server error with a non-JSON body | Medium | REQ-004 | Requirement | — | E2E / Integration | Manual + stub |
| [TC-NFL-010](#tc-nfl-010) | HTTP 200 with missing fields | High | REQ-004 (AC2) | Requirement | F-15 | Integration / Unit | Stub + integration_test / unit |
| [TC-NFL-011](#tc-nfl-011) | HTTP 200 with wrong field types | Medium | REQ-004 (AC2) | Requirement | — | Integration / Unit | Stub / unit |
| [TC-NFL-012](#tc-nfl-012) | Different failure causes are distinguishable | Medium | REQ-003, REQ-004 | Interpretation | F-15 | Integration | Stub + manual |
| [TC-NFL-013](#tc-nfl-013) | Server confidence above 100% | Medium | REQ-002 (AC3) | Interpretation | — | Widget / Integration | Widget test |
| [TC-NFL-014](#tc-nfl-014) | Unknown category and bin from the server render gracefully | Medium | REQ-002 | Interpretation | — | Widget | Widget test |
| [TC-NFL-015](#tc-nfl-015) | Camera permission denied | Critical | REQ-012 (AC1) | Requirement | F-14 | E2E / Manual | Manual |
| [TC-NFL-016](#tc-nfl-016) | Camera permission denied — Turkish | High | REQ-012, REQ-011 | Requirement | F-14 | E2E / Manual | Manual |
| [TC-NFL-017](#tc-nfl-017) | No camera available / unknown camera error | High | REQ-012 (AC2) | Requirement | F-14 | E2E / Manual | Manual |
| [TC-NFL-018](#tc-nfl-018) | Gallery picker cancelled | Medium | REQ-001 | Requirement | — | E2E / Manual | Manual |
| [TC-NFL-019](#tc-nfl-019) | Gallery image larger than 10 MiB | Medium | REQ-003 | Requirement | F-13, F-15 | E2E / Manual | Manual |
| [TC-NFL-020](#tc-nfl-020) | Gallery WebP/GIF image | High | REQ-003 | Requirement | F-16 | E2E / Manual | Manual |
| [TC-NFL-021](#tc-nfl-021) | Double-tap on the capture button | Medium | REQ-001, REQ-005 | Interpretation | — | E2E / Manual | Manual |
| [TC-NFL-022](#tc-nfl-022) | Leaving the screen while analysis is running | Medium | REQ-001 | Interpretation | — | E2E / Manual | Manual |
| [TC-NFL-023](#tc-nfl-023) | App sent to background during analysis | Low | REQ-001 | Exploratory | — | E2E / Manual | Manual |
| [TC-NFL-024](#tc-nfl-024) | Corrupted history entry does not make History unusable | High | REQ-007 | Interpretation | — | Unit / Widget | flutter test |
| [TC-NFL-025](#tc-nfl-025) | History entry with wrong field types | Medium | REQ-007 | Interpretation | — | Unit | flutter test |
| [TC-NFL-026](#tc-nfl-026) | History entry with missing fields | Medium | REQ-007 | Exploratory | — | Unit / Widget | flutter test |
| [TC-NFL-027](#tc-nfl-027) | Clear-history when already empty | Low | REQ-008 | Exploratory | — | Widget | Widget test |

## TC-NFL-001

| Field | Value |
|---|---|
| **Title** | FastAPI down — camera flow |
| **Priority** | Critical |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual (integration_test candidate) |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose stop fastapi_ai`. |
| **Test Steps** | 1. Open the camera with Take Photo and capture a photo. |
| **Test Data** | — |
| **Expected Result** | • The user sees a clear error message (SnackBar)<br>• The app does not crash; the camera can be used again (the OK action restarts it)<br>• The result sheet does not open; no record is added to local history |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-002

| Field | Value |
|---|---|
| **Title** | FastAPI down — gallery flow |
| **Priority** | Critical |
| **Requirement** | REQ-004 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | F-13 |
| **Level / Automation** | E2E / Manual / Manual (integration_test candidate) |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose stop fastapi_ai`. |
| **Test Steps** | 1. Pick a valid JPEG from the gallery via Select Image. |
| **Test Data** | `plastic_bottle.jpg` |
| **Expected Result** | • The user sees an error message (consistent with the camera flow)<br>• The app does not crash; the user can retry<br>• The result sheet does not open |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | In the code, the gallery flow only handles the success case; silent failure is expected here (this case may fail). |

## TC-NFL-003

| Field | Value |
|---|---|
| **Title** | Gallery analysis shows a progress indicator |
| **Priority** | Medium |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-13 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. FastAPI's response is delayed (stub, 5 s delay). |
| **Test Steps** | 1. Select an image via Select Image. |
| **Test Data** | — |
| **Expected Result** | • A progress indicator is shown while analysis is running (the camera flow already shows "Scanning & Analyzing...") |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-004

| Field | Value |
|---|---|
| **Title** | No network connectivity |
| **Priority** | High |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. Emulator is in airplane mode. |
| **Test Steps** | 1. Scan via the camera flow.<br>2. Scan via the gallery flow. |
| **Test Data** | — |
| **Expected Result** | • Both flows show a clear error; no crash<br>• Retry succeeds once connectivity returns |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-005

| Field | Value |
|---|---|
| **Title** | FastAPI hangs — timeout handling |
| **Priority** | Medium |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose pause fastapi_ai` (connection accepted but no response). |
| **Test Steps** | 1. Scan via the camera flow; watch the loading indicator (~90 s). |
| **Test Data** | — |
| **Expected Result** | • An error is shown within ~90 s at the latest; the loading indicator does not spin forever<br>• Retrying works after `docker compose unpause` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-006

| Field | Value |
|---|---|
| **Title** | Scan still succeeds when the .NET API is down |
| **Priority** | Critical |
| **Requirement** | REQ-006 (AC2), REQ-005 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual (integration_test candidate) |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose stop dotnet_api`. |
| **Test Steps** | 1. Perform a valid scan.<br>2. Open the History tab. |
| **Test Data** | `plastic_bottle.jpg` |
| **Expected Result** | • The result sheet opens normally<br>• The record appears in local history<br>• No error is shown to the user (a failed background sync does not block the main flow) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-007

| Field | Value |
|---|---|
| **Title** | Result is shown immediately when the .NET API is slow |
| **Priority** | High |
| **Requirement** | REQ-006 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose pause dotnet_api`. |
| **Test Steps** | 1. Perform a valid scan; measure how long it takes for the result sheet to open. |
| **Test Data** | — |
| **Expected Result** | • The result opens as soon as the AI response arrives; it does **not** wait for .NET's 10 s timeout |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-008

| Field | Value |
|---|---|
| **Title** | Failed backend sync is not observable to the user and is not later reconciled |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | F-02, F-03 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. `docker compose stop dotnet_api`; perform several scans; start the service back up. |
| **Test Steps** | 1. Compare the record count from `GET /api/scan/history` against local History. |
| **Test Data** | — |
| **Expected Result** | • The observation is recorded: if failed syncs are never retried, server records stay incomplete |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | This is not a violation since the requirement says 'best-effort'; the intent is to document the data-loss risk. |

## TC-NFL-009

| Field | Value |
|---|---|
| **Title** | Server error with a non-JSON body |
| **Priority** | Medium |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Integration / Manual + stub |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. A stub FastAPI replaces the real service on port 8000, producing the stated response (Phase 12: `test-data/mock/`). |
| **Test Steps** | 1. Configure the stub to return `500` with an HTML body.<br>2. Perform a scan. |
| **Test Data** | Stub: `500`, `<html>Internal Server Error</html>` |
| **Expected Result** | • The app does not crash; a clear error is shown |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-010

| Field | Value |
|---|---|
| **Title** | HTTP 200 with missing fields |
| **Priority** | High |
| **Requirement** | REQ-004 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | F-15 |
| **Level / Automation** | Integration / Unit / Stub + integration_test / unit |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. A stub FastAPI replaces the real service on port 8000, producing the stated response (Phase 12: `test-data/mock/`). |
| **Test Steps** | 1. Configure the stub to return `200` with `{"category":"Plastic"}`.<br>2. Perform a scan. |
| **Test Data** | Stub body: `{"category":"Plastic"}` |
| **Expected Result** | • The app does not crash<br>• The user sees an understandable message rather than a raw exception (same principle as REQ-012 AC3) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code may surface the type error to the screen via `e.toString()`. |

## TC-NFL-011

| Field | Value |
|---|---|
| **Title** | HTTP 200 with wrong field types |
| **Priority** | Medium |
| **Requirement** | REQ-004 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | Integration / Unit / Stub / unit |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. A stub FastAPI replaces the real service on port 8000, producing the stated response (Phase 12: `test-data/mock/`). |
| **Test Steps** | 1. Configure the stub so `confidence` is text (`"0.9"`).<br>2. Perform a scan. |
| **Test Data** | `{"category":"Plastic","confidence":"0.9","description":"x","recycling_bin":"Yellow bin","color_hex":"#FFC107"}` |
| **Expected Result** | • No crash; a clear error is shown |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-012

| Field | Value |
|---|---|
| **Title** | Different failure causes are distinguishable |
| **Priority** | Medium |
| **Requirement** | REQ-003, REQ-004 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-15 |
| **Level / Automation** | Integration / Stub + manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. A stub FastAPI replaces the real service on port 8000, producing the stated response (Phase 12: `test-data/mock/`). |
| **Test Steps** | 1. Using the stub, produce in sequence: 400 ("Only JPEG or PNG…"), 400 ("File size exceeds 10MB."), 422 (not recognized), and service-down. |
| **Test Data** | — |
| **Expected Result** | • The message shown to the user reflects the cause (format / size / not recognized / service down) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code discards the server's `detail` message and throws the same generic text in every case. |

## TC-NFL-013

| Field | Value |
|---|---|
| **Title** | Server confidence above 100% |
| **Priority** | Medium |
| **Requirement** | REQ-002 (AC3) |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | Widget / Integration / Widget test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Render `ScanResultSheet` with `confidence = 1.7`. |
| **Test Data** | `1.7` |
| **Expected Result** | • A value above 100% (170%) is **not** shown; it is clamped or the result is treated as invalid |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-014

| Field | Value |
|---|---|
| **Title** | Unknown category and bin from the server render gracefully |
| **Priority** | Medium |
| **Requirement** | REQ-002 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Render `ScanResultSheet` with `category = "Foo"`, `recycling_bin = "Purple bin"`. |
| **Test Data** | — |
| **Expected Result** | • No exception/`RenderFlex` error<br>• A reasonable fallback text is shown |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-015

| Field | Value |
|---|---|
| **Title** | Camera permission denied |
| **Priority** | Critical |
| **Requirement** | REQ-012 (AC1) |
| **Expected-result basis** | Requirement |
| **Related finding** | F-14 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. The app's camera permission is denied in system settings. |
| **Test Steps** | 1. Open the camera with Take Photo. |
| **Test Data** | — |
| **Expected Result** | • A localized message is shown: "Camera access denied. Please enable camera permission in settings." (or its Turkish equivalent)<br>• The raw exception text is never shown |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | `ScannerCubit` emits the raw `e.toString()`; nothing produces a `CameraAccessDenied` value — this case may fail. |

## TC-NFL-016

| Field | Value |
|---|---|
| **Title** | Camera permission denied — Turkish |
| **Priority** | High |
| **Requirement** | REQ-012, REQ-011 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-14 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. Language is Turkish; camera permission is denied. |
| **Test Steps** | 1. Open the camera with Take Photo. |
| **Test Data** | `app_tr.arb › cameraAccessDenied` |
| **Expected Result** | • A localized Turkish message is shown (the ARB counterpart, not the hardcoded `Kamera başlatılamadı.` text) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-017

| Field | Value |
|---|---|
| **Title** | No camera available / unknown camera error |
| **Priority** | High |
| **Requirement** | REQ-012 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | F-14 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. Emulator camera disabled (AVD setting: Camera = None). |
| **Test Steps** | 1. Open the camera with Take Photo. |
| **Test Data** | — |
| **Expected Result** | • A localized generic camera error is shown (`cameraUnknownError`)<br>• The app does not crash |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-018

| Field | Value |
|---|---|
| **Title** | Gallery picker cancelled |
| **Priority** | Medium |
| **Requirement** | REQ-001 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Tap Select Image.<br>2. Go back without picking any image while the picker is open. |
| **Test Data** | — |
| **Expected Result** | • Home remains; no error or result sheet appears; no crash |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-019

| Field | Value |
|---|---|
| **Title** | Gallery image larger than 10 MiB |
| **Priority** | Medium |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-13, F-15 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. A ~12 MB PNG is in the gallery (`imageQuality` recompression does not apply to PNG). |
| **Test Steps** | 1. Select this image via Select Image. |
| **Test Data** | `test-data/boundary/size_12MB.png` (generated by a script) |
| **Expected Result** | • The user sees a message that makes it clear rejection was due to size<br>• No crash |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-020

| Field | Value |
|---|---|
| **Title** | Gallery WebP/GIF image |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-16 |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. WebP and GIF images are in the gallery. |
| **Test Steps** | 1. Select a WebP via Select Image.<br>2. Select a GIF via Select Image. |
| **Test Data** | `test-data/invalid/sample.webp`, `sample.gif` |
| **Expected Result** | • Rejected as an unsupported format and the user is informed<br>• The result sheet does not open with a misdeclared format (`image/jpeg`) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The client sends every extension except `png` as `image/jpeg`, and the server doesn't validate the actual content either. |

## TC-NFL-021

| Field | Value |
|---|---|
| **Title** | Double-tap on the capture button |
| **Priority** | Medium |
| **Requirement** | REQ-001, REQ-005 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Tap the shutter button twice, quickly, on the camera screen. |
| **Test Data** | — |
| **Expected Result** | • A single analysis starts, a single result sheet opens<br>• Exactly **1** record is added to local history |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-022

| Field | Value |
|---|---|
| **Title** | Leaving the screen while analysis is running |
| **Priority** | Medium |
| **Requirement** | REQ-001 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. FastAPI's response is delayed (stub, 5 s). |
| **Test Steps** | 1. Select an image from the gallery.<br>2. While analysis is running, switch to another tab via bottom navigation. |
| **Test Data** | — |
| **Expected Result** | • No crash or state error<br>• The record is created exactly once; no stray result sheet appears |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-023

| Field | Value |
|---|---|
| **Title** | App sent to background during analysis |
| **Priority** | Low |
| **Requirement** | REQ-001 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | E2E / Manual / Manual |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Send the app to the background during analysis and bring it back after 10 s. |
| **Test Data** | — |
| **Expected Result** | • The app recovers; either a result or a clear error is shown; no crash |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-024

| Field | Value |
|---|---|
| **Title** | Corrupted history entry does not make History unusable |
| **Priority** | High |
| **Requirement** | REQ-007 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | Unit / Widget / flutter test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Load `SharedPreferences` with `scan_history = ["not-json", <a valid record>]`.<br>2. Call `HistoryCubit.loadHistory()` / open the History screen. |
| **Test Data** | A corrupted + a valid record |
| **Expected Result** | • The app does not crash<br>• The user can recover without reinstalling: either the corrupted record is skipped, **or** it can be cleared via "Clear History" from the error screen<br>• If "Retry" keeps producing the same error, the user is stuck (reported as a finding) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code parses the whole list in a single `try`; one corrupted record drops the entire history into `HistoryError`. |

## TC-NFL-025

| Field | Value |
|---|---|
| **Title** | History entry with wrong field types |
| **Priority** | Medium |
| **Requirement** | REQ-007 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | Unit / flutter test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Load a record shaped `{"category":123,"timestamp":true}`.<br>2. Call `loadHistory()`. |
| **Test Data** | — |
| **Expected Result** | • No crash; either a `HistoryError` (clear message) or the record is skipped |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-026

| Field | Value |
|---|---|
| **Title** | History entry with missing fields |
| **Priority** | Medium |
| **Requirement** | REQ-007 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | Unit / Widget / flutter test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Load a record shaped `{}`.<br>2. Call `loadHistory()`. |
| **Test Data** | `{}` |
| **Expected Result** | • No crash<br>• The record is listed under an "Unknown" category; "now" is used as its date (may inflate the "Today" counter — recorded as an observation) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NFL-027

| Field | Value |
|---|---|
| **Title** | Clear-history when already empty |
| **Priority** | Low |
| **Requirement** | REQ-008 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | Widget / Widget test |
| **Precondition** | The app is running on an Android emulator (English); backend services are up unless stated otherwise. |
| **Test Steps** | 1. Tap the clear icon (if present) on an empty history and choose **Delete**. |
| **Test Data** | — |
| **Expected Result** | • No error; the empty state is preserved |
| **Actual Result** | — |
| **Status** | Not Run |
