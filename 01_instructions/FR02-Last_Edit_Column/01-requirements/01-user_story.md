## User Story: Add “Last Edit” Column to Pipeline Table

**As a** user of the data transformation pipelines list,  
**I want** a “Last Edit” column in the list of pipelines,  
**So that** I can track when a pipeline was last changed and sort/filter by that timestamp.

### Acceptance Criteria

- A fifth column titled **“Last Edit”** is added next to the current columns (Name, Version, Description, ID).
- The column displays the **last modified datetime** for each pipeline (ideally in ISO 8601 format).
- The column supports the **same behavior** as the existing columns:
  - Sorting (ascending and descending)
  - Filtering (including date or datetime range filters)
- The UI/UX must be consistent with the current design and interaction patterns.