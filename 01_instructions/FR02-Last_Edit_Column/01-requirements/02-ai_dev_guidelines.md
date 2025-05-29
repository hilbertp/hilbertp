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

- **Pipeline Management Service**:
  - Responsible for managing data transformation pipelines
  - Owns the Pipeline entity and its persistence including lastEdit timestamp
  - Provides API for CRUD operations on pipelines
  - Maintains pipeline metadata including creation and modification timestamps

- **Workflow Editor Frontend**:
  - Provides user interface for listing and managing pipelines
  - Displays pipeline table with sortable and filterable columns
  - Consumes pipeline data from backend APIs
  - Handles client-side sorting, filtering, and date formatting

- **Building Block Controller (BBC)**:
  - May be involved in pipeline deployment and status updates
  - Potentially updates pipeline lastEdit timestamps when deployments occur
  - Stores pipeline metadata and configuration in MongoDB

##### Data Flow Patterns

- **REST API Pattern**:
  - Standard REST endpoints for resources
  - Example endpoints:
    - `GET /api/pipelines` (list all pipelines)
    - `GET /api/pipelines/{id}` (retrieve single pipeline)
    - `PUT /api/pipelines/{id}` (update pipeline, updates lastEdit)
    - `POST /api/pipelines` (create pipeline, sets lastEdit)

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

#### Pipeline Management Service Context

##### Service Purpose

- Manages the lifecycle of data transformation pipelines
- Provides CRUD operations for pipelines via REST API
- Maintains pipeline metadata including creation and modification timestamps
- Enables pipeline tracking and auditing through lastEdit timestamps

##### Technical Stack

- **Language/Framework**: Java/Kotlin (Spring Boot) - exact language TBD based on codebase exploration
- **Database**: MongoDB (NoSQL document database)
- **Schema Approach**: Document-based schema for pipelines
- **Key Libraries**: Spring Data MongoDB, Spring Web

##### Data Models

- **Core Entity**: `Pipeline` (fields: id, name, version, description, lastEdit, etc.)
- **DTOs**: `PipelineDTO` for API transfer, mapping to/from entity
- **Validation**: ISO 8601 datetime format for lastEdit; standard validation for other fields

##### API Contracts

- **REST Conventions**: Resource-oriented endpoints, JSON payloads
- **Endpoints**:
  - `GET /api/pipelines` (list all pipelines with lastEdit)
  - `GET /api/pipelines/{id}` (retrieve single pipeline)
  - `PUT /api/pipelines/{id}` (update pipeline, automatically updates lastEdit)
  - `POST /api/pipelines` (create pipeline, sets initial lastEdit)
- **Request/Response Patterns**: Standard Spring REST controller conventions, DTO-based
- **Error Handling**: Standard Spring exception handling, returns structured error responses

##### Testing Approach

- **Unit Testing**: Framework TBD based on codebase (likely JUnit/Kotest)
- **Integration Testing**: Embedded MongoDB/Testcontainers for persistence testing
- **Build & Test Management**: Gradle or Maven based on project structure
- **Test Data Management**: Focus on minimal tests for lastEdit timestamp behavior

#### Workflow Editor Frontend Context

##### Service Purpose

- Provides the user interface for listing and managing data transformation pipelines
- Displays pipeline table with sortable and filterable columns including "Last Edit"
- Handles client-side date formatting, sorting, and filtering
- Provides consistent UI/UX for table interactions

##### Technical Stack

- **Language/Framework**: TypeScript, React
- **Build Tooling**: Vite, pnpm
- **Testing**: Playwright for end-to-end tests, Vitest for unit tests
- **Knowledge Gap**: State management library TBD based on codebase exploration

##### Data Models

- **Pipeline**: TypeScript interface reflecting backend DTO (fields: id, name, version, description, lastEdit, etc.)
- **Validation**: ISO 8601 datetime format handling for lastEdit display and filtering

##### API Contracts

- **REST Conventions**: Consumes backend REST API endpoints for pipelines
- **Endpoints Used**:
  - `GET /api/pipelines` (list all pipelines)
  - `GET /api/pipelines/{id}` (retrieve single pipeline)
- **Request/Response Patterns**: JSON payloads matching backend DTOs
- **Error Handling**: Standard error display patterns for API failures

##### Testing Approach

- **Unit Testing**: Vitest for component logic, table sorting/filtering
- **Integration/E2E Testing**: Playwright for pipeline table interactions
- **Test Data Management**: Mocked API responses with various lastEdit timestamps

#### Building Block Controller (BBC) Context

##### Service Purpose

- Manages the lifecycle of building blocks (deployable workflow components)
- May update pipeline metadata when deployments occur
- Potentially involved in maintaining lastEdit timestamps for pipeline changes
- Stores pipeline deployment metadata in MongoDB

##### Technical Stack

- **Language/Framework**: Kotlin (Spring Boot)
- **Database**: MongoDB (NoSQL document database)
- **Kubernetes Integration**: fabric8 Kubernetes client
- **Other**: Uses Helm charts for building block deployment

##### Data Models

- **Pipeline**: May contain pipeline metadata including lastEdit timestamps
- **BuildingBlock**: Deployment-related data that may trigger pipeline updates

##### API Contracts

- **REST Conventions**: Resource-oriented endpoints, JSON payloads
- **Endpoints**: TBD based on pipeline update requirements
- **Request/Response Patterns**: Standard Spring REST controller conventions
- **Error Handling**: Standard Spring exception handling

##### Testing Approach

- **Unit Testing**: Framework TBD based on project conventions
- **Integration Testing**: May require testing pipeline timestamp updates
- **Test Data Management**: Focus on pipeline lastEdit behavior during deployments

##### Knowledge Gaps

- Exact involvement in pipeline lastEdit updates needs verification
- Specific endpoints for pipeline operations need exploration

### 1.3 Feature-Specific Context

For each feature, create a focused context document:

```markdown
# Feature: Add "Last Edit" Column to Pipeline Table

## Scope Definition

- **DOES include**:
  - Adding a fifth column titled "Last Edit" to the existing pipeline table
  - Displaying last modified datetime in ISO 8601 format
  - Implementing sorting (ascending/descending) for the Last Edit column
  - Implementing filtering (date/datetime range) for the Last Edit column
  - Ensuring UI/UX consistency with existing columns

- **DOES NOT include**:
  - Changes to other pipeline properties or functionality
  - Modifications to pipeline creation/deletion workflows
  - Changes to pipeline execution logic
  - Modifications to other unrelated tables or UI components

- **Expected impact**: Minimal impact on existing functionality; purely additive feature

## Data Flow Map

- **Entities**: Pipeline entity with lastEdit timestamp field
- **Data path**: Backend API → Frontend table component → User display
- **Transformation points**: 
  - Backend: Ensure lastEdit is populated and returned in API responses
  - Frontend: Format ISO 8601 datetime for display, handle sorting/filtering

## Minimal Viable Implementation

- **Files to modify**: 3-5 files maximum (pipeline table component, types, tests)
- **Lines of code**: 30-50 lines estimated
- **Testing scope**: Table rendering, sorting, filtering of Last Edit column
```

## 2. Proportional Implementation Strategy

One of the key challenges identified in your project was disproportionate implementation complexity. Use these strategies to maintain proportionality:

### 2.1 Change Scope Boundaries

Before implementing any feature, explicitly define scope boundaries for GitHub Copilot:

#### Implementation Boundaries

##### In Scope (Required)

- **Backend**: 
  - Pipeline entity schema (ensure `lastEdit` field exists and is populated)
  - API endpoints: `GET /api/pipelines` (ensure `lastEdit` is included in response)
  - Automatic timestamp updates when pipelines are modified
  - Unit tests for lastEdit timestamp behavior

- **Frontend**:
  - Pipeline table component (add "Last Edit" column)
  - Date formatting utilities for ISO 8601 display
  - Sorting logic for datetime column
  - Filtering logic for date/datetime range
  - Unit and integration tests for table functionality

- **Documentation**:
  - Update API documentation to include lastEdit field
  - Update user documentation for new column functionality

##### Potentially In Scope (Evaluate Need)

- **Backend**:
  - Database migration if lastEdit field doesn't exist
  - Backfilling lastEdit for existing pipelines without timestamps

- **Frontend**:
  - Enhanced date picker components for filtering
  - Improved responsive design for additional column

- **Testing**:
  - E2E tests for complete table interaction workflows
  - Performance tests for large pipeline lists with sorting/filtering

##### Explicitly Out of Scope

- Any changes to pipeline creation, execution, or deployment logic
- Modifications to other tables or UI components not related to pipeline listing
- Changes to authentication/authorization logic
- Infrastructure or deployment pipeline changes
- Refactoring of unrelated pipeline management features

### 2.2 Incremental Implementation Approach

Instruct GitHub Copilot to follow this incremental pattern:

1. **Backend Data Model Verification**: Confirm Pipeline entity includes `lastEdit` field
2. **API Response Verification**: Ensure `GET /api/pipelines` returns `lastEdit` in response
3. **Frontend Table Column**: Add "Last Edit" column to pipeline table component
4. **Date Formatting**: Implement ISO 8601 datetime display formatting
5. **Sorting Logic**: Add sorting capability for the Last Edit column
6. **Filtering Logic**: Add date range filtering for the Last Edit column
7. **Unit Tests**: Add focused tests for new table functionality
8. **Integration Tests**: Verify complete sorting and filtering workflows

For each step, review before proceeding to the next.

### 2.3 Explicit File Modification Limits

Set explicit limits on the scope of changes:

```markdown
# Implementation Constraints for "Last Edit" Column Feature

- Maximum files to modify: 5 (pipeline table component, types/interfaces, date utilities, tests)
- Maximum new files to create: 2 (date utility helpers, additional test files if needed)
- Maximum lines of code to add: ~50 (proportional to adding a single table column)
```

## 3. Frontend-Specific Implementation Guidelines

Since this feature is primarily frontend-focused, add specific guidelines for table component modifications:

### 3.1 Table Component Best Practices

```markdown
# Table Column Addition Guidelines

## Column Integration
- Follow existing column patterns for consistency
- Ensure responsive design accommodates the new column
- Use existing styling and theme variables

## Sorting Implementation  
- Leverage existing sorting infrastructure
- Handle datetime comparison correctly
- Maintain sort state consistency with other columns

## Filtering Implementation
- Use existing filter component patterns
- Implement date range picker consistent with UI design
- Handle edge cases (null dates, invalid ranges)

## Performance Considerations
- Avoid re-rendering entire table when sorting/filtering
- Use memo/optimization for date formatting if needed
- Consider virtual scrolling for large datasets
```

### 3.2 Testing Strategy for Frontend

```markdown
# Frontend Testing Approach

## Unit Tests
- Test column rendering with various date formats
- Test sorting logic with different date scenarios
- Test filtering logic with date ranges

## Integration Tests  
- Test complete table interaction workflows
- Test API integration with lastEdit field
- Test responsive behavior with new column

## E2E Tests (if needed)
- Test user journey: view table → sort by Last Edit → filter by date range
```

## 4. Backend Integration Guidelines

### 4.1 API Contract Verification

Before frontend implementation, verify the backend provides:

```markdown
# Backend API Requirements

## Pipeline API Response Format
```json
{
  "id": "pipeline-123",
  "name": "Data Processing Pipeline",
  "version": "1.2.0", 
  "description": "Processes customer data",
  "lastEdit": "2025-05-29T10:30:00Z"
}
```

## Required Behavior
- lastEdit field populated on all pipeline responses
- ISO 8601 format (UTC timezone recommended)
- Automatic updates when pipeline is modified
- Consistent field naming across all endpoints
```

## 5. Infrastructure Configuration Management

Since this is primarily a frontend feature, infrastructure changes should be minimal:

### 5.1 Infrastructure Change Isolation

```markdown
# Infrastructure Change Guidelines for Last Edit Column

- No infrastructure changes should be required for this feature
- Verify that existing API infrastructure already supports the lastEdit field
- Use existing development environment configuration (.env.development)
- Avoid any deployment or database migration changes unless absolutely necessary
```

## 6. Verification and Testing Protocol

### 6.1 Development Verification Steps

```markdown
# Manual Testing Checklist

## Frontend Verification (localhost:3000)
1. Start frontend: `pnpm start` (ensure port 3000)
2. Navigate to pipeline list/table view
3. Verify "Last Edit" column appears as 5th column
4. Test sorting: click column header, verify ascending/descending
5. Test filtering: use date range filter, verify results
6. Verify responsive design with new column
7. Check date format displays as ISO 8601

## API Verification  
1. Check `GET /api/pipelines` response includes lastEdit field
2. Verify lastEdit format is ISO 8601 datetime
3. Test that pipeline updates modify lastEdit timestamp

## Cross-browser Testing
1. Test in Chrome, Firefox, Safari (macOS)
2. Verify table functionality consistent across browsers
```

### 6.2 Automated Testing Requirements

```markdown
# Test Coverage Requirements

## Minimum Test Coverage
- Unit tests for table column rendering
- Unit tests for date sorting logic  
- Unit tests for date filtering logic
- Integration test for API data display

## Test Data Requirements
- Pipelines with various lastEdit timestamps
- Edge cases: null dates, very old dates, future dates
- Large dataset for performance validation
```
