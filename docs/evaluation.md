# Evaluation Design

## 1. Two questions, two layers

The project distinguishes:

1. **Did the agent complete the task?**  
   The Terminal-Bench/Harbor verifier and reward answer this question.

2. **Why did the run fail?**  
   Human analysis and the LLM-based diagnostic judge answer this question.

The judge does not replace the verifier and should not become the primary task KPI.

## 2. L1 and L2 labels

### L1: observable outcome

L1 gives a compact top-level classification:

- 'success'
- 'timeout'
- 'verifier_fail'
- 'infra'

The exact set can evolve with the runner, but the purpose is stable: separate what happened at the outcome level.

### L2: primary cause

L2 explains the dominant failure pattern. Examples include:

- 'verifier_fail.logic_error'
- 'verifier_fail.misunderstanding'
- 'verifier_fail.wrong_output'
- 'verifier_fail.knowledge_gap'
- 'timeout.tool_hang'
- 'timeout.slow_progress'
- 'timeout.stuck_loop'
- 'infra.network'
- 'infra.daytona'

A secondary label is allowed only when it represents an independent, evidence-supported contributor. Otherwise it should remain empty.

## 3. Strict and relaxed diagnostic agreement

The comparison reports use two agreement definitions:

- **Strict:** the judge's L2 primary label exactly matches the human L2 primary label.
- **Relaxed:** the human L2 primary label appears in the judge's primary or secondary labels.

These definitions measure diagnostic-label agreement. They do not measure agent task success, benchmark reward, or production reliability.

The observed comparisons are:

| Comparison | Cases | Strict | Relaxed | Coverage |
|---|---:|---:|---:|---:|
| Development | 25 | 88% | 92% | 100% |
| Test | 20 | 70% | 75% | 100% |

The lower test agreement is not hidden. It points to genuine boundary cases such as logic error versus knowledge gap, and stuck loop versus tool hang or slow progress.

## 4. Evidence-first output

A public version of the diagnostic output can use a compact schema:

~~~json
{
  "run_id": "sanitized-example-01",
  "success": false,
  "l1": "verifier_fail",
  "l2_primary": "verifier_fail.misunderstanding",
  "l2_secondary": [],
  "root_cause_stage": "verification_contract",
  "evidence": [
    "The agent-created endpoint was reachable at one path.",
    "The external verifier attempted the required endpoint at a different path.",
    "The agent's self-check did not exercise the verifier-facing path."
  ],
  "confidence": 0.91
}
~~~

The evidence field should point to observable signals, not to a post-hoc story unsupported by the trace.

## 5. Dataset boundaries

The intended evaluation contract separates:

- **Agent development:** frequent iteration and failure discovery;
- **Agent regression:** a stable set for checking small changes;
- **Agent test:** a less frequently used set for milestone reporting;
- **Judge development:** examples used to refine the diagnostic prompt or rules;
- **Judge regression:** fixed examples used to detect diagnostic drift.

These are different axes. Agent task splits measure the agent. Judge splits measure the stability of the diagnostic layer.

## 6. Snapshot results

Separate local reports provide these task-run snapshots:

- Development: 41 successes out of 117 valid trials (35.0%), with 11 infrastructure errors excluded.
- Test: 17 successes out of 29 valid trials (58.6%), with 6 rate-limit errors excluded.

An aggregate diagnostic batch contains 122 labels:

| L1 label | Count |
|---|---:|
| success | 41 |
| timeout | 39 |
| verifier_fail | 31 |
| infra | 11 |

The numbers describe different snapshots and should not be merged into a single final score.

## 7. Practical decision rule

When a change is proposed:

1. run the controlled task set;
2. inspect verifier outcomes;
3. label the relevant failures;
4. compare the targeted failure mode;
5. check for new failure modes;
6. keep, revise, or reject the change.

If an apparent improvement is smaller than the observed run-to-run variation, it should be treated as inconclusive until rerun.
