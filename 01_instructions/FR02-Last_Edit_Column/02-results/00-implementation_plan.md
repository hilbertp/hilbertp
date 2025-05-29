# Implementation Plan: Last Edit Column for Pipeline Table

## Overview
This document outlines the proposed changes required to implement the "Last Edit" column feature for the pipeline table. It details which services and files are affected, API contract changes, endpoints involved, technologies used, and provides a clear Definition of Done (DoD) and acceptance criteria.

---

## Table of Contents
1. Overview
2. Services and Components Affected
3. Out of Scope
4. API Contracts and Endpoints
5. Example API Response
6. Technologies Involved
7. Feature-Specific Acceptance Criteria
8. Backend Unit Test Requirements for lastEdit Field
9. Manual Test Scenario: Last Edit Column
10. General DoD (Team-wide)

---

## 1. Overview
This document outlines the proposed changes required to implement the "Last Edit" column feature for the pipeline table. It details which services and files are affected, API contract changes, endpoints involved, technologies used, and provides a clear Definition of Done (DoD) and acceptance criteria.

---

## 2. Services and Components Affected
- **Backend:**
  - data-transformation-service
    - Entity: `PipelineApiEntity`
    - Versioning: `VersionDetailsEntity`, `VersioningService`
    - Controller: `PipelineController`
    - Service: `PipelineResourceService`
- **Frontend:**
  - user-interface
    - Table component displaying pipelines
    - Type definitions for pipeline data

---

## 3. Out of Scope
- No changes to pipeline creation/deletion logic
- No changes to authentication/authorization
- No modifications to unrelated UI components
- No infrastructure or deployment pipeline changes
- No refactoring of unrelated pipeline management features
- No database migration or backfilling for legacy pipelines

---

## 4. API Contracts and Endpoints
- **Modified Endpoint:**
  - `GET /api/pipelines` (and any other endpoints returning pipeline lists)
    - Add `lastEdit` field (ISO 8601 datetime string) to each pipeline object in the response.
- **Backward Compatibility:**
  - The change is backward compatible; clients not using `lastEdit` will not be affected.
  - For legacy pipelines where `lastEdit` is not available, the frontend will display "n/a".

---

## 5. Example API Response
```json
{
  "id": "pipeline-123",
  "name": "Data Processing Pipeline",
  "version": "1.2.0",
  "description": "Processes customer data",
  "lastEdit": "2025-05-29T10:30:00Z"
}
```

---

## 6. Technologies Involved
- Kotlin (Spring Boot, JPA)
- TypeScript (React, MUI)
- REST API (JSON)
- Unit/Integration Testing (JUnit, Vitest, Playwright, etc.)
- Date formatting library (e.g., date-fns, dayjs) if needed

---

## 7. Feature-Specific Acceptance Criteria
- [x] Backend: `PipelineApiEntity` includes a `lastEdit` field, populated from the latest version timestamp.
- [x] API: All relevant endpoints return the `lastEdit` field in ISO 8601 format.
- [x] API: Handles pipelines with missing or null `lastEdit` values gracefully in the UI (display "n/a").
- [ ] Frontend: Pipeline table displays a new column "Last Edit" with formatted datetime or "n/a" if not available.
- [ ] Frontend: Sorting by "Last Edit" works (asc/desc).
- [ ] Frontend: Filtering by date/datetime range works for "Last Edit".
- [ ] Frontend: Table remains performant with large pipeline lists.
- [ ] Unit tests cover backend and frontend logic for the new field.
- [ ] Integration tests verify end-to-end sorting/filtering workflows.
- [ ] All changes and decisions are documented in Markdown.
- [ ] All related documentation files (DoD, lessons learned, peer review checklist, etc.) are reviewed and updated to reflect the full scope of the work and learnings.

---

## 8. Backend Unit Test Requirements for lastEdit Field

**Plan and Goal:**
- All unit tests for the `lastEdit` field must be real, value-based assertions—not placeholders or comments.
- The test style used in the existing codebase (e.g., `BehaviorSpec`) must be respected and kept consistent across all related test files.
- If a value-based test for `lastEdit` is implemented in a shared abstract base class, it should not be duplicated in concrete test classes unless there is a specific need for additional, adapter-specific logic.
- Placeholder comments (e.g., "TODO: add test for lastEdit") are not acceptable in committed code; every test file must either contain a real test or clearly inherit coverage from a base class.
- The goal is to ensure that the `lastEdit` field is always present and correct in the DTO, and that any future regressions are caught by automated tests. This approach also ensures clarity and maintainability for peer reviewers and future contributors.

- [ ] Add/extend unit tests for the DTO adapter and service layer to verify:
  - [ ] The `lastEdit` field is present in the API response for each pipeline.
  - [ ] The value of `lastEdit` matches the latest version's `createdTimeStamp` (in ISO 8601 format).
  - [ ] The field is `null` for legacy pipelines or when no version information is available.
- [ ] Ensure tests cover both normal and edge cases (e.g., missing version info).
- [ ] Rationale: This ensures the new field is correctly populated, exposed, and robust against regressions or data issues.

---

## 9. Manual Test Scenario: Last Edit Column
- [ ] As a user, view the pipeline table and see a "Last Edit" column with correct datetimes or "n/a" for missing values.
- [ ] Sort the table by "Last Edit" and verify order is correct.
- [ ] Filter pipelines by a date/datetime range and verify only matching pipelines are shown.
- [ ] Confirm the backend returns the correct `lastEdit` value for each pipeline.
- [ ] Test in Chrome, Firefox, Safari (macOS) and verify table functionality is consistent.
- [ ] Verify UI behavior if the `lastEdit` field is missing or malformed (should show "n/a").
- [ ] Document any observed issues or deviations.

---

## 10. General DoD (Team-wide)
- [ ] Code is written, peer-reviewed, and merged.
- [ ] Unit tests are written and pass.
- [ ] Integration tests (if applicable) are written and pass.
- [ ] Documentation is updated (API, user-facing, and internal).
- [ ] Feature is deployed to dev and verified by QA in the dev namespace.
- [ ] No open critical bugs or regressions.
- [ ] All acceptance criteria for the feature are met.

---