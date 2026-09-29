# Negative Test Cases — AI Classification (FastAPI)

Scope: invalid/corrupted input, format/size violations, and error responses on `POST http://localhost:8000/api/ai/classify`. The **Expected-result basis** column states where the expected result comes from: *Requirement* (a REQ/AC), *Framework* (framework contract), *Interpretation* (inferred from a requirement; to be confirmed with the product owner), *Exploratory* (observation).

## Index

| ID | Title | Priority | REQ | Basis | Finding | Level | Automation |
|---|---|---|---|---|---|---|---|
| [TC-NAI-001](#tc-nai-001) | Missing file part | High | REQ-003 | Framework | — | API | Postman/Newman |
| [TC-NAI-002](#tc-nai-002) | Wrong form field name | Medium | REQ-003 | Framework | — | API | Postman/Newman |
| [TC-NAI-003](#tc-nai-003) | Unsupported content type: GIF | High | REQ-003 | Requirement | — | API | Postman/Newman |
| [TC-NAI-004](#tc-nai-004) | Unsupported content type: WebP | High | REQ-003 | Requirement | — | API | Postman/Newman |
| [TC-NAI-005](#tc-nai-005) | Unsupported content type: PDF | High | REQ-003 | Requirement | — | API | Postman/Newman |
| [TC-NAI-006](#tc-nai-006) | Unsupported content type: Text | High | REQ-003 | Requirement | — | API | Postman/Newman |
| [TC-NAI-007](#tc-nai-007) | Unsupported content type: Binary | High | REQ-003 | Requirement | — | API | Postman/Newman |
| [TC-NAI-008](#tc-nai-008) | File part without a Content-Type | Medium | REQ-003 | Requirement | — | API (curl) | curl script / Postman |
| [TC-NAI-009](#tc-nai-009) | Non-image content spoofed as image/jpeg | Critical | REQ-003 (AC4) | Requirement | F-09 | API | Postman/Newman |
| [TC-NAI-010](#tc-nai-010) | Real GIF spoofed as image/jpeg | High | REQ-003 | Requirement | F-09, F-16 | API | Postman/Newman |
| [TC-NAI-011](#tc-nai-011) | Real WebP spoofed as image/jpeg | High | REQ-003 | Requirement | F-09, F-16 | API | Postman/Newman |
| [TC-NAI-012](#tc-nai-012) | Truncated JPEG | Medium | REQ-003, REQ-004 | Requirement | — | API | Postman/Newman |
| [TC-NAI-013](#tc-nai-013) | Zero-byte file | Medium | REQ-003, REQ-004 | Requirement | — | API | Postman/Newman |
| [TC-NAI-014](#tc-nai-014) | File larger than 10 MiB | High | REQ-003 (AC2) | Requirement | — | API | Postman/Newman |
| [TC-NAI-015](#tc-nai-015) | Very large upload (50 MB) is rejected and the service stays healthy | Medium | REQ-003 | Requirement | — | API (manual/script) | Script |
| [TC-NAI-016](#tc-nai-016) | Blank white image is not classified | High | REQ-004 | Requirement | — | API | Postman/Newman |
| [TC-NAI-017](#tc-nai-017) | Non-waste photo (landscape) is handled gracefully | Medium | REQ-004 | Exploratory | — | API | Postman/Newman |
| [TC-NAI-018](#tc-nai-018) | Wrong HTTP method on classify endpoint | Low | REQ-002 | Framework | — | API | Postman/Newman |
| [TC-NAI-019](#tc-nai-019) | Unknown route | Low | — | Framework | — | API | Postman/Newman |
| [TC-NAI-020](#tc-nai-020) | Multiple file parts in one request | Low | REQ-003 | Exploratory | — | API (curl) | curl script |
| [TC-NAI-021](#tc-nai-021) | Model unavailable is reported as a server-side error | Low | REQ-004 | Interpretation | — | Manual (fault injection) | Manual |
| [TC-NAI-022](#tc-nai-022) | Error responses are JSON with a detail field | Medium | REQ-003, REQ-004 | Framework | — | API | Postman/Newman (schema) |

## TC-NAI-001

| Field | Value |
|---|---|
| **Title** | Missing file part |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send an empty form-data body with no file part at all. |
| **Test Data** | — |
| **Expected Result** | • HTTP 422 (FastAPI validation)<br>• Body is JSON; a `detail` field is present<br>• HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-002

| Field | Value |
|---|---|
| **Title** | Wrong form field name |
| **Priority** | Medium |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send the file under the field name `image` instead of `file`. |
| **Test Data** | `test-data/valid/plastic_bottle.jpg` |
| **Expected Result** | • HTTP 422; `detail` references the missing `file` field |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-003

| Field | Value |
|---|---|
| **Title** | Unsupported content type: GIF |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add a real GIF file to the `file` field; part Content-Type = `image/gif`. |
| **Test Data** | `test-data/invalid/…` (a real GIF file) |
| **Expected Result** | • HTTP 400<br>• `detail` = "Only JPEG or PNG images are accepted." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-004

| Field | Value |
|---|---|
| **Title** | Unsupported content type: WebP |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add a real WebP file to the `file` field; part Content-Type = `image/webp`. |
| **Test Data** | `test-data/invalid/…` (a real WebP file) |
| **Expected Result** | • HTTP 400<br>• `detail` = "Only JPEG or PNG images are accepted." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-005

| Field | Value |
|---|---|
| **Title** | Unsupported content type: PDF |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add a one-page PDF to the `file` field; part Content-Type = `application/pdf`. |
| **Test Data** | `test-data/invalid/…` (a one-page PDF) |
| **Expected Result** | • HTTP 400<br>• `detail` = "Only JPEG or PNG images are accepted." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-006

| Field | Value |
|---|---|
| **Title** | Unsupported content type: Text |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add a plain text file to the `file` field; part Content-Type = `text/plain`. |
| **Test Data** | `test-data/invalid/…` (a plain text file) |
| **Expected Result** | • HTTP 400<br>• `detail` = "Only JPEG or PNG images are accepted." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-007

| Field | Value |
|---|---|
| **Title** | Unsupported content type: Binary |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Add random binary data to the `file` field; part Content-Type = `application/octet-stream`. |
| **Test Data** | `test-data/invalid/…` (random binary data) |
| **Expected Result** | • HTTP 400<br>• `detail` = "Only JPEG or PNG images are accepted." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-008

| Field | Value |
|---|---|
| **Title** | File part without a Content-Type |
| **Priority** | Medium |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API (curl) / curl script / Postman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Omit the Content-Type header on the file part (curl: `-F "file=@x.jpg;type="` or raw multipart). |
| **Test Data** | `plastic_bottle.jpg` |
| **Expected Result** | • HTTP 400 (no Content-Type → not in the allow-list)<br>• HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Postman's UI can always attach a part header; this case is executed via curl. |

## TC-NAI-009

| Field | Value |
|---|---|
| **Title** | Non-image content spoofed as image/jpeg |
| **Priority** | Critical |
| **Requirement** | REQ-003 (AC4) |
| **Expected-result basis** | Requirement |
| **Related finding** | F-09 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a plain-text-content file with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/invalid/fake_image_text.jpg` |
| **Expected Result** | • Rejected: HTTP 4xx (**not** 200, **not** 5xx)<br>• Ideally HTTP 400/415 with a format-related message |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code tries to open the actual content with Pillow; on failure a `422 Could not identify…` is expected. Returning 422 instead of 400 is recorded as an observation (O-01). |

## TC-NAI-010

| Field | Value |
|---|---|
| **Title** | Real GIF spoofed as image/jpeg |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-09, F-16 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a real GIF file with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/invalid/real_gif_as_jpeg.jpg` |
| **Expected Result** | • Rejected (HTTP 400): only JPEG/PNG should be accepted<br>• If 200 is returned, this is a requirement violation (the format check relies on the client's declaration) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Since Pillow can also open GIF/WebP files, this case **may fail**. |

## TC-NAI-011

| Field | Value |
|---|---|
| **Title** | Real WebP spoofed as image/jpeg |
| **Priority** | High |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | F-09, F-16 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a real WebP file with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/invalid/real_webp_as_jpeg.jpg` |
| **Expected Result** | • Rejected (HTTP 400)<br>• If 200 is returned, this is a requirement violation |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-012

| Field | Value |
|---|---|
| **Title** | Truncated JPEG |
| **Priority** | Medium |
| **Requirement** | REQ-003, REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send the first ~50% of the bytes of a valid JPEG, with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/invalid/truncated.jpg` |
| **Expected Result** | • HTTP 4xx (expected 422)<br>• HTTP 500 is not returned; the service stays healthy (`/health` → 200) |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-013

| Field | Value |
|---|---|
| **Title** | Zero-byte file |
| **Priority** | Medium |
| **Requirement** | REQ-003, REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a 0-byte file with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/boundary/empty.jpg` |
| **Expected Result** | • HTTP 4xx (expected 422)<br>• HTTP 500 is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-014

| Field | Value |
|---|---|
| **Title** | File larger than 10 MiB |
| **Priority** | High |
| **Requirement** | REQ-003 (AC2) |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a file sized 10 MiB + 1 byte, with Content-Type `image/jpeg`. |
| **Test Data** | `test-data/boundary/size_10MiB_plus1.jpg` |
| **Expected Result** | • HTTP 400<br>• `detail` = "File size exceeds 10MB." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-015

| Field | Value |
|---|---|
| **Title** | Very large upload (50 MB) is rejected and the service stays healthy |
| **Priority** | Medium |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API (manual/script) / Script |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a 50 MB file; measure the response time. |
| **Test Data** | `test-data/boundary/size_50MB.bin` (not stored in the repo; generated by a script) |
| **Expected Result** | • HTTP 400 (size error)<br>• Response ≤ 30 s<br>• A subsequent `GET /health` → 200 (no crash/memory exhaustion) |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The code reads the entire file into memory before checking its size; memory impact is observed. |

## TC-NAI-016

| Field | Value |
|---|---|
| **Title** | Blank white image is not classified |
| **Priority** | High |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Requirement |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a plain white 640×640 JPEG. |
| **Test Data** | `test-data/invalid/blank_white.jpg` |
| **Expected Result** | • HTTP 422<br>• `detail` = "Could not identify this item. Please take a clearer photo." |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-017

| Field | Value |
|---|---|
| **Title** | Non-waste photo (landscape) is handled gracefully |
| **Priority** | Medium |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a landscape photo containing no waste item. |
| **Test Data** | `test-data/invalid/landscape.jpg` |
| **Expected Result** | • Either HTTP 422 (with the message above) or HTTP 200 + a valid schema<br>• HTTP 5xx is not returned |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | The model may produce a false positive; the result is recorded as an observation. |

## TC-NAI-018

| Field | Value |
|---|---|
| **Title** | Wrong HTTP method on classify endpoint |
| **Priority** | Low |
| **Requirement** | REQ-002 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `GET http://localhost:8000/api/ai/classify`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 405; the `Allow` header includes `POST` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-019

| Field | Value |
|---|---|
| **Title** | Unknown route |
| **Priority** | Low |
| **Requirement** | — |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. `POST http://localhost:8000/api/ai/unknown`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 404; JSON `detail` |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-020

| Field | Value |
|---|---|
| **Title** | Multiple file parts in one request |
| **Priority** | Low |
| **Requirement** | REQ-003 |
| **Expected-result basis** | Exploratory |
| **Related finding** | — |
| **Level / Automation** | API (curl) / curl script |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send two valid image parts under the same `file` field name. |
| **Test Data** | `plastic_bottle.jpg`, `metal_can.jpg` |
| **Expected Result** | • HTTP 200 (single result) or 4xx; HTTP 5xx is not returned |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-NAI-021

| Field | Value |
|---|---|
| **Title** | Model unavailable is reported as a server-side error |
| **Priority** | Low |
| **Requirement** | REQ-004 |
| **Expected-result basis** | Interpretation |
| **Related finding** | — |
| **Level / Automation** | Manual (fault injection) / Manual |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. For this test, `models/best.pt` has been temporarily removed from the container and the service restarted. |
| **Test Steps** | 1. Send `POST http://localhost:8000/api/ai/classify` (multipart/form-data).<br>2. Send a valid image. |
| **Test Data** | `plastic_bottle.jpg` |
| **Expected Result** | • A server error: HTTP 5xx (e.g. 503) — **not** a client error (4xx)<br>• The service stays up, does not crash |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | In the code, a missing model returns `None`, which produces a `422` (O-02). This case observes whether a server-side fault is misreported as a client error. |

## TC-NAI-022

| Field | Value |
|---|---|
| **Title** | Error responses are JSON with a detail field |
| **Priority** | Medium |
| **Requirement** | REQ-003, REQ-004 |
| **Expected-result basis** | Framework |
| **Related finding** | — |
| **Level / Automation** | API / Postman/Newman (schema) |
| **Precondition** | Docker Compose is up; `GET http://localhost:8000/health` → 200. |
| **Test Steps** | 1. Run TC-NAI-003, -014 and -016.<br>2. Validate each response's `Content-Type` and body. |
| **Test Data** | — |
| **Expected Result** | • `Content-Type` is `application/json`<br>• Body is shaped `{"detail": "<text>"}`; not empty |
| **Actual Result** | — |
| **Status** | Not Run |
