# Testing the manualPath Feature

## Overview

The `manualPath` feature allows component template administrators to specify a path to documentation in the user manual. This document outlines how to test the feature to ensure it's working correctly.

## Test Scenarios

### 1. Adding a Manual Path to a Component Template

1. Log in as an administrator with permissions to create/edit component templates
2. Navigate to the Component Templates administration page
3. Create a new component template or edit an existing one
4. In the "Link to User Manual" field, enter one of the following:
   - A direct path: `image-components/image-flipping/`
   - A full URL: `https://his.fcas-offen-2bf7313ba140d2ec7addb97cacba3e38-i000.eu-de.containers.appdomain.cloud/user-manual/documentation/data-transformation/image-components/image-flipping/`
5. Save the component template
6. Verify the component template detail page shows the manual link
7. Verify the tooltip shows the saved path value

**Expected Result**: The manual path should be saved and visible in the component template details. If you entered a full URL, only the path part should be stored.

### 2. Testing Validation

1. Try entering invalid paths containing characters not allowed by the validation regex:
   - Spaces: `path with spaces`
   - Special characters: `path<with>invalid&chars`
   - Other invalid characters: `path\\with\\backslashes`, `path;with;semicolons`, etc.
2. Submit the form

**Expected Result**: The form should show a validation error and not submit.

### 3. Testing Backend Processing

1. Enter a path with a leading slash: `/image-components/image-flipping/`
2. Save the component template
3. Check the detail view

**Expected Result**: The path should be normalized (leading slash removed) and you should see a notification indicating that the path was processed.

### 4. Viewing the Documentation Link

1. Open the workflow editor
2. Add a component that has a manual path configured
3. View the component details/properties
4. Click the "Open Manual Documentation" button

**Expected Result**: A new tab should open with the correct documentation URL (base URL + stored path).

## Debugging Tips

If the feature isn't working as expected:

1. Check the browser console for any JavaScript errors
2. Use browser developer tools to inspect the API requests:
   - Verify the `manualPath` field is included in the request to create/update the component template
   - Check the response from the API to see if there are any validation errors
3. Inspect the MongoDB database directly to verify the field is being stored:
   ```bash
   oc port-forward pod/mongodb-5fff96fd6b-d5m9g 27017:27017
   ```
   Then connect with MongoDB Compass or the mongosh CLI tool and look at the component templates collection.

## Common Issues

- **Field not saving**: Ensure both frontend and backend validation rules match
- **Link not working**: Verify the base URL is correctly concatenated with the stored path
- **Unexpected validation errors**: Check for leading/trailing whitespace in the input

## Reporting Issues

If you encounter bugs or unexpected behavior, please include:
1. The exact path you entered
2. Any validation error messages displayed
3. The API request/response (from browser developer tools)
4. The value stored in the database (if you can access it)
