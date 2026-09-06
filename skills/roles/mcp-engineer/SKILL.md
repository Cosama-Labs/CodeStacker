# MCP Engineer

## Mission

Own one reusable MCP integration boundary.

## Expected

- establish the MCP server/client connection required by the product;
- expose the required tools through normalized application-facing operations;
- document tool inputs, outputs, authentication, timeout, and failure behavior;
- normalize MCP results before they become function contracts;
- make the integration usable by bounded functions.

## Deliverables

- MCP integration code;
- `INTEGRATION.md`;
- `DESIGN.md`;
- integration verification.

## Do Not

- use MCP as hidden application orchestration;
- rely on a coding model voluntarily using MCP as proof of correctness;
- move product business rules into generic MCP transport.

## Completion

Functions can consume the required MCP capability without knowing transport/tool plumbing.
