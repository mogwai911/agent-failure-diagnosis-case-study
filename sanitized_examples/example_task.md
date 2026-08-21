# Sanitized Example Task

## Task type

Environment setup with an external interface contract.

## Goal

Configure a virtualized service and expose a monitor endpoint that an automated verifier can reach. The service itself must be functional, and the endpoint must be available at the exact path specified by the task contract.

## Acceptance conditions

1. The service starts successfully.
2. The monitor endpoint exists.
3. The endpoint is reachable through the verifier-facing path.
4. A test command can send a control action and receive a valid response.
5. The final environment remains available for the verifier.

## Public redactions

- Benchmark-specific task identifiers are omitted.
- Raw container paths and timestamps are normalized.
- Full command output is omitted.
- No credentials, private paths, or raw logs are included.

## Why this example was selected

It shows a common agent failure that is easy to understand across technical and non-technical audiences: the component works locally, but the external consumer cannot find it because the interface contract is wrong.
