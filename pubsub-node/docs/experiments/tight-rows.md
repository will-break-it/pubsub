# The tight rows: gated coverage at each candidate-table floor

**The candidate bucket table doubles *B* with each row so that every floor
holds roughly the same eligible pool — about twenty peers at pick count 10.
The three-digit rows (41–1,293) are where that headroom is smallest, and the
cited experiments never measured them. This closes that gap: gated coverage at
the floor of each row, no admission cap.**

## Provenance

| | |
|---|---|
| Configurations | [`configs/experiments/tight-rows/`](../../configs/experiments/tight-rows/) — 5 cells, μ = 0.2, pick count 10, symmetric (M4), gated by each row's *B*, no admission cap, master seeds 1210–1214, 2 000 draws each |
| Tool commit | as recorded in the cell manifests |
| Criterion | post-churn one-SCC goodness of the up-honest propagation digraph |
| Cap note | the serving cap is omitted on purpose: admission-budget validation is a separate later row (CIP item 1); these cells characterise the gate alone at the floor |

## 1. What was run, and what came back

| N | *B* | eligible pool (N−1)/B | bad / draws | Wilson 95 % upper bound | degree mean |
|---:|---:|---:|---:|---:|---:|
| 41 | 2 | 20.0 | 0 / 2 000 | 1.9 × 10⁻³ | 14.99 |
| 81 | 4 | 20.0 | 0 / 2 000 | 1.9 × 10⁻³ | 15.00 |
| 161 | 8 | 20.1 | 0 / 2 000 | 1.9 × 10⁻³ | 14.99 |
| 321 | 16 | 20.0 | 0 / 2 000 | 1.9 × 10⁻³ | 14.99 |
| 641 | 32 | 20.0 | 0 / 2 000 | 1.9 × 10⁻³ | 14.99 |

## 2. Reading

- **The floor is not the binding constraint.** No run stranded a node at any
  floor. The isolation the gate could produce here is the empty-pool channel,
  ≈ e^(−(1−μ)(N−1)/B) = e^(−16) ≈ 10⁻⁷ at every floor — far below δ = 10⁻⁴ —
  so a 2 000-draw pass is the expected result, not a strong tail measurement.
- **Constant headroom shows in the degree.** The degree mean is 15.00 at all
  five floors: own picks (10) plus accepted picks (≈ 10 × the constant pool)
  minus the mutual-pick overlap, exactly what a table that holds
  *r* = (N−1)/(B·k) ≈ 2 promises. The bucket table does what it is for.
- **The open tail is structural, not gate-width.** These cells do not certify
  P(bad) ≤ 10⁻⁴ (that needs ~10⁵+ draws); they establish there is no gross
  failure at the floor. The candidate *B* rows' δ-bind plus the admission
  budget is item 1's measurement, carried out at the larger populations.

## 3. Limits

The pass is consistent with the empty-pool law but does not measure it; a
defect rarer than ~1.9 × 10⁻³ is not excluded by these cells. The adversarial
class picks from its own gate survivors and withholds — it does not dial every
eligible peer (that flooding arm is the cap's problem, item 1).
