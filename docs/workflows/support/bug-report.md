# Bug Report Workflow

## Rules

Bug report submission requires a signed-in user.

Title is required.

Description is required.

Steps to reproduce are optional.

Expected behavior is optional.

Client should include detected OS, browser, and browser version when available.

Bug report should create a GitHub issue when GitHub configuration is available.

## Client Side Validation

Client side validation occurs when Submit a Bug Report modal is open.

On client side validation:
1. If title is empty after trimming, keep Submit button disabled
2. If description is empty after trimming, keep Submit button disabled
3. Detect OS from browser user agent
4. Detect browser and browser version from browser user agent

## Server Side Validation

Server side validation must occur at the API level.

Bug report API must validate:
1. Caller is authenticated
2. Title is present
3. Description is present

API must add reporter information from JWT payload when creating the GitHub issue.

## Success Workflow

1. User opens account menu
2. User clicks Support
3. Client opens Submit a Bug Report modal
4. Client detects OS, browser, and browser version
5. User enters title
6. User enters description
7. User optionally enters steps to reproduce
8. User optionally enters expected behavior
9. User clicks Submit
10. Client calls bug report API
11. API validates JWT
12. API creates GitHub issue with bug label
13. API includes app version, environment, and reporter details in issue body
14. API returns success response
15. Client closes modal
16. Client displays success message

## Failure Workflow

1. If title or description is empty, keep Submit button disabled
2. If GitHub issue creation fails, display failed-to-submit message
3. If GitHub connection fails, display failed-to-connect message
4. If network fails, display unexpected-error message
5. Keep modal open when submission fails
