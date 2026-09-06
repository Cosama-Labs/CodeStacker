# Mixed-stack example

Code Stacker contracts allow individual functions to use different runtimes without turning the application into a collection of ad-hoc microservices.

```text
pipeline/control plane: Python

01_ingest      Python
02_validate    Go
03_payment     TypeScript
04_enrich      Python + model API
05_build       Node.js
06_publish     Go
```

The implementation transport can differ by step:

- direct in-process call for same-runtime synchronous functions
- durable job + worker for asynchronous functions
- queue/RPC for cross-runtime execution

The pipeline still sees a uniform contract:

```json
{
  "step": "03_payment",
  "status": "passed",
  "result": {
    "authorization_id": "auth_123"
  },
  "error": null
}
```

The downstream function does not know whether that authorization came from TypeScript, Python, Stripe SDK, or an internal billing service.
