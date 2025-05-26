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

- [ ] Backend accepts and normalizes both full URLs and paths for `manualPath`.
- [ ] Only the path, not the full URL, is stored in the database, e.g., */audio-components/add-colored-noise/*
- [ ] Validation logic matches documented requirements (see [Requirement Deviations](../results/01-requirement_deviations.md)).
- [ ] Frontend always displays the documentation button; shows fallback if empty.
- [ ] Red error message shown for critical validation failures.
- [ ] End-to-end test: admin sets manualPath, user sees and uses documentation link.
- [ ] All changes and decisions are documented in Markdown.

---
