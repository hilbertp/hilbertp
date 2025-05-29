# Enhanced AI Development Guidelines for Microservice Architecture

This document provides comprehensive guidelines for using GitHub Copilot effectively in a microservice architecture environment, addressing common challenges and ensuring proportional, maintainable implementations.

## 1. Project Context Initialization

Before starting development with GitHub Copilot, establish a clear project context to help the AI understand your microservice architecture:

### 1.1 Architecture Overview Document

#### Project Architecture Overview

##### System Architecture

- **Overall Purpose**: Integrity-protocolling is a workflow-based data processing platform focused on data transformation with integrity verification capabilities
- **Architecture Style**: Microservice architecture with containerized deployments
- **Key Components**:
  - **Frontend**: Workflow editor interface (likely React/TypeScript)
  - **Backend Services**: Multiple microservices including data-transformation service
  - **Data Storage**: MongoDB as NoSQL document database
  - **Infrastructure**: Kubernetes for container orchestration
- **Documentation Approach**: C4 model for architecture documentation (System Context, Container, Component diagrams)

##### Service Boundaries

- **Component Template Service**:
  - Responsible for managing component templates used in workflows
  - Owns the ComponentTemplate entity and its persistence
  - Provides API for CRUD operations on templates
  - Manages validation of template properties including manualPath

- **Workflow Editor Frontend**:
  - Provides user interface for configuring workflows
  - Consumes component templates from backend
  - Responsible for displaying documentation links
  - Handles client-side validation of inputs

- **Data Transformation Service**:
  - Core service executing workflow operations
  - Processes data through configured component pipelines
  - Consumes component templates for execution

##### Data Flow Patterns

- **REST API Pattern**:
  - Standard REST endpoints for resources
  - Example endpoints:
    - `GET /api/component-templates/{id}` (retrieve)
    - `PUT /api/component-templates/{id}` (update)
    - `GET /api/component-templates` (list)

- **Data Transformation Chain**:
  1. Frontend UI → API Request (DTO)
  2. Controller → Service Layer
  3. Service Layer → Repository Layer
  4. Repository → MongoDB
  5. Reverse flow for retrievals

- **Authentication/Authorization**:
  - Role-based access controls appear to be implemented
  - it's a keycloak based implementation called uriel, managed by our partner in the consortium

##### Development Conventions

- **Documentation Standards**:
  - MADR (Markdown Architecture Decision Records) for architectural decisions
  - OpenAPI specifications for REST APIs
  - C4 model diagrams for service visualization

- **Testing Approach**:
  - Unit tests for individual components
  - Integration tests for service interactions
  - Container-based tests for database operations

- **Code Organization**:
  - Layer separation (Controller → Service → Repository)
  - DTO pattern for API data transfer
  - Entity objects for domain representation
  - Adapter/mapping between layers

- **Development Workflow**:
  - Feature branches
  - Peer reviews before merging
  - Deployment to dev → stage → prod
  - the scope of this AI-based development with be to make a feature branch, testable by a QA engineer on the dev namespace
    - it is very likely that the current configration of this file is completely correct: /Users/hilbertp/SSE/user-interface/.env.development

### 1.2 Service-Specific Context

For each service you're modifying, create a service context document:

#### Component Template Service Context

##### Service Purpose

- Manages the lifecycle of component templates used in workflow definitions
- Provides CRUD operations for component templates via REST API
- Validates and persists template properties, including documentation links (manualPath)
- Enables workflow components to be documented and referenced in the UI

##### Technical Stack

- **Language/Framework**: Java (Spring Boot)
- **Database**: MongoDB (NoSQL document database)
- **Schema Approach**: Document-based schema for component templates
- **Key Libraries**: Spring Data MongoDB, Spring Web
- **Knowledge Gap**: No direct evidence found for Lombok or MapStruct usage in the codebase as of this writing. If present, update this section accordingly.

##### Data Models

- **Core Entity**: `ComponentTemplate` (fields: id, name, description, manualPath, etc.)
- **DTOs**: `ComponentTemplateDTO` for API transfer, mapping to/from entity
- **Validation**: Regex pattern `^[a-zA-Z0-9-_./#]+$` for manualPath; Bean Validation (JSR-380) for other fields

##### API Contracts

- **REST Conventions**: Resource-oriented endpoints, JSON payloads
- **Endpoints**:
  - `GET /api/component-templates` (list all)
  - `GET /api/component-templates/{id}` (retrieve one)
  - `PUT /api/component-templates/{id}` (update)
- **Request/Response Patterns**: Standard Spring REST controller conventions, DTO-based
- **Error Handling**: Standard Spring exception handling, returns structured error responses (problem+json)

##### Testing Approach

- **Unit Testing**: Kotest for service and controller logic
- **Integration Testing**: Kotest with embedded MongoDB/Testcontainers
- **Build & Test Management**: Gradle for build and test orchestration
- **Test Data Management**: Test data loaded via test configuration or builder patterns; focus on minimal, targeted integration tests for persistence and API contract verification

#### Workflow Editor Frontend Context

##### Service Purpose

- Provides the user interface for configuring workflows and managing component templates
- Displays documentation links for each component template (using manualPath)
- Handles client-side validation and user feedback for manualPath input

##### Technical Stack

- **Language/Framework**: TypeScript, React
- **Build Tooling**: Vite, pnpm
- **Testing**: Playwright for end-to-end tests, Vitest for unit tests
- **Knowledge Gap**: No direct evidence found for Redux, MobX, or other state management libraries; update if found in codebase.

##### Data Models

- **ComponentTemplate**: TypeScript interface reflecting backend DTO (fields: id, name, description, manualPath, etc.)
- **Validation**: Regex pattern `^[a-zA-Z0-9-_./#]+$` for manualPath; additional frontend validation logic for full URL vs. path

##### API Contracts

- **REST Conventions**: Consumes backend REST API endpoints for component templates
- **Endpoints Used**:
  - `GET /api/component-templates` (list all)
  - `GET /api/component-templates/{id}` (retrieve one)
  - `PUT /api/component-templates/{id}` (update)
- **Request/Response Patterns**: JSON payloads matching backend DTOs
- **Error Handling**: Displays validation and API errors in the UI; no evidence of custom error boundary components

##### Testing Approach

- **Unit Testing**: Vitest for component and utility logic
- **Integration/E2E Testing**: Playwright for user flows and API integration
- **Test Data Management**: Mocked API responses and test fixtures for UI tests

#### Building Block Controller (BBC) Context

##### Service Purpose

- Manages the lifecycle of building blocks (deployable workflow components, typically as Helm charts)
- Provides REST API for creating, updating, listing, and validating building blocks
- Interprets workflow definitions and deploys them to Kubernetes using the fabric8 client
- Stores building block metadata and configuration in MongoDB

##### Technical Stack

- **Language/Framework**: Kotlin (Spring Boot)
- **Database**: MongoDB (NoSQL document database)
- **Kubernetes Integration**: fabric8 Kubernetes client
- **Other**: Uses Helm charts for building block deployment

##### Data Models

- **BuildingBlock**: releaseName, namespace, buildingBlockName, creationTimestamp, status, values (Map)
- **NewBuildingBlock**: Used for creation requests
- **K8sStatus**: Kubernetes status info for each building block

##### API Contracts

- **REST Conventions**: Resource-oriented endpoints, JSON payloads
- **Endpoints**:
  - `GET /v1/building-blocks` (list all)
  - `GET /v1/building-blocks/{name}` (get by name)
  - `PUT /v1/building-blocks/{name}` (create or replace)
  - `POST /v1/building-blocks` (bulk create/replace)
  - `POST /v1/building-blocks/{name}:validate` (dry-run validation)
- **Request/Response Patterns**: Standard Spring REST controller conventions, DTO-based
- **Error Handling**: Standard Spring exception handling

##### Testing Approach

- **Unit Testing**: (Not explicitly found, but likely JUnit/Kotest based on project conventions)
- **Integration Testing**: Not explicitly found, but likely uses Spring Boot Test and embedded MongoDB/Testcontainers
- **Test Data Management**: Not explicitly found; likely uses test fixtures or builder patterns

##### Knowledge Gaps

- No direct evidence of custom validation for manualPath in BBC
- No direct evidence of Lombok, MapStruct, or specific test frameworks in this service (update if found)
- No evidence that BBC directly handles the manualPath field (it may only manage building block deployment, not documentation links)

#### Data Transformation Service Context

##### Service Purpose

- Executes workflow operations using configured component templates
- Consumes component templates for data processing pipelines
- No direct involvement in manualPath feature, but relies on up-to-date template definitions

##### Technical Stack

- **Language/Framework**: Python (inferred from directory structure and file extensions)
- **Database**: Consumes data from MongoDB via backend APIs
- **Knowledge Gap**: No direct evidence of which Python web framework is used (Flask, FastAPI, etc.); update if found in codebase.

##### Data Models

- **ComponentTemplate**: Consumed as part of workflow definitions; structure mirrors backend DTO
- **Validation**: No direct validation of manualPath; relies on upstream services

##### API Contracts

- **REST Conventions**: Consumes backend REST API endpoints for workflow and template data
- **Endpoints Used**: Not directly involved in manualPath CRUD, but may consume template data via internal APIs
- **Error Handling**: Not directly relevant for manualPath feature

##### Testing Approach

- **Unit Testing**: Python test framework (pytest or unittest, not confirmed)
- **Integration Testing**: Not directly relevant for manualPath feature
- **Knowledge Gap**: No direct evidence of test framework or coverage for this service; update if found in codebase.

### 1.3 Feature-Specific Context

For each feature, create a focused context document:

```markdown
# Feature: [Feature Name]

## Scope Definition
- Precise description of what this feature DOES include
- Explicit list of what this feature DOES NOT include
- Expected impact on existing functionality

## Data Flow Map
- Specific entities and fields being modified
- Complete path of data from UI to database
- All transformation/validation points

## Minimal Viable Implementation
- Smallest set of files that must be modified
- Estimated lines of code change (target range)
- Explicit testing scope
```

## 2. Proportional Implementation Strategy

One of the key challenges identified in your project was disproportionate implementation complexity. Use these strategies to maintain proportionality:

### 2.1 Change Scope Boundaries

Before implementing any feature, explicitly define scope boundaries for GitHub Copilot:

#### Implementation Boundaries

##### In Scope (Required)
- Backend: 
  - ComponentTemplate schema/model (add `manualPath` field, persistence, validation)
  - OpenAPI specification and example generators (document and exemplify `manualPath`)
  - API endpoints: `GET/PUT /api/component-templates/{id}`, `GET /api/component-templates` (ensure `manualPath` is included)
  - Unit and integration tests for persistence, retrieval, and validation of `manualPath`
- Frontend:
  - Component template admin UI (add/edit input for `manualPath`, validation, tooltip)
  - Workflow editor UI (display documentation link button/icon, handle empty state)
  - Frontend validation logic for `manualPath`
  - Unit and integration tests for UI, validation, and link behavior
- Documentation:
  - Update API docs and markdown documentation to reflect new field and logic
  - Peer-review checklist updates for both frontend and backend

##### Potentially In Scope (Evaluate Need)
- Backend:
  - Data normalization logic (if input is a full URL, extract path)
  - Additional DTOs or mapping layers if required by architecture
- Frontend:
  - Refactoring of related UI components for maintainability
  - Additional user guidance or error handling improvements
- Testing:
  - Regression tests for related component template features
  - QA automation scripts for manualPath scenarios

##### Explicitly Out of Scope
- Any changes to unrelated microservices (e.g., Building Block Controller, notification-service, etc.)
- Changes to infrastructure, deployment, or CI/CD pipelines
- Modifications to unrelated frontend or backend modules
- Changes to the user manual content or its hosting
- Any authentication/authorization logic unrelated to manualPath
- Refactoring or redesign of the overall component template system

### 2.2 Incremental Implementation Approach

Instruct GitHub Copilot to follow this incremental pattern:

1. **Core Data Model Changes Only**: First implement only entity/DTO changes
2. **Validation Logic**: Add only essential validation
3. **Persistence Layer**: Implement minimal database changes
4. **API Layer**: Add endpoint support
5. **Unit Tests**: Add focused tests for new functionality
6. **Integration Tests**: Add only tests that verify the complete chain

For each step, review before proceeding to the next.

### 2.3 Explicit File Modification Limits

Set explicit limits on the scope of changes:

```markdown
# Implementation Constraints

- Maximum files to modify: [N] (typically 3-5 for simple features)
- Maximum new files to create: [N] (typically 2-3 for simple features)
- Maximum lines of code to add: ~[N] (proportional to feature complexity)
```

## 3. Layered Testing Strategy

The postmortem identified excessive and unfocused testing as a key issue. Use this layered approach:

### 3.1 Test Planning Template

Before writing any tests, create a test plan:

```markdown
# Test Plan for [Feature]

## Unit Tests
- [List specific classes/methods requiring unit tests]
- [List specific scenarios to test]

## Integration Tests
- [List specific integration points to test]
- [Define minimal test scope to verify feature]

## End-to-End Tests
- [List user scenarios to verify]
- [Define test data requirements]

## Infrastructure Requirements
- [List any infrastructure needed for testing]
- [Define minimal configuration changes required]
```

### 3.2 Test Implementation Sequence

Instruct GitHub Copilot to implement tests in this sequence:

1. **Unit Tests**: Test individual components in isolation
2. **Focused Integration Tests**: Test only the specific data flow path
3. **Minimal End-to-End Tests**: Test only the primary user scenario

### 3.3 Test Infrastructure Isolation

To prevent unnecessary infrastructure changes:

```markdown
# Test Infrastructure Guidelines

- Use mocks/stubs for external dependencies when possible
- Isolate test infrastructure changes from feature implementation
- Use the simplest container configuration that will verify functionality
- Avoid modifying Kubernetes configurations for feature testing
```

## 4. Cross-Layer Verification Strategy

The lessons learned document highlighted issues with data flow across layers. Use this verification approach:

### 4.1 Data Flow Verification Checklist

Create a verification checklist for each feature:

```markdown
# Data Flow Verification Checklist

## UI to API
- [ ] Field appears correctly in UI form
- [ ] Field is included in API request payload
- [ ] Field passes frontend validation

## API to Service
- [ ] Field is defined in API DTO
- [ ] Field is mapped correctly to domain entity
- [ ] Field passes backend validation

## Service to Database
- [ ] Field is defined in database schema
- [ ] Field is correctly persisted in database
- [ ] Field is correctly retrieved from database

## Database to UI
- [ ] Field is included in API response
- [ ] Field is correctly displayed in UI
```

### 4.2 Diagnostic Approach

Instruct GitHub Copilot to implement a systematic diagnostic approach:

```markdown
# Diagnostic Implementation

1. Add logging at each layer transition
2. Implement verification tests that check each transition point
3. Create a debug endpoint that shows the state of the entity at each layer
```

## 5. Infrastructure Configuration Management

To address the challenges with infrastructure configuration:

### 5.1 Infrastructure Change Isolation

```markdown
# Infrastructure Change Guidelines

- Keep infrastructure changes separate from feature implementation
- Document all infrastructure dependencies for the feature
- Use the simplest configuration that will support the feature
- Avoid modifying shared infrastructure for feature-specific needs
```

### 5.2 Configuration Verification

```markdown
# Configuration Verification Steps

1. Verify local development environment configuration
2. Test with minimal container configuration
3. Document exact infrastructure requirements for deployment
4. Create separate MRs for infrastructure changes
```

## 6. GitHub Copilot Interaction Patterns

These patterns will help you get the most effective results from GitHub Copilot:

### 6.1 Context Window Management

GitHub Copilot has a limited context window. Use these strategies:

```markdown
# Context Management for Copilot

- Focus on one layer at a time (e.g., data model, then service, then API)
- Provide explicit file references when asking for changes
- When implementing cross-cutting concerns, provide examples from each affected layer
```

### 6.2 Effective Prompting Patterns

Use these prompt patterns for better results:

#### Pattern 1: Layered Implementation Request

```
I need to implement a new field 'manualPath' for component templates.

First, let's focus ONLY on the data model changes:
1. Here's the current ComponentTemplate class: [paste code]
2. Here's the current ComponentTemplateDTO: [paste code]

Please modify ONLY these files to add the new field with appropriate validation.
```

#### Pattern 2: Targeted Test Request

```
I need to create focused integration tests for the 'manualPath' field persistence.

1. Here's the current test structure: [paste example test]
2. I need to verify ONLY that:
   - The field is correctly sent in the API request
   - The field is correctly stored in the database
   - The field is correctly returned in the API response

Please create a minimal integration test that verifies just this chain.
```

#### Pattern 3: Diagnostic Implementation Request

```
I need to diagnose where the 'manualPath' field is being lost in the persistence chain.

1. Here's the data flow: UI -> API -> Service -> Repository -> Database
2. Here are the relevant files:
   - [list files for each layer]

Please suggest logging statements I can add at each transition point to track the field value.
```

### 6.3 Review and Refinement Loop

Implement this review loop with GitHub Copilot:

1. Request initial implementation
2. Review for scope creep and unnecessary changes
3. If detected, provide explicit feedback: "This implementation is too complex. Please simplify to modify only X, Y, Z files."
4. Request focused refinements rather than complete rewrites

## 7. Implementation Workflow

Follow this workflow for each feature:

### 7.1 Pre-Implementation

1. Create architecture and service context documents
2. Define feature scope boundaries
3. Create data flow map
4. Define minimal viable implementation
5. Create test plan

### 7.2 Implementation Sequence

1. Implement core data model changes
2. Add validation logic
3. Implement persistence layer changes
4. Add API layer support
5. Implement unit tests
6. Implement focused integration tests

### 7.3 Verification

1. Verify each layer transition with logging
2. Run all tests
3. Manually verify the complete chain
4. Document any scope changes or deviations

### 7.4 Review Preparation

1. Document implementation decisions
2. Create focused peer review checklist
3. Highlight any areas of concern
4. Document lessons learned

## 8. Practical Examples

### Example 1: Adding a Simple Field (Like manualPath)

#### Minimal Implementation Scope

```markdown
# Implementation Scope for manualPath Field

## Required Changes
- ComponentTemplate entity class (add field)
- ComponentTemplateDTO class (add field)
- MongoDB schema mapping (ensure field is mapped)
- API validation logic (add validation for field)

## Required Tests
- Unit test for field validation
- Integration test for field persistence
- API test for field in request/response

## Explicitly Out of Scope
- Kubernetes configuration changes
- Test infrastructure modifications
- Unrelated service changes
```

#### Implementation Sequence

1. Add field to entity and DTO classes
2. Add validation logic
3. Verify MongoDB mapping
4. Add focused tests
5. Verify complete chain

### Example 2: Diagnosing Persistence Issues

```markdown
# Diagnostic Approach for Field Persistence

## Systematic Verification
1. Check API request payload (browser network tab or API client)
2. Add logging in controller to verify field in request
3. Add logging in service layer to verify field in DTO
4. Add logging in repository layer to verify field in entity
5. Query database directly to verify field in stored document
6. Add logging in service layer to verify field in retrieved entity
7. Add logging in controller to verify field in response

## Focused Fix
- Once the exact point of failure is identified, make minimal changes to just that layer
- Verify the fix with the same diagnostic approach
```

## 9. Retrospective and Continuous Improvement

After each feature implementation:

### 9.1 Implementation Metrics

Record and review:

- Number of files changed
- Lines of code added/removed
- Test coverage
- Time spent on implementation
- Time spent on debugging

### 9.2 Lessons Learned Template

```markdown
# Implementation Retrospective

## What Went Well
- [List aspects that were implemented efficiently]

## What Could Be Improved
- [List areas where the implementation was more complex than necessary]

## Copilot Effectiveness
- [Note where Copilot was particularly helpful]
- [Note where Copilot suggestions needed significant modification]

## Process Improvements
- [Suggest changes to the development process]
- [Identify new patterns for Copilot interaction]
```

### 9.3 Update Guidelines

Based on retrospectives, regularly update these guidelines to incorporate new lessons and patterns.

## 10. Conclusion

By following these enhanced guidelines, you can leverage GitHub Copilot effectively in your microservice architecture while maintaining proportionality, ensuring focused testing, and managing infrastructure complexity. The key principles are:

1. **Provide clear context** for the AI to understand your architecture
2. **Maintain proportionality** between feature complexity and implementation scope
3. **Use a layered testing approach** that focuses on the specific feature
4. **Implement systematic verification** across all layers
5. **Isolate infrastructure changes** from feature implementation
6. **Use effective interaction patterns** with GitHub Copilot
7. **Follow a structured workflow** for each feature
8. **Continuously improve** based on implementation metrics and lessons learned
