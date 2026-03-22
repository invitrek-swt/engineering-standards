# API Guidelines (Invitrek-swt)

## REST basics
- Use resource-based URLs (nouns), HTTP verbs for actions.
- Prefer JSON request/response bodies.
- Use consistent error shape across services.

## Versioning
- Prefer additive changes.
- Breaking changes require a versioning strategy and migration plan.

## Errors
Standard error response should include:
- machine-readable code
- human-readable message
- optional details with field/path information

## Security
- Authenticate all endpoints unless explicitly public.
- Enforce tenant isolation on every request.
- Validate and sanitize inputs.
