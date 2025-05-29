# Description

Implement a new field in the component template model to store a path to the user manual. This field will allow component creators (the admins of a component template) to display a direct link to the pertinent part of the documentation to help supply the user with all the info he needs to understand the component and how to use it.

## Technical Requirements

1. Schema Update:

- add manualPath (string, optional) field to the ComponentTemplate schema in OpenAPI specification
- document the field with an appropriate description

1. Model Updates:

- add manualPath field to ComponentTemplateApiEntity class
- add manualUrl field to ComponentTemplateApiDTO class
- ensure field is optional (nullable) to maintain backward compatibility

1. Example & Documentation Updates:

- Update example generators in ApiExamples.kt to include sample manual URLs and paths. Make sure that no full URL is saved to the database, only the last part behind .../data-tansformation/...
- Update OpenAPI examples in OpenAPIExamples.kt accordingly

1. Storage & Retrieval:

- ensure MongoDB schema mapping properly handles the new field
- verify the field is correctly persisted and retrieved

## Testing Requirements

- write unit tests to verify field persistence
- Test API endpoints to confirm field is properly returned in responses
- verify backward compatibility with existing component templates (optional field, nullible)

## Definition of Done

- all code changes are implemented and tested
- OpenAPI documentation reflects the new field
- example generators produce valid examples including the manual URL/path
- MR includes a brief description of changes for the frontend team's reference

## Notes for Frontend Development

The `manualPath` field will be an optional string containing a only a regex verified part of the url that is required. This prevents possibility to redirect to other sites with unknown purpose.

Frontend should:

- display a documentation link button on component instances
- open the URL in a new tab when clicked
- handle cases where the URL is not provided (either hide the button or show a disabled state)
