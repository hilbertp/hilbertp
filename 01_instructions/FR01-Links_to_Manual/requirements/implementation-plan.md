# Distilled Implementation Plan: FR01 Links to Manual (`manualPath`)

This plan is adapted to avoid the bloat and pitfalls from the previous attempt, following the Enhanced AI Development Guidelines and lessons learned. It combines the base version 1 /Users/hilbertp/SSE/backups/FR01-Links_to_Manual-backup-20250521-184037 (v1)/requirements/implementation-plan.md with the knowledge gained in an incrementation attempt and the failures documented in /Users/hilbertp/SSE/integrity-protocolling/ph-dev-instructions/FR01-Links_to_Manual/results/03-lessons_learned.md and /Users/hilbertp/SSE/integrity-protocolling/ph-dev-instructions/FR01-Links_to_Manual/results/09-post-mortem.md

---

## 1. Scope & Constraints

- **In Scope:**  
  - Add `manualPath` (string, optional) to backend ComponentTemplate schema, DTO, and API.
  - Add/edit input for `manualPath` in frontend admin UI, with validation and tooltip.
  - Minimal, focused unit/integration tests for persistence and UI.
  - Documentation updates (API, user guidance).
- **Out of Scope:**  
  - No changes to unrelated microservices, infrastructure, or test infrastructure.
  - No refactoring or redesign of unrelated code.
- **File/Line Limits:**  
  - Target: ≤5 files per side (backend/frontend), ≤100 lines per side.

---

## 2. Backend Steps

1. **Data Model & DTO**
   - Add `manualPath` to `ComponentTemplate` entity and DTO.
   - Update mapping/adapters as needed.

2. **Validation**
   - Apply the unified regex: `^[a-zA-Z0-9-_./#]+$` (allowing underscores, uppercase, trailing slashes, and blocking only critical format violations, as agreed in the latest requirements and lessons learned).
   - Normalize input: if full URL, extract path.

3. **Persistence**
   - Ensure MongoDB mapping and retrieval for `manualPath`.

4. **API**
   - Expose `manualPath` in `GET`/`PUT` endpoints.
   - Update OpenAPI spec.

5. **Testing**
   - Unit test: validation and mapping.
   - Integration test: persistence and retrieval.

---

## 3. Frontend Steps

1. **UI/UX**
   - Add `manualPath` input in admin UI.
   - Add tooltip: explain allowed formats and show example.

2. **Validation**
   - Accept full URL (with base) or path matching the unified regex: `^[a-zA-Z0-9-_./#]+$`.
   - Show clear validation errors.

3. **Display**
   - Show documentation link if `manualPath` is set; disable otherwise.

4. **Testing**
   - Unit test: normalization, validation, UI.
   - Integration test: admin sets `manualPath`, user sees/uses link.

---

## 4. Documentation

- Update API docs and user guidance.
- Add a short section to peer-review checklist for both frontend and backend.

---

## 5. Verification & Troubleshooting

- Use the cross-layer checklist:
  - Field present in UI, API request/response, backend DTO, DB document.
- If persistence fails, use the stepwise diagnostic approach:
  1. Check OpenAPI/DTOs
  2. Test API with Postman/curl
  3. Trace through controller/service/repository
  4. Inspect MongoDB directly
  5. Fix only the minimal layer where the field is lost

---

## 6. Lessons Applied from Last Attempt

- Limit changes to essential files only (no test infra or unrelated refactoring).
- Keep integration tests focused and minimal.
- Avoid redundant or excessive test coverage.
- Use incremental, stepwise implementation and verification.
- Document and communicate any normalization or validation logic clearly in both code and user guidance.

---

# End of Plan
