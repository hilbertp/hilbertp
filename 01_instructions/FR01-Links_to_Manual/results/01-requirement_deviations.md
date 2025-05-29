# 01-Requirement Deviations

This document shows where development strayed from requirements or suggestions provided in the user stories:

## Validation & Regex

- The original user story specified a strict regex, disallowed underscores, required trailing slashes, and enforced a two-part path.
- **We are now adopting a unified, simplified approach:**
  - Using a single regex pattern across the application: `^[a-zA-Z0-9-_./#]+$`
  - Underscores are allowed for better usability
  - Basic normalization (trailing slashes, lowercase conversion) handled automatically 
  - Full URLs are accepted as input, but only the path component (after the base URL) is stored in MongoDB. This ensures no external or absolute URLs are persisted, only the relevant manual path.
  - Focus on preventing harmful characters rather than enforcing strict formatting
- **Reasoning:**  
  This approach improves user experience, reduces implementation complexity, and maintains security while ensuring consistency between frontend and backend validation.

## Validation Responsibility 

- **Simplified validation system:**
  - Frontend performs comprehensive validation for immediate user feedback
  - Backend implements minimal sanitization for security purposes
  - Using the same regex pattern in both places eliminates inconsistencies
  - Full URL construction happens only at click time using the stored path
- **Reasoning:**  
  This creates a clearer implementation path, reduces redundant code, and prevents validation discrepancies between frontend and backend while maintaining security and usability.

## Adapter Logic & Variable Naming

- **Deviation:**  
  Instead of directly using `dto.manualPath` in the entity mapping, a local variable (`normalizedManualPath`) is used after applying all validation and normalization logic.
- **Reasoning:**  
  This approach ensures that only sanitized and validated data is persisted, reduces the risk of accidental misuse of raw input, and makes the code easier to review and maintain.  
  It also makes the validation and normalization process explicit for peer reviewers.
- **Impact:**  
  No functional deviation from requirements, but improves code quality and reviewability.
