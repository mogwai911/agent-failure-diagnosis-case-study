# Workflow Design

The project treats a terminal task as an execution loop with an explicit acceptance boundary.

## Stage 1: Task contract

Before the agent acts, the task needs to be reduced to a small contract:

- required input files or services;
- permitted tools and environment assumptions;
- expected artifact;
- exact path, name, format, and content constraints;
- verifier-facing checks.

This stage matters because many failures are not failures to write code. They are failures to preserve or satisfy a contract.

## Stage 2: Agent planning

The agent proposes a plan and identifies the first low-cost observations it should make. A useful plan includes:

- environment inspection;
- dependency and file checks;
- a minimal implementation path;
- incremental validation;
- a final verifier-facing check.

The human review question is not “is this plan elegant?” but “does it protect the important evidence and cover the acceptance conditions?”

## Stage 3: Terminal execution

The agent runs commands and changes the environment. Each tool result is part of the evidence:

- command and arguments;
- exit code;
- stdout/stderr;
- files created or modified;
- tests run;
- time spent;
- errors returned by the runner.

The workflow should preserve enough of this information to locate the first meaningful divergence without replaying an entire raw trace.

## Stage 4: Feedback and recovery

When a command fails, the agent should update its plan based on the new evidence. A good recovery loop changes the hypothesis or strategy when the previous one is disproven.

Two guardrails are especially important:

1. Preserve potentially destructive inputs before opening or mutating them.
2. Stop repeating the same failed action unless a new observation justifies it.

## Stage 5: Verification

The benchmark verifier checks the actual artifact. Passing a self-authored smoke check is not enough if the smoke check uses a different path, schema, or assumption from the external verifier.

## Stage 6: Diagnosis

For a failed run, the diagnostic evidence pack should be small but sufficient:

- top-level reward and exception;
- failed test names and key assertions;
- the first suspicious tool call;
- the last relevant trajectory segment;
- any environment or network error.

The judge maps this evidence to L1 and L2 labels. Human review remains important for borderline cases.

## Stage 7: Iteration

Each iteration should change one major variable:

- prompt or behavior constraint;
- tool protocol;
- wrapper or timeout handling;
- task decomposition;
- evaluator rule;
- model configuration.

The next run should check both the overall task outcome and the targeted failure distribution. A lower count for one failure type is not enough if the failures merely moved to another category.
