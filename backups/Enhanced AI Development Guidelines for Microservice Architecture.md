# Enhanced AI Development Guidelines for Microservice Architecture

This document provides comprehensive guidelines for using GitHub Copilot effectively in a microservice architecture environment, addressing common challenges and ensuring proportional, maintainable implementations.

## 1. Project Context Initialization

Before starting development with GitHub Copilot, establish a clear project context to help the AI understand your microservice architecture:

### 1.1 Architecture Overview Document

Create a concise architecture overview document (500-800 words) that includes:

#### Project Architecture Overview

##### System Architecture
- Brief description of overall system purpose
- Diagram or text description of microservice relationships
- Key technologies used across services (languages, frameworks, databases)

##### Service Boundaries
- Clear definition of each microservice's responsibility
- Primary data entities owned by each service
- Cross-service dependencies and communication patterns

##### Data Flow Patterns
- How data typically flows between services
- Authentication/authorization mechanisms
- Common API patterns and conventions

##### Development Conventions
- Naming conventions for key components
- Testing strategy overview
- Configuration management approach
```

### 1.2 Service-Specific Context

For each service you're modifying, create a service context document:

#### [Service Name] Context

##### Service Purpose
- Primary responsibility of this service
- Key business capabilities provided

##### Technical Stack
- Language and framework versions
- Database technology and schema approach
- Key libraries and dependencies

##### Data Models
- Core entity definitions
- DTO structure patterns
- Validation approaches

##### API Contracts
- REST/GraphQL conventions
- Request/response patterns
- Error handling standards

##### Testing Approach
- Unit testing framework and patterns
- Integration testing approach
- Test data management
```

### 1.3 Feature-Specific Context

For each feature, create a focused context document:


#### Feature: [Feature Name]

##### Scope Definition
- Precise description of what this feature DOES include
- Explicit list of what this feature DOES NOT include
- Expected impact on existing functionality

##### Data Flow Map
- Specific entities and fields being modified
- Complete path of data from UI to database
- All transformation/validation points

##### Minimal Viable Implementation
- Smallest set of files that must be modified
- Estimated lines of code change (target range)
- Explicit testing scope
```

## 2. Proportional Implementation Strategy

One of the key challenges identified in your project was disproportionate implementation complexity. Use these strategies to maintain proportionality:

### 2.1 Change Scope Boundaries

Before implementing any feature, explicitly define scope boundaries for GitHub Copilot:

```markdown
# Implementation Boundaries

## In Scope (Required)
- [List specific files/components that MUST be modified]
- [List specific tests that MUST be written]

## Potentially In Scope (Evaluate Need)
- [List files/components that MIGHT need changes]
- [List tests that MIGHT be needed]

## Explicitly Out of Scope
- [List areas that should NOT be modified]
- [List infrastructure that should remain unchanged]
```

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
