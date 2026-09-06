# The Code Stacker AI Engineering Team

Code Stacker treats each fresh AI coding session as a **specialized engineer with a bounded assignment**.

This is not role-play for its own sake. The role is a scope contract.

A founder or pure vibe coder should be able to say:

> "You are the Pipeline Engineer for this workflow."

or:

> "You are the Stripe Engineer for this project."

and the role definition tells the AI what it is responsible for, what it must deliver, what it must not take over, and what handoff it must leave behind.

The role files do **not** teach Stripe, OAuth, PostgreSQL, MCP, queues, React, or any other technology. The coding model is expected to know or research the implementation details.

The role files define **what good completion looks like**.

## Why specialized engineers

One long AI coding session is often asked to do too many jobs at once:

- understand product intent;
- choose architecture;
- build database schema;
- integrate providers;
- write business logic;
- wire queues;
- build UI;
- test everything;
- deploy it;
- decide whether it is done.

Code Stacker splits those responsibilities the same way a software team would.

```text
Founder / Product Owner
        ↓
defines WHAT + priorities + acceptance
        ↓
specialized AI engineers
        ↓
documented boundaries + handoffs
        ↓
workflows / product
```

## Role categories

### Product execution

- **Function Engineer** — owns one bounded business capability.
- **Pipeline Engineer** — owns execution order, handoffs, retries, pauses, and pipeline state.
- **Workflow Engineer** — maps user/business intent into a composition of existing functions.
- **UI Engineer** — connects screens, forms, buttons, and dashboards to workflows.

### Integration

- **Integration Engineer** — owns a reusable external capability boundary.
- **API Engineer** — owns one API/SDK integration.
- **MCP Engineer** — owns one MCP server/tool integration.
- **CLI Engineer** — owns one CLI/tool integration.
- **Stripe Engineer** — owns Stripe plumbing and provider behavior.
- **Auth Engineer** — owns authentication/session/identity plumbing.
- **Email Engineer** — owns email-provider connectivity and reusable email capabilities.
- **Model Engineer** — owns model-provider connectivity and structured model invocation.

### Data and runtime

- **Database Engineer** — owns shared schema, migrations, constraints, and data plumbing.
- **Queue/Worker Engineer** — owns reliable background execution transport.
- **Storage Engineer** — owns object/file storage integration and artifact persistence.
- **Platform Engineer** — owns generic shared runtime capabilities such as config, DB clients, Redis, telemetry, and environment boundaries.

### Quality and delivery

- **Verification Engineer** — independently proves that the real production path satisfies the declared contract.
- **Security Engineer** — reviews secrets, trust boundaries, permissions, tenant isolation, and unsafe data flow.
- **Observability Engineer** — ensures operators can see what ran, what failed, and why.
- **Deployment Engineer** — owns build/runtime/deployment configuration without changing product behavior.

A project does not need every role.

Use the smallest set of roles required by the product.

## Role rule

> **Roles define responsibility, not implementation instructions.**

A Stripe Engineer role file should not say:

> "Use endpoint X, call method Y, then parse field Z."

It should say:

> "Own the Stripe boundary. Provide normalized operations required by the product. Handle authentication, provider errors, idempotency, webhook normalization where required, and document the interface. Do not decide product pricing policy."

The AI engineer determines how to implement that responsibility.

## Founder / vibe-coder responsibility

The founder owns:

- product intent;
- business rules;
- priorities;
- user experience;
- required capabilities;
- acceptance criteria;
- what role is assigned.

The AI engineer owns:

- implementation details inside the assigned boundary;
- code structure;
- appropriate libraries/SDKs;
- internal interfaces;
- tests;
- debugging;
- technical decisions;
- keeping its handoff current.

The founder should not have to become the Stripe expert, OAuth expert, queue expert, or database expert just to make the AI finish.

## Handoff standard

Every engineer finishes by leaving the next engineer enough information to continue without the old chat session.

For function work:

- `FUNCTION.md`
- `DESIGN.md`

For integrations:

- `INTEGRATION.md`
- `DESIGN.md`

For pipelines/workflows:

- pipeline/workflow contract
- `DESIGN.md`

For platform work:

- capability contract
- `DESIGN.md`

The exact filenames may vary outside standardized function/integration folders, but the handoff principle does not.

## Completion question

At the end of every specialized session, ask:

> **Did this engineer complete its assigned responsibility in the real application, document the handoff, and avoid taking ownership of other engineers' jobs?**

If not, the session is not complete.
