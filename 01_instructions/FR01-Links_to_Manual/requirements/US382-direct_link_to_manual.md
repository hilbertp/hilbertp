# User Story 382

**As a** user configuring workflows,
**I want** a direct link in the GUI of each component tempplate to its manual documentation page that opens in a new tab,
**so that I** can quickly access usage guidelines and additional information without disrupting my workflow.

## Acceptance Criteria

1. Each component template in the workflow editor displays a clearly labeled button (e.g., "View Manual" or a question mark icon) instead of showing the full URL.
1. Clicking the button opens the documentation in a new browser tab.
1. The original workflow editor remains active and unaffected

## URL Implementation Details

There is a hardcoded Base URL.

 <https://his.fcas-offen-2bf7313ba140d2ec7addb97cacba3e38-i000.eu-de.containers.appdomain.cloud/user-manual/documentation/data-transformation/>

The user either pastes the whole URL or the last part of the url (Path Component): The database stores only the path component in the `manualPath` field, which must follow this pattern:

 ^([a-z-]+/[a-z-]+/|[a-z-]+/#[a-z-]+)$

Note: The provided regex pattern is up for debate/change and needs to make sure that the `manualPath` field cleanly stores the path of the manual and nothing else. no full URL, no chance for false path injection.

## Component Template Administration

When an admin user configures a component template they can enter either (a) enter the full URL (system will extract just the path component) or (b) enter just the path component directly. The database stores only the path component in MongoDB.
When displaying the documentation link, the frontend shows a button or icon rather than the raw URL

Valid Path Examples (not comprehensive, indicates direction not full scope of validation)

✓ image-components/image-flipping/
✓ load-data/#save-data
✓ audio-components/audio-visualizations/
✓ tabular-data-components/count-missing-values/

Invalid Path Examples

✗ Image-Components/image-flipping/ (uppercase letters)
✗ image_components/image_flipping/ (underscores)
✗ image-components/ (missing second part)
✗ image-components/image-flipping (missing trailing slash)
✗ image-components/#/image-flipping (incorrect anchor format)

this story can be broken down into two subtasks:
/home/philly/sse/integrity-protocolling/ph-dev-instructions/386-manualPath-backend.md
