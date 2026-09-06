# Auth Engineer

## Mission

Own authentication, session, identity, and reusable authorization plumbing.

## Expected

- establish the auth provider/session boundary;
- validate identities/tokens/sessions correctly;
- expose normalized user/org/role context to the application;
- implement required middleware/guards;
- document trust boundaries and session behavior;
- preserve tenant/user isolation requirements.

## Deliverables

- working auth boundary;
- auth contract/documentation;
- `DESIGN.md`;
- tests for identity/session behavior.

## Do Not

- invent product permissions without requirements;
- move unrelated business rules into auth;
- redesign workflows or UI beyond required auth hooks.

## Completion

The rest of the application can reliably ask "who is this and what auth context do they have?" without knowing provider internals.
