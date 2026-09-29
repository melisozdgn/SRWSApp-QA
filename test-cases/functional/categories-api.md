# Functional Test Cases — Categories API

Scope: `GET http://localhost:5000/api/categories`.

## Index

| ID | Title | Priority | REQ | Level | Planned automation |
|---|---|---|---|---|---|
| [TC-CAT-001](#tc-cat-001) | Categories endpoint returns 5 categories | High | REQ-009 | API | Postman/Newman |
| [TC-CAT-002](#tc-cat-002) | Category names and bin colors match seed data | High | REQ-009 | API | Postman/Newman |
| [TC-CAT-003](#tc-cat-003) | Category items follow the response schema | Medium | REQ-009 | API | Postman/Newman (schema) |
| [TC-CAT-004](#tc-cat-004) | Category names are consistent with AI service output names | High | REQ-009, REQ-002 | Integration | Newman (cross-service) |

## TC-CAT-001

| Field | Value |
|---|---|
| **Title** | Categories endpoint returns 5 categories |
| **Priority** | High |
| **Requirement** | REQ-009 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. `GET http://localhost:5000/api/categories`. |
| **Test Data** | — |
| **Expected Result** | • HTTP 200<br>• The array contains exactly 5 elements |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-CAT-002

| Field | Value |
|---|---|
| **Title** | Category names and bin colors match seed data |
| **Priority** | High |
| **Requirement** | REQ-009 |
| **Level / Automation** | API / Postman/Newman |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET /api/categories`.<br>2. Compare each record against the expected values. |
| **Test Data** | Plastic/#FFC107/Yellow bin; Paper & Cardboard/#2196F3/Blue bin; Metal/#9E9E9E/Grey bin; Battery/#F44336/Red bin; Glass/#00BCD4/Blue bin |
| **Expected Result** | • The five names and their color/bin values match exactly |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-CAT-003

| Field | Value |
|---|---|
| **Title** | Category items follow the response schema |
| **Priority** | Medium |
| **Requirement** | REQ-009 |
| **Level / Automation** | API / Postman/Newman (schema) |
| **Precondition** | Docker Compose is up; `GET http://localhost:5000/api/categories` → 200. |
| **Test Steps** | 1. Call `GET /api/categories`.<br>2. Validate the schema. |
| **Test Data** | — |
| **Expected Result** | • Every element contains `id`(int), `name`, `colorHex`, `iconName`, `recyclingBinColor`, `description`<br>• `id` values are unique |
| **Actual Result** | — |
| **Status** | Not Run |

## TC-CAT-004

| Field | Value |
|---|---|
| **Title** | Category names are consistent with AI service output names |
| **Priority** | High |
| **Requirement** | REQ-009, REQ-002 |
| **Level / Automation** | Integration / Newman (cross-service) |
| **Precondition** | Both services are up. |
| **Test Steps** | 1. Call `GET /api/categories` → collect the `name` set.<br>2. Collect the `category` values returned in TC-AI-002…007. |
| **Test Data** | — |
| **Expected Result** | • Every AI `category` value exactly matches a `name` in the categories list |
| **Actual Result** | — |
| **Status** | Not Run |
| **Notes** | Cross-service contract consistency; drift risk is high across separately developed services. |
