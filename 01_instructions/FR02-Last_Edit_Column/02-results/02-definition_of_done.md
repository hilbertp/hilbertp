# 02-Definition of Done (DoD)

This checklist applies to this feature and ensures it meets the team’s quality and readiness standards.

---

## General DoD (Team-wide)

- [ ] Code is written, peer-reviewed, and merged.
- [ ] Unit tests are written and pass.
- [ ] Integration tests (if applicable) are written and pass.
- [ ] Documentation is updated (API, user-facing, and internal).
- [ ] Feature is deployed to dev and verified by QA.
- [ ] No open critical bugs or regressions.
- [ ] All acceptance criteria for the feature are met.

---

## Feature-Specific Acceptance Criteria

- [x] Backend accepts and normalizes both full URLs and paths for `manualPath`.
- [x] Only the path, not the full URL, is stored in the database, e.g., */audio-components/add-colored-noise/*
- [x] Validation logic matches documented requirements (see [Requirement Deviations](../results/01-requirement_deviations.md)).
- [x] Frontend always displays the documentation button; shows fallback if empty.
- [x] Red error message shown for critical validation failures.
- [x] End-to-end test: admin sets manualPath, user sees and uses documentation link.
- [x] All changes and decisions are documented in Markdown.
- [x] Manual documentation link button in the UI is always visible, opens in a new tab, and uses the correct MUI component for external links with proper security props.
- [x] Validation and normalization logic for manualPath is consistent between frontend and backend.
- [x] All unused imports and type errors are removed; code passes lint and Prettier checks.
- [x] All Vitest tests use explicit imports and pass; test runner issues are resolved.
- [x] The detailed, chronologically structured Erfahrungsbericht is created and inserted into 10-erfahrungsbericht.md.
- [x] All related documentation files (DoD, lessons learned, peer review checklist, etc.) are reviewed and updated to reflect the full scope of the work and learnings, with cross-references to the Erfahrungsbericht for traceability.

---

## Manual Test Scenario: Supplying Only a Path (Not a Full URL)

- [ ] As a user, enter only a path (e.g., `/audio-components/add-colored-noise/`) in the manual documentation field instead of a full URL.
- [ ] Save the component and verify:
    - The backend accepts and stores the path as expected (no normalization or error occurs if the path is valid).
    - The UI displays the documentation link correctly, and it opens the expected manual page in a new tab.
    - No error or warning is shown if the path is valid.
    - If the path is invalid, a clear error message is displayed.
- [ ] Document the observed behavior and update the Erfahrungsbericht if any issues or deviations are found.

---

## Traceability

- See [10-erfahrungsbericht.md](./10-erfahrungsbericht.md) for a detailed, chronologically structured experience report covering all technical, process, and validation learnings for this feature.
- All documentation updates and process improvements are cross-referenced in the relevant Markdown files for maximum transparency and future value.
