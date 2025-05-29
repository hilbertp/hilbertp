# Component Manual URL Implementation Details

## Overview

We are adding a `manualPath` field to component templates to allow users to access documentation directly from the workflow editor.

## API Integration Details

To fetch and update the `manualPath` for a component template, the frontend must use the following API endpoints:

- **Get a component template (including manualPath):**
  - **Endpoint:** `GET /api/component-templates/{id}`
  - **Usage:** Call this endpoint when displaying or editing a component template to retrieve the current `manualPath`.
  - **Response Example:**

    ```json
    {
      "id": "component-template-123",
      "name": "Image Flipping",
      "manualPath": "image-components/image-flipping/"
      // ...other fields...
    }
    ```

- **Update a component template (set manualPath):**
  - **Endpoint:** `PUT /api/component-templates/{id}`
  - **Usage:** Call this endpoint when saving changes to a component template, including updates to the `manualPath`.
  - **Request Example:**

    ```json
    {
      "id": "component-template-123",
      "name": "Image Flipping",
      "manualPath": "image-components/image-flipping/"
      // ...other fields...
    }
    ```

  - **Response:** Returns the updated component template object.

- **List all component templates:**
  - **Endpoint:** `GET /api/component-templates`
  - **Usage:** Call this endpoint to retrieve all component templates, each with their `manualPath` field.
  - **Response:** Array of component template objects, each with a `manualPath` field.

**Note:**

- The field manualPath is optional and may be null.
- The frontend must use these endpoints to fetch and update the manualPath as part of the component template object.

## Implementation Details

- The backend stores only the path component (not the full URL) in MongoDB.
- The frontend constructs the full documentation URL by combining a hardcoded base URL with the backend-stored path `manualPath`.

**Base URL (hardcoded in frontend):**  
`https://his.fcas-offen-2bf7313ba140d2ec7addb97cacba3e38-i000.eu-de.containers.appdomain.cloud/user-manual/documentation/data-transformation/`

- The backend delivers regex-verified paths.

## Component Template Administration

- When creating or updating component templates, admins can specify the documentation path in the configuration form.
- Input validation: Only valid path formats are accepted (see below).
- Only the variable path component is stored, not the full URL.

## Frontend Behavior

- Display a documentation link button on component instances.
- Construct the full URL by combining the hardcoded base URL + stored path.
- Open in a new tab when clicked.
- Show "No manual link provided" when empty.

## User Input Guidance for manualPath Field

- The frontend must provide a clear hint or help text to the user:
  - The user can either paste the full documentation URL (it must start with the hardcoded base URL), or
  - The user can enter just the path part, but it must match the allowed pattern: only letters, numbers, hyphens, underscores, periods, slashes, and hash symbols are allowed (regex: ^[a-zA-Z0-9-_./#]+$).
- If the input does not meet these requirements, the frontend should display a validation error before submitting to the backend.

## UI/UX Requirement for manualPath Field

- The input field for `manualPath` must include a round icon with a question mark ("?") next to it.
- When the user hovers over this icon, a tooltip must appear with the following guidance:

  > You can either paste the full documentation URL (it must start with  
  > <https://his.fcas-offen-2bf7313ba140d2ec7addb97cacba3e38-i000.eu-de.containers.appdomain.cloud/user-manual/documentation/data-transformation/>),  
  > or enter just the path part.  
  > Allowed characters: letters, numbers, hyphens, underscores, periods, slashes, and hash symbols.  
  > Example: `image-components/image-flipping/`

## Testing & Validation

- Unit test `manualPath` normalization, validation, and UI behavior.
- Integration test: admin sets `manualPath`, user sees and uses documentation link.
- Ensure the frontend prevents submission of invalid or unsafe paths.
- Coordinate with QA for regression/integration tests.

## Acceptance Criteria

- The admin UI provides clear, accessible guidance for entering the manual path.
- Only valid paths or URLs are accepted and stored.
- Users can access the manual via a button in the workflow editor.
- Invalid or unsafe URLs/paths are prevented from being stored or used.

## Notes

- The backend enforces relaxed validation: underscores allowed, trailing slashes auto-added, uppercase converted to lowercase, only critical format violations blocked.
- Only the path is stored in the database; the frontend always uses the hardcoded base URL for links.
