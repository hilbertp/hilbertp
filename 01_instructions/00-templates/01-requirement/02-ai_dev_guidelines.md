# AI Development Guidelines Template for Microservice Architecture

This document provides guidelines for using AI coding assistants (e.g., GitHub Copilot) in a microservice architecture. Adapt the sections below to your project's specifics.

## 1. Project Context Initialization

Before starting, establish a clear project context for the AI:

### 1.1 Architecture Overview Document
- **Overall Purpose**: <Describe the main goal of the system>
- **Architecture Style**: <e.g., Microservice, Monolith, etc.>
- **Key Components**: <List main frontend, backend, data storage, infrastructure>
- **Documentation Approach**: <e.g., C4 model, ADRs, OpenAPI>

### 1.2 Service Boundaries
- <Service 1>: <Responsibilities, owned entities, APIs>
- <Service 2>: <Responsibilities, owned entities, APIs>

### 1.3 Data Flow Patterns
- <Describe main data/API flows, e.g., REST, event-driven, etc.>

### 1.4 Development Conventions
- <Documentation, testing, code organization, workflow standards>

## 2. Proportional Implementation Strategy

- Define explicit scope boundaries for each feature.
- Use incremental, reviewable steps (e.g., data model → API → frontend → tests).
- Set limits on files/lines to change for each feature.

## 3. Frontend/Backend Implementation Guidelines

### 3.1 Frontend
- Follow existing UI/UX and component patterns.
- Use project-specific date formatting, sorting, and filtering logic.
- Add/modify columns or features incrementally.
- Write unit/integration tests for new UI features.

### 3.2 Backend
- Ensure API contracts are clear and documented.
- Use DTOs and entity mapping as per project standards.
- Write unit/integration tests for new endpoints or fields.

## 4. Verification and Testing Protocol

- Manual testing checklist for new features (adapt to your dev environment).
- Automated test coverage requirements (unit, integration, E2E as needed).
- Test data requirements (edge cases, large datasets, etc.).

## 5. Infrastructure and Configuration

- Minimize infrastructure changes for feature work.
- Use existing environment configs unless change is required.
- Document any required infra changes clearly.

---

> Adapt each section to your project's architecture, stack, and workflow. Use this template to onboard AI coding assistants and ensure maintainable, proportional feature delivery.
