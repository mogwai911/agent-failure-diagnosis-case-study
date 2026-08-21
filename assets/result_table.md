# Source-backed result table

These figures are kept separate because they come from different local run snapshots.

## Diagnostic judge agreement

| Comparison | Cases | Strict L2-primary match | Relaxed primary-or-secondary match | Coverage |
|---|---:|---:|---:|---:|
| Development comparison | 25 | 22 / 25 (88%) | 23 / 25 (92%) | 100% |
| Test comparison | 20 | 14 / 20 (70%) | 15 / 20 (75%) | 100% |

## First-run task snapshots

| Split | Valid trials | Reward 1 | Success rate among valid trials | Infrastructure excluded |
|---|---:|---:|---:|---:|
| Development | 117 | 41 | 35.0% | 11 |
| Test | 29 | 17 | 58.6% | 6 |

## Aggregate diagnostic batch

| L1 label | Count | Share |
|---|---:|---:|
| success | 41 | 33.6% |
| timeout | 39 | 32.0% |
| verifier_fail | 31 | 25.4% |
| infra | 11 | 9.0% |

## Interpretation boundary

The 88/92% and 70/75% figures measure diagnostic-label agreement, not agent success. The split success rates are separate run snapshots and are not a final leaderboard claim.
