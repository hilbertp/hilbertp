# 01-Requirement Deviations

This document shows where development strayed from requirements or suggestions provided in the user stories:

## Validation & Regex

- The original user story specified a strict regex, disallowed underscores, required trailing slashes, and enforced a two-part path.
- **We are now relaxing these requirements for better usability:**
  - Underscores are allowed.
  - Trailing slashes are auto-added if missing.
  - Uppercase letters are converted to lowercase.
  - Missing second path part is allowed (user responsibility).
  - Full URLs are accepted, but only the path is stored.
  - Only critical format violations (disallowed characters, wrong anchoring) result in a red error message.
- **Reasoning:**  
  This approach improves user experience, reduces friction, and maintains security by enforcing the base URL and path extraction.

## Validation & Feedback Loop

- **Enhanced validation feedback system:**
  - Frontend performs basic validation for immediate user feedback
  - Backend remains the authority on validation and path processing
  - After submission, the frontend displays exactly what was saved in the database
  - The backend communicates back the processed/extracted path or validation errors
  - Full URL construction happens only at click time using the stored path
- **Reasoning:**  
  This creates a clearer feedback loop for users, ensures what's displayed matches what's stored, and centralizes URL construction logic. It also makes it transparent to users how their input was processed, while maintaining robust backend validation.
