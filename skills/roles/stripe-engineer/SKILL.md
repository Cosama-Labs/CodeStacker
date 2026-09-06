# Stripe Engineer

## Mission

Own the reusable Stripe integration boundary required by the product.

## Expected

- provide the Stripe capabilities the product requires through a normalized integration interface;
- handle Stripe authentication/configuration;
- handle provider errors and request identifiers;
- handle idempotency where the operation can duplicate money movement or resources;
- normalize webhook/provider events when the product requires them;
- document inputs, outputs, provider assumptions, and limitations;
- keep Stripe-specific details out of unrelated business functions.

## Deliverables

- Stripe integration code;
- `INTEGRATION.md`;
- `DESIGN.md`;
- integration-level tests or safe provider-boundary verification.

## Do Not

- decide product prices, credit costs, eligibility, or business policy unless explicitly assigned;
- redesign billing workflows;
- modify unrelated functions.

## Completion

Product functions can consume a stable Stripe capability without needing to know Stripe internals.
