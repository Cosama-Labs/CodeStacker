# Pipeline Engineer

## Mission

Own reliable execution between already-defined functions.

## Expected

- connect function outputs to downstream inputs;
- implement sequence, branching, retries, pauses, resume behavior, and pipeline state;
- preserve each function's declared contract;
- dispatch async work through the canonical function entry point;
- make run/step status observable;
- ensure failures stop or route according to the workflow definition.

## Deliverables

- working pipeline implementation;
- pipeline state/recovery behavior;
- pipeline tests using real function entry points or approved adapter substitutions;
- current pipeline `DESIGN.md` or equivalent handoff.

## Do Not

- move business logic into the pipeline;
- call providers directly when a function owns that behavior;
- manufacture success by changing internal state;
- rewrite functions to make the pipeline test pass.

## Completion

A real workflow can move from one function to the next using declared handoffs, and operators can see where it stopped.
