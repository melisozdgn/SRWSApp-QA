# Negative Test Cases — Scan History & Categories API (.NET 8)

Scope: malformed requests, invalid fields, type/length/range violations, pagination, and method/route errors on `POST/GET http://localhost:5000/api/scan/history` and `http://localhost:5000/api/categories`. Boundary-value matrices live in `edge-cases/boundary-values.md`. Expected values are defined against the requirements; **HTTP 500 is never an acceptable outcome for any input**.

## Index

| ID | Title | Priority | REQ | Basis | Finding | Level | Automation |
|---|---|---|---|---|---|---|---|
| [TC-NHAPI-001](#tc-nhapi-001) | Empty request body | High | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-002](#tc-nhapi-002) | Malformed JSON | High | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-003](#tc-nhapi-003) | Non-JSON text with a JSON content type | Medium | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-004](#tc-nhapi-004) | Unsupported media type: text/plain | Medium | REQ-006 | Framework | — | API | Postman/Newman |
| [TC-NHAPI-005](#tc-nhapi-005) | Unsupported media type: XML | Low | REQ-006 | Framework | — | API | Postman/Newman |
| [TC-NHAPI-006](#tc-nhapi-006) | JSON array instead of object | Medium | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-007](#tc-nhapi-007) | JSON literal null body | Medium | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-008](#tc-nhapi-008) | Missing categoryName | High | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-009](#tc-nhapi-009) | categoryName is null | High | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-010](#tc-nhapi-010) | categoryName is an empty string | High | REQ-006, REQ-009 | Interpretation | F-17 | API | Postman/Newman |
| [TC-NHAPI-011](#tc-nhapi-011) | categoryName is whitespace only | Medium | REQ-006, REQ-009 | Interpretation | F-17 | API | Postman/Newman |
| [TC-NHAPI-012](#tc-nhapi-012) | categoryName not in the fixed category list | Medium | REQ-009 | Interpretation | F-17 | API | Postman/Newman |
| [TC-NHAPI-013](#tc-nhapi-013) | categoryName with wrong JSON types | Medium | REQ-006 | Requirement | — | API | Postman/Newman (data-driven) |
| [TC-NHAPI-014](#tc-nhapi-014) | Missing confidenceScore | High | REQ-006, REQ-010 | Interpretation | F-17 | API | Postman/Newman |
| [TC-NHAPI-015](#tc-nhapi-015) | confidenceScore is null | High | REQ-006, REQ-010 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-016](#tc-nhapi-016) | confidenceScore with wrong JSON types | Medium | REQ-006, REQ-010 | Requirement | — | API | Postman/Newman (data-driven) |
| [TC-NHAPI-017](#tc-nhapi-017) | confidenceScore = -1 | Critical | REQ-010 | Requirement | F-01 | API | Postman/Newman |
| [TC-NHAPI-018](#tc-nhapi-018) | confidenceScore = 1.5 | Critical | REQ-010 | Requirement | F-01 | API | Postman/Newman |
| [TC-NHAPI-019](#tc-nhapi-019) | confidenceScore causes numeric overflow (≥10) | Medium | REQ-010 | Requirement | F-01 | API | Postman/Newman |
| [TC-NHAPI-020](#tc-nhapi-020) | confidenceScore = 1e30 (beyond decimal range) | Low | REQ-010 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-021](#tc-nhapi-021) | categoryName longer than 100 characters | High | REQ-006 | Requirement | F-17 | API | Postman/Newman |
| [TC-NHAPI-022](#tc-nhapi-022) | deviceId longer than 255 characters | High | REQ-006 | Requirement | F-17 | API | Postman/Newman |
| [TC-NHAPI-023](#tc-nhapi-023) | SQL-injection-like string is treated as data | High | REQ-006 | Exploratory | — | API | Postman/Newman |
| [TC-NHAPI-024](#tc-nhapi-024) | HTML/script content in aiDescription is stored as inert data | Low | REQ-006 | Exploratory | — | API | Postman/Newman |
| [TC-NHAPI-025](#tc-nhapi-025) | Unknown extra property is ignored | Low | REQ-006 | Exploratory | — | API | Postman/Newman |
| [TC-NHAPI-026](#tc-nhapi-026) | Unsupported HTTP methods on scan history | Low | REQ-006 | Framework | — | API | Postman/Newman |
| [TC-NHAPI-027](#tc-nhapi-027) | POST on categories endpoint | Low | REQ-009 | Framework | — | API | Postman/Newman |
| [TC-NHAPI-028](#tc-nhapi-028) | Unknown route | Low | — | Framework | — | API | Postman/Newman |
| [TC-NHAPI-029](#tc-nhapi-029) | page is not a number | Medium | REQ-006 | Framework | — | API | Postman/Newman |
| [TC-NHAPI-030](#tc-nhapi-030) | page = 0 | High | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-031](#tc-nhapi-031) | page = -1 | High | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-032](#tc-nhapi-032) | pageSize = 0 | Medium | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-033](#tc-nhapi-033) | pageSize negative | Medium | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-034](#tc-nhapi-034) | pageSize far above a sane maximum | Medium | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-035](#tc-nhapi-035) | page number causes integer overflow in the offset | Medium | REQ-006 | Interpretation | F-10 | API | Postman/Newman |
| [TC-NHAPI-036](#tc-nhapi-036) | Page beyond last page returns an empty list | Low | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-037](#tc-nhapi-037) | Unknown deviceId returns an empty list | Low | REQ-006 | Requirement | — | API | Postman/Newman |
| [TC-NHAPI-038](#tc-nhapi-038) | deviceId with SQL metacharacters does not leak data | Medium | REQ-006 | Exploratory | — | API | Postman/Newman |
| [TC-NHAPI-039](#tc-nhapi-039) | Empty deviceId returns records from all devices | Medium | REQ-006 | Exploratory | — | API | Postman/Newman |

## TC-NHAPI-001

| Field | Value |
|---|---|
| **Title** | Empty request body |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: (empty) |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-002

| Field | Value |
|---|---|
| **Title** | Malformed JSON |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName": "Plastic",` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-003

| Field | Value |
|---|---|
| **Title** | Non-JSON text with a JSON content type |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `hello world` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-004

| Field | Value |
|---|---|
| **Title** | Unsupported media type: text/plain |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: text/plain`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 415 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-005

| Field | Value |
|---|---|
| **Title** | Unsupported media type: XML |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/xml`).<br>2. Body: `<scan><categoryName>Plastic</categoryName></scan>` |
| **Test Data** | — |
| **Expected Result** | • HTTP 415 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-006

| Field | Value |
|---|---|
| **Title** | JSON array instead of object |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `[]` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-007

| Field | Value |
|---|---|
| **Title** | JSON literal null body |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `null` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-008

| Field | Value |
|---|---|
| **Title** | Missing categoryName |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; the error body references `categoryName` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-009

| Field | Value |
|---|---|
| **Title** | categoryName is null |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":null,"confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-010

| Field | Value |
|---|---|
| **Title** | categoryName is an empty string |
| **Priority** | High |
| **Requirement** | REQ-006, REQ-009 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"","confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (an empty category is invalid)<br>• If 200 is returned, a record with an empty category has been created → a data-quality violation |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-011

| Field | Value |
|---|---|
| **Title** | categoryName is whitespace only |
| **Priority** | Medium |
| **Requirement** | REQ-006, REQ-009 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"   ","confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-012

| Field | Value |
|---|---|
| **Title** | categoryName not in the fixed category list |
| **Priority** | Medium |
| **Requirement** | REQ-009 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Foo","confidenceScore":0.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (the category should be one of the 5 fixed values in REQ-009) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | To be confirmed with the product owner: is the category name free text or a closed list? (Open Question Q-2) |

## TC-NHAPI-013

| Field | Value |
|---|---|
| **Title** | categoryName with wrong JSON types |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman (data-driven) |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName": <value>, "confidenceScore": 0.5}` |
| **Test Data** | See the data set below |
| **Expected Result** | • HTTP 400 for every row; HTTP 500 is never returned |
| **Actual Result** | — |
| **Status** | Not Run |

**Data set (each row = one iteration):**

| # | categoryName value |
|---|---|
| 1 | `123` |
| 2 | `true` |
| 3 | `[]` |
| 4 | `{}` |
| 5 | `["Plastic"]` |

## TC-NHAPI-014

| Field | Value |
|---|---|
| **Title** | Missing confidenceScore |
| **Priority** | High |
| **Requirement** | REQ-006, REQ-010 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic"}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (a required field is missing)<br>• If 200 is returned, the score has been silently stored as `0` → incorrect data |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Since it's a `decimal` value type, a missing field may default to `0`. |

## TC-NHAPI-015

| Field | Value |
|---|---|
| **Title** | confidenceScore is null |
| **Priority** | High |
| **Requirement** | REQ-006, REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":null}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-016

| Field | Value |
|---|---|
| **Title** | confidenceScore with wrong JSON types |
| **Priority** | Medium |
| **Requirement** | REQ-006, REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman (data-driven) |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore": <value>}` |
| **Test Data** | See the data set below |
| **Expected Result** | • HTTP 400 for every row; HTTP 500 is never returned |
| **Actual Result** | — |
| **Status** | Not Run |

**Data set (each row = one iteration):**

| # | confidenceScore value | Note |
|---|---|---|
| 1 | `"abc"` | text |
| 2 | `"0.5"` | numeric-looking string (JSON expects a number) |
| 3 | `true` | boolean |
| 4 | `[]` | array |
| 5 | `{}` | object |

## TC-NHAPI-017

| Field | Value |
|---|---|
| **Title** | confidenceScore = -1 |
| **Priority** | Critical |
| **Requirement** | REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-01 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":-1}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (out of range)<br>• HTTP 500 **must not** occur; no record is created |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-018

| Field | Value |
|---|---|
| **Title** | confidenceScore = 1.5 |
| **Priority** | Critical |
| **Requirement** | REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-01 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":1.5}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (out of range)<br>• HTTP 500 **must not** occur; no record is created<br>• Afterwards, `GET /api/scan/history` confirms no record was created |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | This is the BUG-001 candidate from the plan: a DB CHECK constraint exists but the API layer doesn't validate; a 500 instead of 400 is expected. Only reproduction by execution turns this into a bug. |

## TC-NHAPI-019

| Field | Value |
|---|---|
| **Title** | confidenceScore causes numeric overflow (≥10) |
| **Priority** | Medium |
| **Requirement** | REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-01 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":99999.9999}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400<br>• HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | `DECIMAL(5,4)` stores at most 9.9999; a numeric overflow may occur before the CHECK is evaluated. |

## TC-NHAPI-020

| Field | Value |
|---|---|
| **Title** | confidenceScore = 1e30 (beyond decimal range) |
| **Priority** | Low |
| **Requirement** | REQ-010 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":1e30}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-021

| Field | Value |
|---|---|
| **Title** | categoryName longer than 100 characters |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"<101 × A>","confidenceScore":0.5}` |
| **Test Data** | A 101-character `AAAA…` string |
| **Expected Result** | • HTTP 400<br>• HTTP 500 is not returned (the DB `VARCHAR(100)` limit should be validated at the API level) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-022

| Field | Value |
|---|---|
| **Title** | deviceId longer than 255 characters |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-17 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":0.5,"deviceId":"<256 × D>"}` |
| **Test Data** | A 256-character `DDDD…` string |
| **Expected Result** | • HTTP 400<br>• HTTP 500 is not returned (DB `VARCHAR(255)`) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-023

| Field | Value |
|---|---|
| **Title** | SQL-injection-like string is treated as data |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic'; DROP TABLE scan_history;--","confidenceScore":0.5}`<br>3. Call `GET /api/scan/history` and `GET /api/categories`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 200 (if free text is allowed) or 400 (if a closed list); HTTP 500 is not returned<br>• Tables remain intact: subsequent `GET` calls still return 200<br>• If saved, the value is stored verbatim (not executed as SQL) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | A lightweight security-hygiene check; not a formal security audit (out of scope). |

## TC-NHAPI-024

| Field | Value |
|---|---|
| **Title** | HTML/script content in aiDescription is stored as inert data |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":0.5,"aiDescription":"<script>alert(1)</script>"}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 200 and the text is returned verbatim in the JSON body; response is `application/json` (not interpreted as HTML) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-025

| Field | Value |
|---|---|
| **Title** | Unknown extra property is ignored |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:5000/api/scan/history` (`Content-Type: application/json`).<br>2. Body: `{"categoryName":"Plastic","confidenceScore":0.5,"isAdmin":true}` |
| **Test Data** | — |
| **Expected Result** | • HTTP 200; the response body has no `isAdmin` field |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-026

| Field | Value |
|---|---|
| **Title** | Unsupported HTTP methods on scan history |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `PUT`, `PATCH`, `DELETE` on `http://localhost:5000/api/scan/history`. |
| **Test Data** | — |
| **Expected Result** | • Each returns HTTP 405 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-027

| Field | Value |
|---|---|
| **Title** | POST on categories endpoint |
| **Priority** | Low |
| **Requirement** | REQ-009 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. `POST http://localhost:5000/api/categories`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 405; the category list is unchanged |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-028

| Field | Value |
|---|---|
| **Title** | Unknown route |
| **Priority** | Low |
| **Requirement** | — |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history/123` and `GET http://localhost:5000/api/unknown`. |
| **Test Data** | — |
| **Expected Result** | • Both return HTTP 404 |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-029

| Field | Value |
|---|---|
| **Title** | page is not a number |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?page=abc`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (model-binding error) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-030

| Field | Value |
|---|---|
| **Title** | page = 0 |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?page=0`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (page numbers start at 1)<br>• HTTP 500 **must not** occur (`Skip(-20)` could produce a negative offset) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-031

| Field | Value |
|---|---|
| **Title** | page = -1 |
| **Priority** | High |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?page=-1`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-032

| Field | Value |
|---|---|
| **Title** | pageSize = 0 |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?pageSize=0`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 (invalid page size); HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-033

| Field | Value |
|---|---|
| **Title** | pageSize negative |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?pageSize=-5`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-034

| Field | Value |
|---|---|
| **Title** | pageSize far above a sane maximum |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. 150 records already created with the same `deviceId`. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?deviceId=<id>&pageSize=100000`. |
| **Test Data** | 150 records |
| **Expected Result** | • Either HTTP 400, or the response is capped at a documented maximum (suggested: ≤100)<br>• If all 150 records return unbounded, this confirms the missing upper limit (F-10) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The upper-bound value is undefined (Open Question Q-3). |

## TC-NHAPI-035

| Field | Value |
|---|---|
| **Title** | page number causes integer overflow in the offset |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Interpretation |
| **Related finding** | F-10 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?page=2147483647&pageSize=20`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 400 or HTTP 200 with `[]`; HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The `(page-1)*pageSize` int multiplication can overflow. |

## TC-NHAPI-036

| Field | Value |
|---|---|
| **Title** | Page beyond last page returns an empty list |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?deviceId=<new-id>&page=99999`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 200; body is `[]` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-037

| Field | Value |
|---|---|
| **Title** | Unknown deviceId returns an empty list |
| **Priority** | Low |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?deviceId=does-not-exist-<uuid>`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 200; body is `[]` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-038

| Field | Value |
|---|---|
| **Title** | deviceId with SQL metacharacters does not leak data |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?deviceId=' OR '1'='1` (URL-encoded). |
| **Test Data** | — |
| **Expected Result** | • HTTP 200; body is `[]` (no records from other devices leak) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NHAPI-039

| Field | Value |
|---|---|
| **Title** | Empty deviceId returns records from all devices |
| **Priority** | Medium |
| **Requirement** | REQ-006 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. At least two different `deviceId`s have records. |
| **Test Steps** | 1. Call `GET http://localhost:5000/api/scan/history?deviceId=` (empty).<br>2. Call `GET http://localhost:5000/api/scan/history` (no parameter). |
| **Test Data** | — |
| **Expected Result** | • The behavior is recorded: if both calls return every device's records, there is no data isolation |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code applies no filter when `IsNullOrEmpty(deviceId)` is true (O-05). Since the app has no authentication, this is not a requirement violation, but an observation to clarify with the product owner (Q-4). |
