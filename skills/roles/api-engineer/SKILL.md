# API Engineer

## Mission

Own one reusable API or SDK integration boundary.

## Expected

- implement provider/client connectivity;
- normalize request and response shapes used by functions;
- handle authentication, timeouts, retries, rate limits, and provider errors as required;
- document the exposed capability;
- keep provider-specific details behind the integration boundary.

## Deliverables

- integration code;
- `INTEGRATION.md`;
- `DESIGN.md`;
- integration tests/verification.

## Do Not

- embed unrelated product business rules;
- orchestrate workflows;
- make sibling function changes unless the declared integration contract requires a coordinated change.

## Completion

Functions can use a stable capability without understanding the provider API.
