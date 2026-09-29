# Functional Test Cases — Scan History API (.NET 8)

Scope: `POST/GET http://localhost:5000/api/scan/history` happy path and basic behavior. Validation/boundary/negative scenarios are in Phase 5 (F-01, F-10, F-17).

> This group covers the **HIST-API** data path (PostgreSQL). On-device history is **HIST-LOCAL** and is covered separately in `flutter-history-local.md` (F-02).

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-HAPI-001](#tc-hapi-001) | Create scan history record with valid payload | Critical | REQ-006 | API | Postman/Newman |
| [TC-HAPI-002](#tc-hapi-002) | Create record without optional fields | High | REQ-006 | API | Postman/Newman |
| [TC-HAPI-003](#tc-hapi-003) | Created record is retrievable via GET | Critical | REQ-006 | Integration / API | Postman/Newman + xUnit |
| [TC-HAPI-004](#tc-hapi-004) | GET filters records by deviceId | High | REQ-006 | Integration / API | Postman/Newman + xUnit |
| [TC-HAPI-005](#tc-hapi-005) | GET returns records newest first | High | REQ-006 | Integration / API | Postman/Newman + xUnit |
| [TC-HAPI-006](#tc-hapi-006) | Default pagination returns at most 20 records | Medium | REQ-006 | Integration / API | Postman/Newman + xUnit |
| [TC-HAPI-007](#tc-hapi-007) | Records persist across API restart | High | REQ-006 | Integration (manual/CI) | Manual + CI script |
| [TC-HAPI-008](#tc-hapi-008) | confidenceScore keeps 4-decimal precision | Medium | REQ-006, REQ-010 | API | Postman/Newman |

## TC-HAPI-001

| Field | Value |
|---|---|
| **Title** | Create scan history record with valid payload |
| **Priority** | Critical |
| **Requirement** | REQ-006 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. `POST http://localhost:5000/api/scan/history`, `Content-Type: application/json`.<br>2. Send the test payload as the body. |
| **Test Data** | `{"categoryName":"Plastic","confidenceScore":0.8765,"aiDescription":"Test scan","deviceId":"qa-device-001"}` |
| **Expected Result** | • HTTP 200 (current implementation returns `Ok`)<br>• `id` is a valid GUID<br>• `categoryName` = `Plastic`<br>• `confidenceScore` = `0.8765`<br>• `aiDescription` = `Test scan`<br>• `scannedAt` is a valid ISO-8601 timestamp, reasonably close to now (UTC) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | REST convention would suggest `201 Created`; this is an observation, not a bug claim. |

## TC-HAPI-002

| Field | Value |
|---|---|
| **Title** | Create record without optional fields |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST /api/scan/history` without `aiDescription` or `deviceId` at all. |
| **Test Data** | `{"categoryName":"Metal","confidenceScore":0.5}` |
| **Expected Result** | • HTTP 200<br>• `aiDescription` is `null`<br>• A record is created (`id` populated) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-003

| Field | Value |
|---|---|
| **Title** | Created record is retrievable via GET |
| **Priority** | Critical |
| **Requirement** | REQ-006 |
| **Level / Automation** | Integration / API / Postman/Newman + xUnit |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create a record using TC-HAPI-001 with a unique `deviceId`.<br>2. Call `GET http://localhost:5000/api/scan/history?deviceId=<same deviceId>`. |
| **Test Data** | `deviceId = qa-<timestamp>` |
| **Expected Result** | • HTTP 200<br>• The array contains exactly 1 record<br>• The record's `id`, `categoryName`, `confidenceScore` match the POST response |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-004

| Field | Value |
|---|---|
| **Title** | GET filters records by deviceId |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Level / Automation** | Integration / API / Postman/Newman + xUnit |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create 2 records for `device-A`, 1 for `device-B`.<br>2. Call `GET /api/scan/history?deviceId=device-A`.<br>3. Call `GET /api/scan/history?deviceId=device-B`. |
| **Test Data** | `device-A`, `device-B` (with a run-specific suffix) |
| **Expected Result** | • First call returns only `device-A` records (2)<br>• Second call returns only the `device-B` record (1) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-005

| Field | Value |
|---|---|
| **Title** | GET returns records newest first |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Level / Automation** | Integration / API / Postman/Newman + xUnit |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create 3 records with the same `deviceId`, 1 s apart, in order.<br>2. Call `GET /api/scan/history?deviceId=<id>`. |
| **Test Data** | 3 different `categoryName` values |
| **Expected Result** | • `scannedAt` values are in descending order<br>• The first element is the most recently created record |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-006

| Field | Value |
|---|---|
| **Title** | Default pagination returns at most 20 records |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Level / Automation** | Integration / API / Postman/Newman + xUnit |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create 25 records with the same `deviceId`.<br>2. Call `GET /api/scan/history?deviceId=<id>`.<br>3. Call `GET ...&page=2`. |
| **Test Data** | 25 records |
| **Expected Result** | • The first response contains 20 records<br>• The `page=2` response contains 5 records<br>• No `id` repeats across the two pages |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-007

| Field | Value |
|---|---|
| **Title** | Records persist across API restart |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Level / Automation** | Integration (manual/CI) / Manual + CI script |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create 1 record with a unique `deviceId`.<br>2. Run `docker compose restart dotnet_api`; wait until `GET /api/categories` returns 200.<br>3. Call `GET /api/scan/history?deviceId=<id>`. |
| **Test Data** | 1 record |
| **Expected Result** | • The record is still returned (persisted in the PostgreSQL volume) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-HAPI-008

| Field | Value |
|---|---|
| **Title** | confidenceScore keeps 4-decimal precision |
| **Priority** | Medium |
| **Requirement** | REQ-006, REQ-010 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Create a record with `confidenceScore = 0.1234`.<br>2. Read the record back via GET. |
| **Test Data** | `0.1234` |
| **Expected Result** | • `confidenceScore` = `0.1234` in both the POST and GET responses (no rounding/loss) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Schema is `DECIMAL(5,4)`; a >4-decimal scenario is in Phase 5. |
