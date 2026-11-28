# Identity cost sizing: from a lockup figure to a sustained flow

**Status:** working note · **Feeds:** the deployment profile (AP6), CIP-0201
"deployment profile stating adversarial participation, identity cost …" ·
**Depends on:** ADR 0047 (per-epoch, per-topic participation fee `f`).

This note does not fix a deposit value. It turns the open question the review
pressed on — "how large a deposit makes 20 % adversarial participation
unaffordable?" — into the form the flow-cost mechanism makes answerable. The
underlying arithmetic appears in earlier logbook entries; the contribution here
is to restate it in the `f` unit ADR 0047 introduces, so the deployment profile
can be sized against a *continuing cost* instead of a *one-time lockup*.

## 1. What one identity must cost, in two regimes

The gate divides an identity's cost into two regimes, and the decision only
matters in the second.

- **Below the gate** (small topics, *B* = 1) the cost of an identity is simply
  its price. Every joint identity buys a position, so μ tracks the ratio of the
  attacker's budget to the price of one identity.
- **Above the gate** (*B* > 1) the cost is multiplied by the eligibility
  divisor: one identity is eligible to a chosen victim with probability 1/*B*,
  so an attacker pays roughly *B* deposits to place one useful hostile peer
  next to a target. At the reference *B* = 512 that is a ~500× cost hike before
  the serving cap is even counted.

Sizing must therefore be stated per row of the bucket table, not as one number.

## 2. The reference scenario, restated as a flow

The evaluation scenario holds a topic at *N*<sub>T</sub> = 20 000 with the
assumed adversarial share μ = 0.2, i.e. *S* = 4 000 hostile identities standing
at once. Under a pure lockup that is a stock figure: 4 000 simultaneous
deposits. Under ADR 0047 it is instead a **flow**:

$$
\text{cost}_{\mu=0.2} = 4000 \cdot \bigl(\text{time-value of the locked deposit}\bigr)
 + 4000 \cdot f \cdot |\text{topics}| \cdot \text{epochs per year}.
$$

The first term is the familiar lockup; the second is new and is what makes the
figure *standing* rather than *one-time*. An attacker who holds the position
pays `f` per epoch, per identity, per topic, forever. An honest SPO subscribed
to one or two topics pays the same `f`, which is the point: the fee scales with
surface area, so the adversary's many-topic footprint is priced while the
honest single-topic node is not.

## 3. The one quantity that is still not claimable

What this note does **not** supply — and what no mechanism can supply without a
deployment decision — is the absolute spend that makes μ = 0.2 unaffordable.
That number is the product of two deployment choices, neither yet made:

- the deposit (the stock component), and
- `f` and the epoch length (the flow component).

For example, holding the stock component fixed at a value *D*, the annual flow
an attacker must finance is `4000 · f · topics · (epochs per year)`. The
deployment profile can therefore be stated as a chosen pair (*D*, *f*) that
sets the attacker's annual identity bill, and that bill — not a bare lockup —
is the honest, reviewable answer to the question.

## 4. Placement

- ADR 0047 defines the mechanism; this note defines what the deployment profile
  must publish to use it (the pair *D* and *f*, plus the epoch length that
  turns `f` into a yearly rate).
- The next concrete step is a worked example pricing one candidate (*D*, *f*)
  pair against a stated attacker capital budget, which belongs in the
  deployment-profile document, not this note.