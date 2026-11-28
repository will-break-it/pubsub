# What a testnet deployment still needs to be economically viable for an operator

**Status:** working note · **Feeds:** CIP-0201 Path to Active ("explain how PubSub
node operators cover those costs and sustain participation") · **Depends on:**
ADR 0047 (identity cost as flow), ADR 0045 (parameter authority).

This note enumerates the concrete gaps between the current prototype and a
real testnet deployment whose economics an operator can actually evaluate. It is
deliberately ordered by what an operator needs to *sign off on running a node*,
not by the CIP's own section order.

## 1. The cost side is only half-priced

An operator standing up a node today can read its **traffic** cost — Table 6 in
the design companion gives per-node ingress and link counts against subscriptions
— but cannot read its **capital** cost, because two numbers are unfixed:

- the **deposit** (the stock), and
- **`f`** plus the **epoch length** (the flow, ADR 0047).

Without those, the operator's joint is unknown and every other deployment
decision — which topics to subscribe to, what hardware, whether to run at all —
is undefined. This is the single blocking item: **publish a candidate (deposit,
*f*, epoch-length) triple**, which `docs/identity-cost-sizing.md` reduces to the
pair (*D*, *f*) plus the epoch length that annualises *f*.

## 2. The chain itself is the single point of failure, unfenced

Registration, snapshot, revocation and parameter reads all sit on Cardano. A halt
or fork freezes or splits every one of them. The CPS flags this; the CIP records
"behaviour through a chain halt, fork or unavailable service" as an acceptance
item but does not specify it. For an operator this is the difference between "my
node degrades gracefully from the last snapshot" and "my node is dead and I am
paying fees for nothing". Two things are missing:

- a **degraded-mode specification**: which operations continue from a snapshotted
  state, which suspend, and what an operator's node does at the boundary;
- a **beacon source decision**, because a chain-derived beacon stops exactly when
  the chain the alert channel is meant to outlive stops (CIP issue #22).

## 3. Signing and the registries are mocked, so nothing on-chain exists to run

The reference node uses mock crypto and in-memory registries. No Aiken validator
exists for the node registry, topic registry or parameter output. An operator
cannot register, cannot lock a deposit, cannot be authorised as a publisher —
there is no ledger-side object to participate against. The on-chain rules also
carry genuine open questions that block a validator, chiefly:

- the **slot↔dissemination-epoch mapping** the fee and claim arithmetic share
  (ADR 0047's named follow-up), and
- the **wire/handshake recipient and deployment binding**, without which replay
  and cross-deployment confusion are open.

## 4. Sustainable participation has no defined revenue or subsidy path

The CIP states participation "as assumptions, not solved": deposits are
reclaimable and earn nothing, rational silence folds into μ. For a testnet that
is acceptable — a testnet is subsidised by definition. What is missing is the
**statement of who subsidises it** on the path to the flight run: a testnet
needs a public commitment to reimburse/offset operator costs (fees, the
deposit's time-value, the participation fee *f*), or the honest operators are
the only ones paying. This is not a consensus-mechanism design; it is a
deployment-logistics item the profile must record.

## 5. What is already in place (so the gap is the remainder, not the whole)

- The protocol core, strategy seams, and the deterministic experiment driver
  exist and run; the five models and the gated M4 reference are measured.
- The selected design (M4, symmetric links, hash-gate baseline) is evidence-backed
  at the reference configuration, with the serving-cap and bucket-count sizing
  recipe reproduced number-for-number.
- The node/topic/parameter **schemas** are drafted in CDDL; the authority and
  identity-cost semantics are now pinned (ADR 0045–0047).

## 6. The minimal cut — a list a testnet can be stood up on

For a *testnet* (not the flight run), the viable subset is:

1. Aiken validators for node/topic/parameter outputs, with real Ed25519 signing
   replacing `crypto/mock` (item 3).
2. The slot↔epoch rule (item 3), and a published candidate (*D*, *f*, epoch).
3. A degraded-mode + beacon statement (item 2).
4. A testnet subsidy/subscription statement so operator economics are real (item 4).

Everything else (two interoperating implementations, formal gated derivation,
the full "What remains to be measured" programme) is flight-run work and can
trail the testnet. This ordering is the honest answer to "what is missing for a
real testnet deployment": the four items above, in that order.