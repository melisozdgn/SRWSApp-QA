# Functional Test Cases — AI Classification (FastAPI)

Scope: `http://localhost:8000/health`, `POST /api/ai/classify` (happy path + contract). Negative and boundary scenarios live in Phase 5 (`negative/`, `edge-cases/`).

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-AI-001](#tc-ai-001) | Health endpoint returns service status | High | REQ-002 | API | Postman/Newman |
| [TC-AI-002](#tc-ai-002) | Classify a clear plastic image → Plastic | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-003](#tc-ai-003) | Classify a clear battery image → Battery | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-004](#tc-ai-004) | Classify a clear cardboard image → Paper & Cardboard | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-005](#tc-ai-005) | Classify a clear paper image → Paper & Cardboard | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-006](#tc-ai-006) | Classify a clear glass image → Glass | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-007](#tc-ai-007) | Classify a clear metal image → Metal | Critical | REQ-002 | API | Postman/Newman |
| [TC-AI-008](#tc-ai-008) | Classify a valid PNG image | High | REQ-001, REQ-002 | API | Postman/Newman |
| [TC-AI-009](#tc-ai-009) | Classification response matches contract | Critical | REQ-002 | API | Postman/Newman (schema assertion) |
| [TC-AI-010](#tc-ai-010) | Classification responds within a QA-defined time budget | Low | REQ-002 | API | Postman/Newman (response time) |

## TC-AI-001

| Field | Value |
|---|---|
| **Title** | Health endpoint returns service status |
| **Priority** | High |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up. |
| **Test Steps** | 1. `GET http://localhost:8000/health`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 200<br>• JSON body contains `status` and `service` fields |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-AI-002

| Field | Value |
|---|---|
| **Title** | Classify a clear plastic image → Plastic |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `plastic_bottle.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/plastic_bottle.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Plastic`<br>• `recycling_bin` = `Yellow bin`<br>• `color_hex` = `#FFC107`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-003

| Field | Value |
|---|---|
| **Title** | Classify a clear battery image → Battery |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `battery_aa.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/battery_aa.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Battery`<br>• `recycling_bin` = `Red bin`<br>• `color_hex` = `#F44336`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty and `description` starts with `HAZARDOUS!` |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-004

| Field | Value |
|---|---|
| **Title** | Classify a clear cardboard image → Paper & Cardboard |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `cardboard_box.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/cardboard_box.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Paper & Cardboard`<br>• `recycling_bin` = `Blue bin`<br>• `color_hex` = `#2196F3`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty ; `category` is never returned as `Cardboard`(merge logic) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-005

| Field | Value |
|---|---|
| **Title** | Classify a clear paper image → Paper & Cardboard |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `paper_sheet.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/paper_sheet.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Paper & Cardboard`<br>• `recycling_bin` = `Blue bin`<br>• `color_hex` = `#2196F3`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty ; `category` is never returned as `Paper` |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-006

| Field | Value |
|---|---|
| **Title** | Classify a clear glass image → Glass |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `glass_jar.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/glass_jar.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Glass`<br>• `recycling_bin` = `Blue bin`<br>• `color_hex` = `#00BCD4`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-007

| Field | Value |
|---|---|
| **Title** | Classify a clear metal image → Metal |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add `metal_can.jpg` to the `file` field (Content-Type `image/jpeg`).<br>3. Send the request. |
| **Test Data** | `test-data/valid/metal_can.jpg` (to be prepared in Phase 12, a clear single-object image) |
| **Expected Result** | • HTTP 200<br>• `category` = `Metal`<br>• `recycling_bin` = `Grey bin`<br>• `color_hex` = `#9E9E9E`<br>• `confidence` is numeric and 0.25 ≤ value ≤ 1<br>• `description` is non-empty |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Model output depends on the image; the reference image is validated against the model beforehand and kept fixed (flakiness risk R1). |

## TC-AI-008

| Field | Value |
|---|---|
| **Title** | Classify a valid PNG image |
| **Priority** | High |
| **Requirement** | REQ-001, REQ-002 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST /api/ai/classify`.<br>2. Add a PNG file to `file` (Content-Type `image/png`). |
| **Test Data** | `test-data/valid/plastic_bottle.png` |
| **Expected Result** | • HTTP 200<br>• Body matches the classification contract (TC-AI-009) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-AI-009

| Field | Value |
|---|---|
| **Title** | Classification response matches contract |
| **Priority** | Critical |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman (schema assertion) |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send a valid image via `POST /api/ai/classify`.<br>2. Validate the response body against the schema. |
| **Test Data** | Any valid image |
| **Expected Result** | • Exactly these fields are present: `category`(string), `confidence`(number), `description`(string), `recycling_bin`(string), `color_hex`(string)<br>• `category` ∈ {Plastic, Paper & Cardboard, Metal, Battery, Glass}<br>• `color_hex` is `#RRGGBB` format<br>• Response `Content-Type` header is `application/json` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-AI-010

| Field | Value |
|---|---|
| **Title** | Classification responds within a QA-defined time budget |
| **Priority** | Low |
| **Requirement** | REQ-002 |
| **Level / Automation** | API / Postman/Newman (response time) |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. Model already warmed up (at least one request already sent). |
| **Test Steps** | 1. Send a valid image via `POST /api/ai/classify`.<br>2. Record the response time. |
| **Test Data** | Any valid image |
| **Expected Result** | Response time ≤ 10 s (local Docker, CPU). |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The threshold is a QA assumption, not a requirement; this is a smoke check, not a performance test. |
