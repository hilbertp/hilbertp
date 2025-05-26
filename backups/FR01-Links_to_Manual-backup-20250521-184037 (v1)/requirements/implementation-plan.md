# Implementation Plan: FR01 Links to Manual

This document outlines the step-by-step process for developing, testing, and rolling out the manual path documentation link feature for component templates in the integrity-protocolling project.

---

## 1. Backend Rollout

- Deploy the backend with the new `manualPath` field (string, optional) in the ComponentTemplate schema.
- Ensure the backend only stores the path component (not the full URL) for documentation links.
- The backend applies relaxed validation: allows underscores, uppercase, trailing slashes, and only blocks critical format violations. Regex: `^[a-zA-Z0-9-_./#]+$`.
- The backend exposes the following endpoints:
  - `GET /api/component-templates` (list all, includes `manualPath`)
  - `GET /api/component-templates/{id}` (get one, includes `manualPath`)
  - `PUT /api/component-templates/{id}` (update, set `manualPath`)
- Ensure MongoDB schema mapping, persistence, and retrieval for `manualPath`.
- Update OpenAPI documentation and example generators to include `manualPath`.
- Write and run unit and integration tests for persistence, retrieval, and validation.

---

## 2. Frontend Development

### Preparation

- Switch to the frontend repo and create a feature branch.
- Review backend API contract and normalization logic for `manualPath`.

### UI/UX & Validation

- Add an input field for `manualPath` in the component template admin UI.
- Next to the input, display a round question mark ("?") icon. On hover, show a tooltip:
  > You can either paste the full documentation URL (it must start with the hardcoded base URL), or enter just the path part. Allowed characters: letters, numbers, hyphens, underscores, periods, slashes, and hash symbols. Example: `image-components/image-flipping/`
- Validate input on the frontend:
  - Accept either a full URL starting with the base URL, or a path matching the regex: `^[a-zA-Z0-9-_./#]+$`
  - Show a validation error if the input does not meet these requirements.
- When displaying a component template in the workflow editor:
  - Show a documentation link button/icon.
  - If `manualPath` is set, construct the full URL as: `baseUrl + manualPath` and open in a new tab on click.
  - If `manualPath` is empty or not set, show "No manual link provided" or disable the button.

### API Usage

- Fetch a component template: `GET /api/component-templates/{id}`
- Update a component template: `PUT /api/component-templates/{id}`
- List all component templates: `GET /api/component-templates`
- The field `manualPath` is optional and may be null.

### Testing

- Unit test `manualPath` normalization, validation, and UI behavior.
- Integration test: admin sets `manualPath`, user sees and uses documentation link.
- Coordinate with QA for regression/integration tests.

---

## 3. Documentation & Peer Review

- Document changes, requirement deviations, and lessons learned in Markdown.
- Update API docs to reflect new `manualPath` logic.
- Revise peer-review checklist for both frontend and backend.
- Conduct peer reviews and address feedback.

---

## 4. Rollout & Handover

- Merge frontend feature branch after review.
- Deploy frontend to dev namespace for QA.
- After QA approval, plan rollout to stage/prod.
- Finalize documentation and success story.

---

## 5. Success Story & Lessons Learned

- Summarize process, cross-role collaboration, and improvements.
- Document lessons learned and recommendations for future cycles.
