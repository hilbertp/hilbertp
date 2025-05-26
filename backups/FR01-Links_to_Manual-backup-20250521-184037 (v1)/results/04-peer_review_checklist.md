# 04-Peer Review Checklist

1. Check that only the path component is stored, never a full URL.
2. Verify that normalization is implemented: underscores are allowed, uppercase is converted to lowercase, and trailing slashes are auto-added.
3. Ensure regex validation is enforced at all entry points, but only for critical violations (disallowed characters, wrong anchoring).
4. Confirm that missing second path part is allowed (user responsibility).
5. Ensure OpenAPI docs and examples are up to date and match the actual implementation.
6. Review tests for coverage of edge cases (valid/invalid paths, missing field, normalization).
7. Confirm that the field is optional and backward compatible.
8. Look for clear inline documentation and commit messages.
9. Check that the frontend can reliably construct the full URL from the base + path.
