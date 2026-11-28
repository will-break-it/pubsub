# Degraded mode and the randomness beacon

**Status:** working note · **Feeds:** CIP-0201 "behaviour during a chain halt,
fork or unavailable service" (an acceptance criterion) and the open beacon
source (issue #22) · **Depends on:** ADR 0045 (parameter authority), ADR 0048
(epoch schedule).

This closes two coupled open items the CPS presses on: what a node does when the
chain it is anchored to degrades, and where the per-epoch randomness comes from
without inheriting the chain's own failure modes.

## 1. Degraded mode

A PubSub topology is fixed per epoch: once a snapshot and its randomness are
known, the topology does not consult the chain again until the next cutoff.
Degraded mode is the discipline for what uses that already-fixed state and what
does not.

**Continues from existing state**

- Topology derivation and link establishment for an epoch whose snapshot and
  randomness are already fixed. A node that has both keeps relaying, verifying
  and recovering exactly as in a healthy epoch.
- Signature and publisher-authorisation verification against the snapshot's
  registry view; a revocation that happened before the snapshot is honoured.
- Recovery and gap-notification among already-connected peers.

**Suspends**

- Registration, retirement, topic changes and revocation reads: these need a
  fresh chain position and have no effect until one is available.
- New-epoch advancement: a fresh snapshot and beacon value are required, and
  neither can be produced during a halt.

**At the boundary — two behaviours, chosen by deployment**

- **Hold the schedule.** The current topology stands past its nominal end until
  the beacon and snapshot are available; the epoch length de facto extends. This
  is the default and preserves the guarantee statement's conditions (a fixed
  topology standing for the period it actually stands).
- **Fall back to operator-configured links.** A node MAY additionally hold
  standing links it is configured with, used only while the epoch boundary
  cannot advance. These are trusted availability edges, not part of the
  topology's coverage guarantee, and their public key is pinned out of band.

**Fork.** On a fork, peers that disagree on the cutoff position compute
different topologies until they reconverge on a common prefix. The parameter
output and registry script hashes are the deterministic anchor; nodes SHOULD
require the deployment's confirmation depth before adopting a snapshot, the
same depth used for revocation.

## 2. The randomness beacon

The beacon supplies one value η per epoch; it must be public, unbiasable, and
known only after membership is fixed. The candidate sources differ only in
liveness and cadence:

| Source | Cadence | Liveness with the chain |
|---|---|---|
| Ledger epoch nonce (chain-derived) | 5-day ledger epochs | stops when the chain stalls |
| Threshold VRF / public randomness service, posted on chain | the service's schedule | follows the service, not the chain, once posted |
| External value committed at a fixed position | operator-chosen | as the posting layer |

**Recommendation.** For a **testnet**, the ledger epoch nonce is the correct
cheapest source: its cadence sets a 5-day floor on the epoch length, which the
churn budget already admits, and it meets the ordering requirement by
construction. Its cost — that the beacon stops with the chain — is acceptable on
a testnet and is exactly what §1's degraded mode covers. For the **flight run**
this coupling is the wrong trade (the alert channel should not share a
liveness dependency with the chain it reports on), and the threshold-VRF or an
externally-posted-but-committed value is the candidate; that choice is a
deployment decision recorded in the parameter output, not a protocol constant.

The source is therefore a per-deployment setting. The ordering rule is
invariant across sources: the epoch's snapshot (registration cutoff) precedes
the determination of η, whatever produces η.