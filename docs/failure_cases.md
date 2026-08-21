# Sanitized Failure Cases

These cases are summarized from local analysis notes on public benchmark tasks. Raw local trajectories, personal paths, credentials, environment configuration, and unrelated artifacts are intentionally omitted.

## Failure Case 1: A working component exposed at the wrong path

**Task pattern:** environment setup plus an external socket contract.

**What happened:**  
The agent configured the service and created a working monitor endpoint, but its helper scripts used one socket path while the verifier expected another. The agent's own check passed because it tested the path it had created. The verifier-facing test could not connect.

**Why it mattered:**  
The implementation was functionally close, but the external contract was not satisfied. A terminal agent can look finished while still being unreachable to the system that consumes its output.

**Diagnosis:**  
'verifier_fail.misunderstanding' — an interface/path contract was misunderstood. This is not primarily an infrastructure outage and not a missing implementation.

**Fix direction:**  
Make the verifier-facing path a first-class acceptance criterion. Check the exact path, name, and format from an independent test before declaring completion.

## Failure Case 2: Accessing a WAL-backed database before preserving evidence

**Task pattern:** recover all records from a SQLite database and its WAL file.

**What happened:**  
The agent opened the database before copying and inspecting the WAL file. The database access triggered checkpoint behavior; the WAL evidence then disappeared. The agent spent the remaining run searching for the lost data and eventually produced a plausible but incomplete reconstruction.

**Observed verifier evidence:**  
Five of seven tests passed, but the recovered output did not include the WAL update expected for an existing record.

**Diagnosis:**  
'verifier_fail.logic_error' — the agent understood the general recovery objective but chose an unsafe execution order.

**Fix direction:**  
For potentially corrupted, encrypted, or state-changing inputs: preserve the original first, inspect the copy second, and only then perform operations that may checkpoint, rewrite, or delete evidence.

## Failure Case 3: Slow progress versus a stuck loop

**Task pattern:** design a batching scheduler under multiple cost, padding, latency, and shape constraints.

**What happened:**  
The agent ran for 1,800 seconds across 89 steps and created more than 20 scheduler variants. It moved metrics closer to several thresholds, but the final run still failed shape coverage and cost checks. The last recorded cost was approximately 3.14e11 against a 3.0e11 limit, and the selected shapes did not cover a required aligned sequence length.

**Diagnosis boundary:**  
Human analysis labeled the run 'timeout.slow_progress' because the agent made genuine changes and improved some metrics. The automated judge labeled it primarily 'timeout.stuck_loop' with a reasoning-failure secondary because the agent repeated parameter tuning without changing its fundamental strategy.

**Why this case matters:**  
The disagreement is not noise to hide. It identifies a rubric boundary: progress can be real while the search strategy is still non-convergent.

**Fix direction:**  
Add explicit stop and strategy-change conditions. After repeated failures on the same constraint set, require a feasibility analysis or a different algorithmic approach rather than another threshold tweak.

## Failure Case 4: A mathematically valid basis is not the requested matrix

**Task pattern:** recover a model matrix under a permutation-and-scaling equivalence contract.

**What happened:**  
The agent interpreted the task as recovering a lower-dimensional basis and produced a 10×10 matrix. The verifier expected all rows of the required matrix and tested against a 30×10 shape. The agent completed its own SVD-based workflow but solved a different problem.

**Diagnosis:**  
'verifier_fail.misunderstanding' — the mathematical technique was coherent, but the allowed equivalence transformation was misread. Row permutation and scaling did not authorize reducing the number of rows or replacing the rows with a basis.

**Fix direction:**  
Turn shape and equivalence constraints into executable preconditions. Before optimizing the algorithm, verify that the artifact has the required dimensions and that every transformation is explicitly allowed.

## Cross-case lesson

| Pattern | What failed | Better control |
|---|---|---|
| Wrong path | External interface contract | Verifier-facing contract test |
| Unsafe order | Evidence preservation | Backup and inspect before mutation |
| Non-convergent search | Strategy selection and stopping | Strategy-change threshold |
| Wrong equivalence | Task interpretation | Shape and semantic preconditions |

The useful unit of improvement is therefore not “make the agent more careful” in the abstract. It is a concrete control attached to an observable failure pattern.
