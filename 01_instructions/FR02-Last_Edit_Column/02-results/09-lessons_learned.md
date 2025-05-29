# 03-Lessons Learned

## Technical Challenges and Insights

- **Build Cache and CI/CD:**
  - The build cache in the pipeline can speed up builds, but if it becomes outdated or isn’t properly invalidated, it can cause hard-to-diagnose errors. Clearing the cache and doing a clean build solved the problem, but it cost a lot of time. In the future, the cache should be invalidated more deliberately, especially after schema or DTO changes.

- **Docker Dependency:**
  - Integration tests using Testcontainers require Docker to be running locally. If Docker isn’t active, tests will fail—sometimes without a clear error message. The development environment should check for this early and communicate it clearly.

- **Keeping Unit Tests Up to Date:**
  - After changes to DTOs, MongoDB schemas, or validation logic, all relevant unit and integration tests must be updated and pass. Otherwise, hidden errors can go unnoticed until late in the process.

- **DTO/MongoDB Schema Synchronization:**
  - There were inconsistencies between DTOs, database schemas, and their usage in code. Changes weren’t always applied everywhere, leading to build and runtime errors. Automated checks or better documentation of affected areas would help.

- **Database Schema vs UI Implementation:**
  - Even when backend schema changes are correct (e.g., adding `manualPath` field to component templates), verifying that the data is actually being persisted requires examining the database directly. The field might be defined in the schema but not populated by the frontend, or not correctly sent to/from the API.
  - The entire chain needs verification: UI form fields → frontend data model → API request → backend DTO → database schema → database document.

- **Full-Stack Feature Verification Gap:**
  - When implementing full-stack features like the `manualPath` field for component templates, there can be gaps between what's supported in the code and what's actually used. Our investigation showed that:
    - The backend models and MongoDB schema correctly defined the `manualPath` field
    - The frontend TypeScript types and UI components included the field definition
    - The database didn't contain the field for existing component templates
    - Without debugging the API requests/responses and database operations, it was difficult to pinpoint where the disconnect occurred
  - This highlights the need for thorough end-to-end testing, especially for features that span multiple layers of the application.

  - **Subtle Implementation Details in Validation Logic:**
    - We found a `validateAndExtractPath` function in the `ComponentTemplateApiDTOAdapter` class that processes the `manualPath` input before storing it. This function:
      - Extracts just the path part if a full URL is provided
      - Validates that the path only contains URL-safe characters
      - Enforces a 200-character length limit
      - Could potentially be rejecting valid inputs or silently modifying them in unexpected ways
    - The frontend and backend have different validation for the same field:
      - Frontend validates with more strict regexes that require paths to start with `/`
      - Backend validates with a more general regex that allows any URL-safe characters
    - We improved this implementation by:
      - Making both frontend and backend validation consistent (using the same URL-safe character pattern)
      - Adding proper error handling to display validation failures clearly to users
      - Enhancing the UI to show the exact path that was stored in the database
      - Providing helpful tooltips to guide users on the expected format
      - Adding feedback when the backend normalizes or processes the path differently than submitted
    - This improved user experience by creating a more predictable workflow and making the system's behavior transparent to users.

- **Feedback Loop for Path Processing:**
  - A key insight was the need to show users exactly what the system did with their input:
    - When the backend normalizes a path (e.g., removing a leading slash), users should be informed
    - The UI should display the actual stored value, not attempt to reconstruct it on the frontend
    - Error messages should be specific and actionable
  - This "feedback loop" approach enhances user trust in the system and reduces confusion about what's actually stored

- **Feature Implementation Traceability and Documentation:**
  - For the manual documentation link feature (FR01-Links_to_Manual), a detailed, chronologically structured Erfahrungsbericht was created and linked in the project documentation. This report provides full traceability of technical decisions, process steps, and validation outcomes.
  - Updating related documentation files (DoD, lessons learned, peer review checklist, etc.) ensures that all technical and process learnings are captured and accessible for future reference.
  - This approach improves transparency, supports onboarding, and helps prevent similar issues in future features.

## Analysis: Why Were Issues Missed Despite Clear Requirements?

- **Automated Code Generation (Copilot/VSC):**
  - Using GitHub Copilot and VS Code sped up development, but sometimes details (like all DTO references or test cases) weren’t fully updated. AI tools don’t always catch every cross-reference in the code.

- **Complexity of Distributed Changes:**
  - Changes to central data structures (like DTOs) affect many places in the code. Without a complete overview or automated refactoring tools, it's easy to miss some usages.

- **Missing End-to-End Checks:**
  - The requirements were clear, but there was no final end-to-end test (including build, test, and pipeline) to ensure all parts worked together.

- **Implementation Proportionality:**
  - Small features should have small implementations. Our experience showed that adding a single field (`manualPath`) ballooned to 13 files modified with ~367 lines added, which is disproportionate.
  - Such oversized implementations make peer reviews difficult and increase the chance of introducing bugs.
  - Focused implementation would have required changes to just 3-4 files with perhaps 20-30 lines total.

- **Feature Development Scope Creep:**
  - When implementing a simple feature (like adding a single database field), we often create excessive test coverage and start refactoring related code, leading to scope creep.
  - This can turn a 1-day feature into a multi-day project, with much higher risk of issues.
  - For our `manualPath` field implementation, we ended up modifying Kubernetes test configurations and creating multiple integration tests, none of which were actually needed.

- **Human Oversight Remains Essential:**
  - Despite AI support, manual review and systematic test runs are essential to catch errors that automated tools might miss.
  
- **Tooling and Environment Pitfalls:**
  - Local tooling issues (e.g., missing Helm, wrong working directory, or not being logged in to OpenShift) can block deployment even when code and configuration are correct. Always verify your environment before troubleshooting code or configuration.
  
- **Deployment Process Clarity:**
  - Ensure you know the correct deployment workflow for your environment (e.g., which branch to merge to, which namespace to use, and how to authenticate to the cluster) to avoid confusion and wasted effort.

- **Multi-Layer Verification Requirements:**
  - When implementing features across multiple layers (UI, API, database), verifying only one layer (e.g., UI changes or backend schemas) isn’t sufficient. The feature needs verification at each transition point, including direct database inspection when necessary.

## Recommendations for the Future

- After schema/DTO changes, always search for all usages (using IDE search or refactoring tools).
- Regularly review and, if needed, clear the build cache strategy.
- Highlight Docker dependencies and Testcontainer setups in developer documentation.
- Make end-to-end tests and full pipeline runs mandatory before merging or releasing.
- Use AI tools as support, but not as a replacement for human diligence and review.
- Implement a comprehensive testing approach that includes:
  - Unit tests for individual components
  - Integration tests for interacting components
  - API tests for correct data transfer
  - Database inspection for schema compliance and data persistence
  - UI tests for proper display and interaction
  - Always verify the full chain from user input to data storage and back

## Practical Tips

- **MongoDB Password Location:**
    - If you need to access the MongoDB database directly, the password is securely stored as a Kubernetes/OpenShift secret. You can find the relevant secret by inspecting the deployment YAML under Workloads > Secrets in the OpenShift web console. Look for the environment variable referencing the secret. Never store or document the actual password in the repository.

- **MongoDB Direct Inspection:**
    - For features involving database schema changes, always verify through direct MongoDB inspection that the data is being correctly persisted. Use port-forwarding to access databases in Kubernetes environments:

      ```bash
      oc port-forward pod/mongodb-5fff96fd6b-d5m9g 27017:27017
      ```

      Then connect with MongoDB Compass or the mongosh CLI tool to examine the actual documents.

- **Debugging UI-to-Database Data Flow:**
    - When implementing features like the `manualPath` field that span multiple layers (UI → API → database), use a systematic approach to identify where the data might be lost:
      1. Use browser developer tools to inspect network requests and verify the field is included in the API payload
      2. Use API debugging tools (e.g., Postman) to test the API directly, bypassing the UI
      3. Add temporary logging in the backend services to trace the data flow
      4. Examine the DTO conversion layers where data might be dropped
      5. Check for partial updates or missing fields in the database repository implementation
      6. Verify the MongoDB schema and indexes support the new field
    - In our case, debugging the `manualPath` field should focus on:
      - Confirming the field is included in the UI form submission
      - Tracing the field through the API request handling in the backend
      - Checking if the MongoDB repository implementation is correctly handling the field

## Additional Lessons from FR01-Links_to_Manual (May 2025)

- **End-to-End Consistency:**
  - Ensuring that validation and normalization logic is consistent between frontend and backend is critical. Discrepancies can lead to confusing user experiences and data integrity issues.
  - UI/UX improvements (such as clear tooltips, error messages, and feedback on stored values) significantly enhance user trust and reduce support overhead.

- **Documentation-First Approach:**
  - Drafting the Erfahrungsbericht in parallel with development helped identify missing steps, validation gaps, and process improvements early.
  - Comprehensive documentation of the process, including technical, process, and validation learnings, proved invaluable for both peer review and future maintenance.

- **CI/CD and Testing:**
  - Running `pnpm lint` and `pnpm test` locally before pushing changes, and ensuring the pipeline passes, is essential for maintaining code quality and avoiding regressions.
  - Explicitly updating test files to use the correct imports and removing outdated directives (e.g., triple-slash references) prevents subtle test runner issues.

- **Process Validation and Documentation Traceability:**
  - After completing the feature, all related documentation files (e.g., DoD, lessons learned, peer review checklist) were reviewed and updated to reflect the full scope of the work and learnings.
  - The detailed Erfahrungsbericht was cross-referenced in these files to maximize traceability and value for the team.
  - This practice ensures that technical, process, and validation insights are institutionalized and accessible for future development cycles.
