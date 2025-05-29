# 04-Peer Review Checklist (manualPath Feature)

## Backend

1. **manualPath Extraction & Normalization**
   - Confirm that if a full URL is provided, only the path component is stored in MongoDB (the backend extracts the path from the full URL before persisting).
   - Check that normalization is minimal: trailing slash is added only if required, lowercase conversion only if explicitly specified.

2. **Regex Validation**
   - Ensure the unified regex `^[a-zA-Z0-9-_./#]+$` is used for validation.
   - Validation only blocks critical/harmful characters, not strict formatting.
   - Backend validation is security-focused: only blocks security-relevant or harmful input, not strict formatting.

3. **Error Handling**
   - If validation fails, an `IllegalArgumentException` is thrown and results in a 400 Bad Request.
   - The error message is clear and helps the frontend/user understand the problem.

4. **DTO Adapter Logic**
   - Verify that a local variable (e.g., `normalizedManualPath`) is used for validation and normalization before persisting.
   - Ensure `dto.manualPath` is never used directly in the entity mapping.

5. **Optional Field**
   - Confirm that `manualPath` is optional and does not break existing templates.

6. **Documentation & Tests**
   - Inline comments explain the normalization/validation logic.
   - Tests cover valid/invalid cases, normalization, and error handling.
   - Any deviations from the original requirements are documented in `01-requirement_deviations.md`.

7. **Consistency**
   - Backend validation and normalization logic matches the frontend approach for security, but may be less strict in formatting.

---

## Frontend

1. **UI/UX**
   - `manualPath` input is present in the admin UI for component templates.
   - Tooltip explains allowed formats and provides an example (e.g., `image-components/image-flipping/`).

2. **Validation**
   - Input is validated using the unified regex `^[a-zA-Z0-9-_./#]+$` (matches backend for allowed characters).
   - Accepts either:
     - A full documentation URL starting with the hardcoded base (`https://his.fcas-offen-.../user-manual/documentation/data-transformation/`), or
     - Just the path part (e.g., `image-components/image-flipping/`).
   - Allows anchors (e.g., `#section`), slashes, underscores, hyphens, periods, and both uppercase and lowercase letters.
   - Frontend validation may be stricter (e.g., format, length, or required fields) to guide user input and prevent common mistakes, but always allows all characters permitted by the backend.
   - Clear validation errors are shown for invalid input.

3. **Display Logic**
   - If `manualPath` is set, a documentation link is shown in the UI (e.g., “Open Manual Documentation”).
   - If not set, the link is hidden or disabled.

4. **Error Handling**
   - Backend validation errors for `manualPath` are surfaced in the UI with clear messages.

5. **Testing**
   - Unit and integration tests cover input, validation, normalization, and display logic for `manualPath`.

6. **Documentation**
   - User guidance and help text are updated to reflect the new field and its usage.

7. **Consistency & Cross-Validation**
   - Frontend and backend validation are aligned for security (no harmful input saved), but frontend may be stricter for user experience.
   - Cross-validation: If frontend allows, backend will always block security-relevant input; if backend allows, frontend may still block for UX reasons.

---

## Additional Peer Review Items

- [ ] Manual Test: Supplying Only a Path
  - Manually test entering only a path (e.g., `/audio-components/add-colored-noise/`) in the manual documentation field.
  - Confirm the backend accepts and stores the path as expected, and the UI displays the documentation link correctly.
  - Verify that no error or warning is shown for valid paths, and that invalid paths show a clear error message.
  - Document the observed behavior and update the Erfahrungsbericht and DoD if any issues or deviations are found.

- [ ] Documentation Traceability
  - Confirm that the detailed Erfahrungsbericht is present in `10-erfahrungsbericht.md` and referenced in all related documentation files (DoD, lessons learned, etc.).
  - Ensure all documentation files are updated and cross-referenced for maximum traceability and transparency.

---
**Use this checklist for peer review: Backend and frontend sections are separated for focused review, but both are visible for cross-team questions and consistency checks. Cross-validation notes clarify the relationship between frontend and backend validation.**
