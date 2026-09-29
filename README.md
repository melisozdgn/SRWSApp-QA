# Smart Recycling Waste System — QA & Test Automation

Independent QA portfolio project for the [Smart Recycling Waste System (SRWS)](https://github.com/Smart-Recycling-Waste-System-SRWS): a Flutter + .NET 8 + FastAPI (YOLO) + PostgreSQL waste-classification app.

This repository does **not** modify the application. It documents and automates the QA process applied to it: analysis, strategy, plan, test cases, API/integration/Flutter automation, CI, defects and reporting.

## System under test

| Component | Repository |
|---|---|
| Backend (.NET 8 API, FastAPI AI service, PostgreSQL, Docker Compose) | [`Backend-SRWS`](https://github.com/Smart-Recycling-Waste-System-SRWS/Backend-SRWS) |
| Mobile app (Flutter, Cubit, GetIt, EN/TR) | [`Frontend-FlutterApp`](https://github.com/Smart-Recycling-Waste-System-SRWS/Frontend-FlutterApp) |

## Repository structure

```text
docs/                  Analysis, strategy, plan, traceability, progress
test-cases/            Manual/automation-candidate test cases
api-tests/             Postman collection + Newman runner
backend-tests/         xUnit integration tests
flutter-tests/         Flutter unit / widget / integration tests
test-data/             Synthetic test data only
bug-reports/           Confirmed, reproduced defects only
reports/               Execution reports
.github/workflows/     CI pipeline
```

## Status

See [`docs/PROGRESS.md`](docs/PROGRESS.md).

## Principles

- Every claim is grounded in the real SRWS code.
- Findings from code reading are hypotheses until a test reproduces them.
- Results and bugs are recorded only after real execution.

*Results, bugs found and the test architecture diagram will be added in the final phase.*
