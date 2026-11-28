# The first bucket-table row: completeness at the gate-off sizes

**The candidate bucket table switches the gate off for 2–40 nodes (B = 1).
What stands behind coverage there is the deposit, not the gate — provided the
topology is effectively complete. This closes the row's two open questions:
completeness at eleven and below, and the realised degree and isolation at the
twelve-to-forty points the cited work never measured.**

## Provenance

| | |
|---|---|
| Configurations | [`configs/experiments/first-row.toml`](../../configs/experiments/first-row.toml) — 7 cells, μ = 0.2, pick count 10, symmetric (M4), gate off, master seed 1200, 2 000 draws each |
| Tool commit | as recorded in the cell manifests |
| Criterion | post-churn one-SCC goodness of the up-honest propagation digraph ([framework README](../../configs/experiments/README.md)) |

## 1. What was run, and what came back

| N | bad / draws | Wilson 95 % upper bound | standing degree mean | degree max (all 2 000 runs) |
|---:|---:|---:|---:|---:|
| 5 | 0 / 2 000 | 1.9 × 10⁻³ | 4.00 | 4 |
| 8 | 0 / 2 000 | 1.9 × 10⁻³ | 7.00 | 7 |
| 11 | 0 / 2 000 | 1.9 × 10⁻³ | 10.00 | 10 |
| 12 | 0 / 2 000 | 1.9 × 10⁻³ | 10.91 | 11 |
| 20 | 0 / 2 000 | 1.9 × 10⁻³ | 14.74 | 19 |
| 30 | 0 / 2 000 | 1.9 × 10⁻³ | 16.55 | 26 |
| 40 | 0 / 2 000 | 1.9 × 10⁻³ | 17.42 | 29 |

## 2. Reading

- **Completeness at eleven and below is confirmed exactly.** At N = 5, 8, 11 the
  mean standing degree equals N − 1 (4.00 / 7.00 / 10.00): the pick count of 10
  saturates the population, every honest pair holds a link, and there is no
  topology-induced isolation by construction.
- **At twelve and above nothing degrades within sampling.** At N = 12 a node
  picks 10 of 11 others and no run stranded any node in 2 000 draws (upper
  bound ≈ 1.9 × 10⁻³); the reciprocal symmetric link covers the one peer a
  node does not itself pick. The same holds through N = 40, where the deepest
  observed node carries 29 standing links (its own 10 picks plus 19 accepted).
- **This is the gate-off regime working as sized.** Coverage rests on the
  deposit for the whole row; the topology itself contributes no isolation at
  these populations. The CIP's guidance for the row (C ≥ N − 1 where the gate
  is off, pick count raised to full connectivity at the smallest sizes) is
  consistent with these measurements, not contradicted by them.

## 3. Limits

The cells confirm the absence of a *topology* isolation path; they say nothing
about an adversary who can buy identities cheaply in the gate-off regime — that
risk is the deposit's job and is priced under the resource model, not here.
2 000 draws leave a residual upper bound of ~1.9 × 10⁻³, so a rarer defect is
not excluded; the row claims completeness, not a ≤ 10⁻⁴ tail.
