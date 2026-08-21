# Sanitized Example Evaluation

~~~json
{
  "run_id": "sanitized-example-01",
  "success": false,
  "l1": "verifier_fail",
  "l2_primary": "verifier_fail.misunderstanding",
  "l2_secondary": [],
  "root_cause_stage": "verification_contract",
  "evidence": [
    "The service started without an infrastructure exception.",
    "The agent-created endpoint passed a local smoke check.",
    "The external verifier used a different required endpoint path.",
    "The verifier-facing connection failed because the required path was absent."
  ],
  "confidence": 0.91,
  "recommended_fix": [
    "Promote the exact endpoint path to an explicit acceptance criterion.",
    "Run a verifier-facing contract check before reporting completion."
  ]
}
~~~

## Interpretation

- **L1** says the run reached verification but did not satisfy it.
- **L2** identifies the interface-contract misunderstanding.
- The empty secondary list avoids blaming infrastructure without evidence.
- The evidence is intentionally short and observable.

This is the form that can be passed to a diagnostic judge or reviewed by a human without exposing the full execution trace.
