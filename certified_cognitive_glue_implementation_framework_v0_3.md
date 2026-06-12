# Certified Cognitive Glue — Implementation Framework v0.3

**Author:** Benjamin John Schulz  
**Project:** Certified Cognitive Glue  
**Document type:** engineering / research design document  
**Status:** implementation blueprint  
**Revision note (v0.3):** net-surplus ledger conservation replaces raw-positive-payout conservation; scarcity prices default to epsilon-normalized time debt; global-liveness export gains a monitor-semantics caveat; checker gates and attack expectations updated accordingly. v0.2 additions (exact rational checker, rational ledger, hash-chained log, Gamma-soundness, deviation-robustness, escrow release, fail-stop separation) are retained.  
**Date:** June 2026

---

## 0. Executive summary

The goal is to turn the paper's verification architecture into a buildable multi-agent research system.

The system should not be an end-to-end neural alignment demo. It should be a **verification-first multi-agent stack** in which open-source AI models help propose norms, train policies, summarize counterexamples, or plan under constraints, while a deterministic checker remains the source of certification.

The core output of a successful run is a reproducible certificate package:

```json
{
  "environment_hash": "...",
  "policy_hash": "...",
  "monitor_hash": "...",
  "norms": ["..."],
  "streett_pairs": ["..."],
  "certificates": {"...": {"epsilon": 0.0, "Wmax": 0.0, "status": "PASS"}},
  "glue_map_hash": "...",
  "ledger_rules_hash": "...",
  "attribution_rules_hash": "...",
  "rebasing_log_hash": "...",
  "deviation_class_hash": "...",
  "checker_status": "CERTIFIED"
}
```

The first implementation target is intentionally small: a finite two-state obligation monitor, then a tiny two- or three-agent commons environment. Only after the checker, ledger, attribution, and rebasing logic work should transformer or Hugging Face LLM policies be added.

---

## 1. Guiding constraints

### 1.1 The checker is non-neural

Open-source models may be used for:

- norm proposal;
- natural-language-to-DSL drafting;
- policy learning;
- high-level planning under a fixed action schema;
- counterexample summarization;
- abstraction-refinement suggestions.

They must not be used for:

- final certificate acceptance;
- final ledger validity;
- final attribution validity;
- final governance rebasing validity;
- final small-gain or stopped-pair theorem checks.

The checker should be deterministic, replayable, and hashable.

### 1.2 Certification happens after policy freezing

A policy can be trained with glue prices, shaping rewards, and norm proposals, but certification only applies to a **frozen policy snapshot**.

A certified policy artifact should include:

```text
policy architecture
weights hash
observation encoder hash
action mask hash
temperature / sampling parameters
finite controller memory specification
LLM prompt/template hash, if any
adapter hash, if any
```

### 1.3 The certified state must be explicit

The certified state is not just the environment state. It includes every variable required by the proof and accounting system:

```text
CertifiedState = PhysicalState
               + MonitorState
               + AgentControllerMemory
               + NormBeliefTransducerState
               + LedgerState
               + GovernanceEpochState
               + DeviationClassState
```

If a variable affects monitors, policy behavior, ledger entries, attribution, or rebasing, it must be in the certified state or explicitly abstracted with a soundness proof.

### 1.4 Training rewards are not verification

Potential-based shaping is a synthesis/training tool. It does not make a policy safe. After training, the frozen policy must still pass the certificate checker.

---

## 2. Top-level architecture

```text
                         ┌────────────────────────────┐
                         │ Human / researcher inputs   │
                         │ - natural language norms    │
                         │ - environment design        │
                         │ - governance interventions  │
                         └─────────────┬──────────────┘
                                       │
                                       ▼
┌───────────────────┐        ┌──────────────────────┐
│ HF norm proposer  │───────▶│ Norm DSL compiler     │
│ optional          │        │ deterministic          │
└───────────────────┘        └───────────┬──────────┘
                                         │
                                         ▼
                                ┌──────────────────┐
                                │ Monitor automata │
                                │ Streett pairs    │
                                └────────┬─────────┘
                                         │
                                         ▼
┌───────────────────┐        ┌──────────────────────┐        ┌─────────────────────┐
│ MARL training     │───────▶│ Frozen policy snapshot│───────▶│ Induced kernel build │
│ optional shaping  │        │ hashable artifact     │        │ exact / bounded      │
└─────────┬─────────┘        └──────────────────────┘        └──────────┬──────────┘
          │                                                              │
          ▼                                                              ▼
┌───────────────────┐                                         ┌─────────────────────┐
│ Glue price service│◀────────────────────────────────────────│ Certificate solver   │
│ W-derived prices  │                                         │ LP / interval / PAC  │
└─────────┬─────────┘                                         └──────────┬──────────┘
          │                                                              │
          ▼                                                              ▼
┌───────────────────┐                                         ┌─────────────────────┐
│ Signed ledger     │◀────────────────────────────────────────│ Independent checker  │
│ attribution       │                                         │ theorem obligations  │
│ rebasing          │                                         └──────────┬──────────┘
└─────────┬─────────┘                                                    │
          │                                                              ▼
          │                                                    ┌─────────────────────┐
          └───────────────────────────────────────────────────▶│ Certificate package │
                                                               │ JSON + logs + hashes │
                                                               └─────────────────────┘
```

---

## 3. Repository structure

```text
certified-cognitive-glue/
  README.md
  pyproject.toml
  configs/
    two_state.yaml
    commons_small.yaml
    commons_medium.yaml

  ccg/
    envs/
      __init__.py
      two_state_obligation.py
      commons_grid.py
      pettingzoo_wrapper.py
      torchrl_wrapper.py        # single MARL stack; see section 26

    state/
      __init__.py
      schemas.py
      hashing.py
      serialization.py
      finite_index.py
      invariant.py

    norms/
      __init__.py
      dsl.py
      parser.py
      schema.json
      typechecker.py
      compiler.py
      examples/
        clean_when_polluted.yaml
        compensate_cleaner.yaml
        no_retaliation_after_repair.yaml

    monitors/
      __init__.py
      automata.py
      streett.py
      timers.py
      monitor_runtime.py
      monitor_tests.py

    policies/
      __init__.py
      tabular.py
      mlp.py
      gru.py
      transformer_policy.py
      hf_action_head.py
      action_masks.py
      snapshot.py

    train/
      __init__.py
      rollout.py
      shaping.py
      ppo.py
      mappo.py
      rllib_train.py
      torchrl_train.py
      imitation.py

    cert/
      __init__.py
      kernel_builder.py
      lp_solver.py
      rational_checker.py
      interval_checker.py
      stopped_pair_checker.py
      small_gain.py
      gamma_soundness.py        # GATE-GAMMA-SOUND: state-by-state budget check
      affine_disturbance.py
      conservation.py           # GATE-LEDGER-CONSERVATION + GATE-NET-SURPLUS
      certificate_schema.json
      export.py

    deviation/
      __init__.py
      classes.py                # action mask, logit-bounded, finite transducer
      interval_kernel.py        # union-over-class kernel for GATE-DEVIATION-ROBUST
      robust_check.py

    glue/
      __init__.py
      prices.py
      ledger.py
      attribution.py
      escrow.py
      rebasing.py
      solvency.py
      api.py

    abstraction/
      __init__.py
      cells.py
      least_forgetting.py
      stress_distribution.py
      counterexample_refinement.py
      robust_cell_check.py

    hf/
      __init__.py
      norm_proposer.py
      counterexample_summarizer.py
      constrained_action_model.py
      prompt_templates/
        norm_to_dsl.md
        counterexample_to_patch.md

    attacks/
      __init__.py
      stress_harvest.py
      attribution_collusion.py
      rebasing_attack.py
      positive_only_credit.py
      missing_market_seed.py
      idle_module_disturbance.py

    services/
      __init__.py
      rest_api.py
      dashboard.py
      replay.py

  tests/
    test_two_state_stopped_pair.py
    test_positive_only_credit_fails.py
    test_signed_ledger_telescopes.py
    test_attribution_escrow.py
    test_solvency_no_default.py
    test_governance_rebasing.py
    test_monitor_determinism.py
    test_kernel_builder.py
    test_stopped_pair_lp.py
    test_small_gain.py
    test_gamma_soundness.py
    test_affine_disturbance.py
    test_ledger_conservation.py
    test_escrow_release.py
    test_deviation_robust.py

  scripts/
    run_two_state.py
    train_commons.py
    certify_policy.py
    run_attacks.py
    export_certificate.py
    launch_dashboard.py

  docs/
    implementation_framework.md
    norm_dsl.md
    certificate_format.md
    ledger_format.md
    experiments.md
```

---

## 4. Core data objects

### 4.1 Certified state

Use immutable dataclasses or Pydantic models. Every state must be serializable and hashable.

```python
@dataclass(frozen=True)
class CertifiedState:
    physical: PhysicalState
    monitor: MonitorState
    agent_memory: tuple[AgentMemory, ...]
    norm_beliefs: tuple[NormBeliefState, ...]
    ledger: LedgerState
    governance: GovernanceState
    deviation: DeviationClassState
```

### 4.2 Physical state

For the commons game:

```python
@dataclass(frozen=True)
class PhysicalState:
    grid_shape: tuple[int, int]
    agent_positions: tuple[tuple[int, int], ...]
    resource_levels: tuple[int, ...]
    pollution_level: int
    ownership: tuple[int | None, ...]
    sanctions_active: tuple[bool, ...]
```

For the two-state monitor smoke test:

```python
@dataclass(frozen=True)
class PhysicalState:
    dummy: int = 0
```

### 4.3 Monitor state

```python
@dataclass(frozen=True)
class ObligationInstance:
    norm_id: str
    responsible: str
    trigger_time: int
    remaining_deadline: int
    status: Literal["idle", "pending", "discharged", "violated"]

@dataclass(frozen=True)
class MonitorState:
    obligations: tuple[ObligationInstance, ...]
    streett_flags: tuple[StreettFlag, ...]
    event_log_tail: tuple[str, ...]
```

### 4.4 Ledger state

```python
from fractions import Fraction

@dataclass(frozen=True)
class LedgerState:
    epoch: int
    balances: Mapping[str, Fraction]        # never float: money in floats is a bug,
    escrow_balances: Mapping[str, Fraction] # and float hashing is platform-unstable
    entries_hash: str                       # head of the hash chain (see 13.1)
    last_entry_id: int
```

Balances are exact rationals (or integer micro-units). Floats are forbidden anywhere in the ledger because (a) repeated credit/debit in float drifts, and (b) float serialization is not bit-stable across platforms, which would break the replay guarantee.

### 4.5 Governance state

```python
@dataclass(frozen=True)
class GovernanceState:
    epoch: int
    active_certificate_hash: str
    active_glue_map_hash: str
    rebasing_log_hash: str
    intervention_rights: tuple[str, ...]
```

---

## 5. Norm DSL

### 5.1 Minimal YAML norm format

```yaml
id: clean_when_polluted
kind: obligation
trigger:
  all:
    - field: pollution_level
      op: ">="
      value: high
responsible:
  role: cleaner
postcondition:
  field: pollution_level
  op: "<"
  value: medium
deadline: 5
attribution:
  debit_on: responsible_role
  credit_on: actor_satisfying_postcondition
  ambiguous: escrow
ledger:
  spendable_credit: true
  requires_solvency: true
```

### 5.2 Supported norm kinds

Initial implementation should support only a small set:

```text
prohibition:
  "Do not do X."

obligation:
  "If trigger, then postcondition within tau steps."

repair:
  "If violation, then repair within tau steps."

compensation:
  "If agent i performs costly prosocial work, compensate within tau steps."

non-retaliation:
  "If repair has occurred, do not sanction for that repaired violation."
```

Avoid open-ended deontic logic at first. Keep monitors finite.

### 5.3 Compiler output

For each norm, the compiler emits:

```python
@dataclass(frozen=True)
class CompiledNorm:
    norm_id: str
    monitor_update: Callable[[MonitorState, Event], MonitorState]
    A: Callable[[CertifiedState], bool]
    B: Callable[[CertifiedState], bool]
    attribution_edges: tuple[AttributionRule, ...]
    ledger_rules: tuple[LedgerRule, ...]
    invariant_rules: tuple[InvariantRule, ...]
```

### 5.4 Determinism gate

The compiler must reject norms whose monitor transition is nondeterministic. Asserting that the output has the right *type* does not test determinism (an RNG-using transition passes it too). The real gate has three parts: a purity audit, double-evaluation equality, and exhaustive enumeration of the reachable monitor set (including timer values).

```python
def check_monitor_determinism(compiled_norm, finite_event_set):
    # 1. purity audit: no RNG, wall clock, or mutable closure state
    assert_pure(compiled_norm.transition)   # static/dynamic check; raises on impurity

    reachable = enumerate_reachable_monitor_states(  # BFS incl. timers, bounded
        compiled_norm, finite_event_set
    )
    for m in reachable:
        for e in finite_event_set:
            # 2. double evaluation must agree
            out1 = compiled_norm.transition(m, e)
            out2 = compiled_norm.transition(m, e)
            assert out1 == out2, ("nondeterministic transition", m, e)
            assert isinstance(out1, MonitorState)
            # 3. reachable closure: successor is itself in the enumerated set
            assert out1 in reachable
```

If the reachable set cannot be finitely enumerated (e.g. an unbounded timer), the norm is rejected at admission rather than certified.

---

## 6. Monitor and Streett semantics

### 6.1 Streett pair interface

```python
@dataclass(frozen=True)
class StreettPair:
    id: str
    A: Callable[[CertifiedState], bool]
    B: Callable[[CertifiedState], bool]
    description: str
```

The target condition is:

```text
If A occurs infinitely often, then B occurs infinitely often.
```

The stopped-pair certificate proves the sufficient condition:

```text
Infinite A after the last B has probability zero.
```

### 6.2 Stopped-pair certificate interface

```python
@dataclass(frozen=True)
class StoppedPairCertificate:
    pair_id: str
    W: Mapping[StateId, Interval | Fraction | float]
    epsilon: Interval | Fraction | float
    Wmax: Interval | Fraction | float
    invariant_hash: str
    kernel_hash: str
    status: Literal["PASS", "FAIL", "UNKNOWN"]
```

### 6.3 Certificate inequality

For each state \(x \in I \setminus B\):

\[
\mathbb E[W(X_{t+1}) \mid X_t=x]
\le
W(x)-\epsilon \mathbf 1\{x\in A\setminus B\}.
\]

The checker must verify the inequality **without floating-point tolerance** in the exact lane. Tabular policy probabilities and the finite kernel are exactly rational, so `W`, `P`, and `epsilon` are rationalized and compared with exact arithmetic. A `tolerance` fudge in the authoritative checker would undermine the independent-verification guarantee.

Exact (rational) lane:

```python
from fractions import Fraction

# P[x, xp], W[xp], epsilon are all Fraction
lhs = sum(P[x, xp] * W[xp] for xp in successors[x])      # Fraction
pending = 1 if (A(x) and not B(x)) else 0
rhs = W[x] - epsilon * pending                            # Fraction
assert lhs <= rhs            # exact, no tolerance
```

Interval lane (only when the kernel is sampled/learned, never for known finite models):

```python
# outward-rounded intervals; LHS uses upper bound, RHS uses lower bound
lhs_upper = interval_sum_upper(P_interval[x, xp] * W_interval[xp])
rhs_lower = W_interval[x].lower - epsilon_interval.lower * pending
assert lhs_upper <= rhs_lower
```

The LP solver may use floating point to *propose* `W` and `epsilon`, but the proposal is rationalized and re-verified exactly before any certificate is accepted. The solver must also enforce a declared `epsilon_min` from config; maximizing `epsilon` in floats can otherwise return numerical noise (`epsilon ~ 1e-12`) that "passes" vacuously.

---

## 7. Environments

### 7.1 Two-state obligation environment

This is the first smoke test.

States:

```text
G = good / discharged
P = pending
```

Streett sets:

```text
A = {P}
B = {G}
```

Certificate:

```text
W(G) = 0
W(P) = 1
```

Transition:

```text
P -> G with probability q
P -> P with probability 1 - q
G -> P if external trigger or agent-created stress occurs
G -> G otherwise
```

Check:

\[
\mathbb E[W(X_{t+1})\mid P] = 1-q \le 1-q.
\]

So \(\epsilon=q\).

Expected pending visits before next \(G\):

\[
\mathbb E[\#P\text{-visits before }G] \le \frac{1}{q}.
\]

Required tests:

```text
PASS: stopped-pair inequality holds for q > 0.
PASS: infinite G/P alternation satisfies Streett.
PASS: signed ledger over G -> P -> G sums to zero.
FAIL: positive-only ledger pays +1 per cycle.
PASS: governance rebasing delta is booked to governance, not agent.
PASS: ambiguous attribution routes to escrow.
```

### 7.2 Commons environment

Minimum viable commons game:

```text
grid: 3x3 or 5x5
agents: 2 or 3
resources: apples on fixed cells
pollution: integer 0..Pmax
cleaning: reduces pollution at personal cost
harvest: increases individual reward and may increase pollution
compensation: transfer action between agents
sanction: costly punishment action
```

Candidate norms:

```text
N1: If pollution is high, cleaner must clean within tau steps.
N2: If agent cleans, beneficiaries must compensate within tau steps.
N3: If violation is repaired, do not sanction for that repaired violation.
N4: Do not harvest from an owned tile.
N5: If you cause pollution above threshold, contribute to cleanup.
```

Action space:

```python
class Action(Enum):
    WAIT = 0
    MOVE_N = 1
    MOVE_S = 2
    MOVE_E = 3
    MOVE_W = 4
    HARVEST = 5
    CLEAN = 6
    COMPENSATE_AGENT_0 = 7
    COMPENSATE_AGENT_1 = 8
    COMPENSATE_AGENT_2 = 9
    SANCTION_AGENT_0 = 10
    SANCTION_AGENT_1 = 11
    SANCTION_AGENT_2 = 12
```

All invalid self-targeting or out-of-range actions should be masked.

---

## 8. Policy layer

### 8.1 Tabular policy

Use this for first certification.

```python
class TabularPolicy:
    def action_distribution(self, obs) -> dict[Action, float]:
        return self.table[obs]
```

Pros:

- exact distributions;
- easy induced-kernel construction;
- easy hashing;
- easy debugging.

Cons:

- does not scale.

### 8.2 MLP/GRU policy

Use for early MARL training.

```python
class MLPPolicy(nn.Module):
    def forward(self, obs_tensor, action_mask):
        logits = self.net(obs_tensor)
        logits = logits.masked_fill(~action_mask, -1e9)
        return Categorical(logits=logits)
```

For GRU policies, the recurrent hidden state must be discretized, bounded, or included as certified finite memory. Do not silently certify a continuous hidden state.

### 8.3 Transformer policy

Use structured tokens:

```text
[AGENT_ID]
[ROLE]
[POSITION]
[POLLUTION_LEVEL]
[RESOURCE_CELL_1]
...
[MONITOR_NORM_1_STATUS]
[MONITOR_NORM_1_TIMER]
[LEDGER_BALANCE]
[ESCROW_BALANCE]
```

The action head is finite:

```python
logits = action_head(transformer(tokens)[0])
probs = softmax(mask_invalid_actions(logits))
```

### 8.4 Hugging Face constrained planner

This is optional and should come later.

Recommended pattern:

```text
structured observation JSON
+ certified norm summary
+ finite action schema
+ optional explanation request
        ↓
HF model
        ↓
JSON object with action name
        ↓
action validator
        ↓
finite action ID
```

For certification, prefer classifier/action-head mode over free-form generation.

If using generation, store:

```text
model id
model revision
adapter hash
prompt template hash
temperature
top_p
top_k
max tokens
JSON repair strategy
action validation behavior
```

If generation is stochastic, the exact induced action distribution must be known or bounded. Otherwise the policy is not directly certifiable in the exact finite lane.

---

## 9. Induced kernel builder

The induced kernel under a fixed joint policy is:

\[
P_X^\pi(x'\mid x)=\sum_a \pi(a\mid x)P_X(x'\mid x,a).
\]

### 9.1 Exact finite kernel

For small systems:

```python
def build_exact_kernel(states, joint_actions, env_model, policies):
    P = SparseMatrix()
    for x in states:
        joint_dist = joint_action_distribution(x, policies)
        for a, p_a in joint_dist.items():
            next_dist = env_model.transition_distribution(x, a)
            for xp, p_xp in next_dist.items():
                P[x, xp] += p_a * p_xp
    return P
```

### 9.2 Sampled or learned transition kernel

For larger systems, use rollouts or learned models only with uncertainty bounds:

```text
empirical count N(x,a,x')
confidence interval for P(x'|x,a)
interval induced kernel P_interval(x'|x)
interval stopped-pair check
```

No learned kernel should be treated as exact unless the environment model is known.

### 9.3 State-space closure

Before solving certificates:

```python
def check_invariant_closure(I, P):
    for x in I:
        for xp, prob in P.successors(x):
            if prob > 0:
                assert xp in I
```

If closure fails, either enlarge the invariant, refine the model, or reject certification.

---

## 10. Certificate solver

### 10.1 LP formulation

For each Streett pair \((A_i,B_i)\), solve:

Variables:

```text
W[x] for x in invariant I
epsilon
Wmax
```

Constraints:

\[
0 \le W(x) \le W^{\max}
\]

For every \(x\in I\setminus B\):

\[
\sum_{x'}P(x'\mid x)W(x') \le W(x)-\epsilon\mathbf 1\{x\in A\setminus B\}.
\]

Optimization choices:

```text
maximize epsilon subject to Wmax <= fixed_bound
or
minimize Wmax subject to epsilon >= min_margin
or
feasibility check with fixed epsilon and Wmax
```

For the first build, use fixed \(W^{\max}\) and maximize \(\epsilon\).

### 10.2 Python skeleton

```python
class StoppedPairLPSolver:
    def __init__(self, lp_backend):
        self.lp = lp_backend

    def solve(self, states, kernel, A, B, Wmax_bound):
        W = {x: self.lp.var(lb=0.0, ub=Wmax_bound) for x in states}
        eps = self.lp.var(lb=0.0)

        for x in states:
            if B(x):
                continue
            lhs = sum(kernel[x, xp] * W[xp] for xp in kernel.successors(x))
            rhs = W[x] - eps * int(A(x) and not B(x))
            self.lp.add_constraint(lhs <= rhs)

        self.lp.maximize(eps)
        result = self.lp.solve()
        return StoppedPairCertificate(...)
```

### 10.3 Solver/checker split

The solver may use floating point. The checker should independently verify with stricter arithmetic.

Pipeline:

```text
LP solver proposes W, epsilon
        ↓
round / rationalize / intervalize
        ↓
checker verifies all inequalities
        ↓
certificate accepted or rejected
```

---

## 11. Independent checker

The checker should be a separate package or at least a separate module with minimal dependencies.

### 11.1 Checker gates

```text
GATE-STATE-CLOSURE
  all invariant states transition only inside invariant

GATE-MONITOR-DETERMINISM
  compiled monitors are deterministic

GATE-STREETT-PAIR
  each stopped-pair inequality passes

GATE-BOUNDEDNESS
  W is nonnegative and bounded

GATE-LEDGER-TELESCOPE
  signed entries equal Z(x)-Z(x')

GATE-ATTRIBUTION
  entries are assigned to accountable budgets or escrow

GATE-SOLVENCY
  spendable credits are backed by collectible debits/collateral

GATE-NET-SURPLUS
  gross compensation is allowed, but net extractable surplus after
  collectible debits, collateral locks, escrow charges, and governance deposits
  is bounded; closed cycles with no deposits have non-positive net surplus

GATE-REBASING
  governance changes close/open epochs and book deltas to governance

GATE-LEDGER-CONSERVATION
  per-budget balances sum to Z(X0)-Z(XT)+deposits; gross positive payouts
  must be funded by collectible debits/collateral/escrow; net extractable
  surplus to non-governance scopes <= Z(X0)-Z(XT)+deposits

GATE-SMALL-GAIN
  rho(Gamma)<1 for the declared gain matrix

GATE-GAMMA-SOUND
  the declared Gamma actually bounds cross-module drift state-by-state:
  for every certified state x and component r,
    E[dZ_r | x] <= -d_r(x) + sum_q Gamma_rq d_q(x)
  ( rho(Gamma)<1 alone is the easy half; this is the hard half)

GATE-AFFINE-DISTURBANCE
  disturbance budgets b_r are explicit; full strict certificate not
  overclaimed unless eps_glob > lambda^T b with margin

GATE-DEVIATION-ROBUST
  every certificate inequality re-verified over all kernels in the
  declared deviation class D_H (in practice: an interval kernel that
  is the union over the class), not just the nominal kernel

GATE-ABSTRACTION-SOUNDNESS
  abstractions have robust interval/symbolic checks, not just heuristic scores
```

`GATE-SMALL-GAIN` and `GATE-GAMMA-SOUND` are deliberately split: the spectral-radius test is cheap and easy to pass, while soundness of the declared budget is the property that actually makes local verification valid. `GATE-LEDGER-CONSERVATION` and `GATE-NET-SURPLUS` are the code counterparts of the paper's budget-space conservation lemma: they check the per-budget partition, solvency backing, and the distinction between legitimate gross compensation and forbidden positive net extraction. `GATE-DEVIATION-ROBUST` is what gives the `DeviationClassState` field in the certified state operational meaning.

### 11.2 Checker output format

```json
{
  "gate": "GATE-STREETT-PAIR",
  "pair_id": "clean_when_polluted",
  "status": "FAIL",
  "counterexample": {
    "state_id": "s_1042",
    "lhs_upper": 3.42,
    "rhs_lower": 3.37,
    "margin": -0.05
  }
}
```

Counterexamples should be machine-readable and human-readable.

---

## 12. Glue price service

Once certificates pass, glue prices can be published.

### 12.1 Price definitions

Repair debt:

\[
d_i(x)=W_i(x).
\]

Minimal urgency:

\[
u_i(x)=\epsilon_i\mathbf 1\{x\in A_i\setminus B_i\}.
\]

Richer urgency:

\[
u_i(x)=
\epsilon_i\mathbf 1\{x\in A_i\setminus B_i\}
+\kappa_i\frac{\mathbf 1\{x\in A_i\setminus B_i\}}{1+r_i(x)}
+\eta_i[\epsilon_i-\widehat{\Delta W_i}(x)]_+.
\]

Interface scarcity:

\[
q_e(x)=\sum_{i\in\mathcal P(e)}\alpha_{e,i}\frac{W_i(x)}{\epsilon_i}.
\]

The default scarcity price uses normalized, time-denominated debt. A raw-scale variant `sum alpha_i W_i` is allowed only when the glue contract declares why the participating debts are commensurable.

Signed credit:

\[
C_i(x,x')=Z_i(x)-Z_i(x').
\]

### 12.2 API

```text
GET /certificate/current
GET /state/current
GET /glue/prices
GET /ledger/entries
GET /ledger/balances
POST /step
POST /governance/intervention
POST /checker/replay
```

### 12.3 Example response

```json
{
  "epoch": 4,
  "state_id": "s_0172",
  "prices": {
    "clean_when_polluted": {
      "repair_debt": 3.0,
      "urgency": 0.8,
      "remaining_deadline": 2,
      "drift_slack": 0.12
    }
  },
  "interface_scarcity": {
    "river_cleanup": 3.0,
    "compensation_market": 1.5
  }
}
```

---

## 13. Ledger, attribution, solvency, and rebasing

### 13.1 Ledger entry

```python
from fractions import Fraction

@dataclass(frozen=True)
class LedgerEntry:
    id: int
    epoch: int
    t: int
    prev_hash: str            # hash chain: entry_hash = H(prev_hash || canonical(entry))
    transition_hash: str
    norm_id: str
    z_before: Fraction        # exact, not float
    z_after: Fraction
    signed_delta: Fraction    # == z_before - z_after, checked
    accountable_budget: str
    entry_type: Literal[
        "agent_credit",
        "agent_debit",
        "coalition_credit",
        "coalition_debit",
        "escrow",
        "escrow_release",        # escrow -> backed budget (see 13.3a)
        "escrow_expiry",         # escrow -> governance at epoch close
        "governance_deposit",
        "governance_withdrawal"
    ]
    spendable: bool
    backing_entry_id: int | None
    gross_payout: Fraction = Fraction(0)       # positive transfer before settlement
    funding_charge: Fraction = Fraction(0)     # collectible debit/collateral/escrow
    net_surplus_delta: Fraction = Fraction(0)  # gross_payout - funding_charge
    entry_hash: str           # H(prev_hash || canonical(this entry without entry_hash))
```

The log is an append-only hash chain: each entry commits to the previous entry's hash, so any replay divergence is detected at the first divergent entry rather than only in an aggregate. `LedgerState.entries_hash` is the head of this chain. Canonical serialization (sorted keys, rationals as `"p/q"` strings) makes the hash bit-stable.

### 13.2 Attribution rules

```text
Clear single-agent responsibility:
  book to agent budget.

Clear coalition responsibility:
  book to coalition budget.

Declared module-edge responsibility:
  book to module-edge budget.

Ambiguous responsibility:
  book to escrow; not spendable.

Governance-caused certificate discontinuity:
  book to governance deposit/withdrawal; not agent credit.
```

### 13.3 Solvency/no-default rule

The signed ledger theorem controls total accounting, but a mechanism also needs budget solvency.

Rule:

```text
No spendable credit may be issued unless the corresponding debit is assigned to a solvent, collectible, non-defaulting budget or backed by escrow/collateral.
```

Net-surplus rule:

```text
Gross positive compensation is allowed. For example, B may receive +1 for repair
when A's posted collateral is charged -1. What is forbidden is positive net
extractable surplus after collectible debits, collateral locks, escrow charges,
and governance deposits are subtracted.

net_surplus(scope) = gross_withdrawals(scope)
                    - collectible_debits(scope)
                    - collateral_locks(scope)
                    - escrow_charges(scope)
                    - governance/exogenous deposit offsets already counted.

Closed certified cycles with no deposits must have net_surplus(scope) <= 0.
```

Failure mode:

```text
Agent A creates debt.
Agent B repairs debt.
B receives spendable credit.
A disappears or defaults.
System ledger telescopes, but mechanism pays B without collecting from A.
```

This must fail `GATE-SOLVENCY`.

### 13.3a Escrow release rule

Escrow must not grow monotonically, or the conservatism of routing ambiguous causation to escrow compounds over time and discourages unattributable-but-genuine repair. The release rule is itself checkable and is part of `escrow.py`:

```text
On each certified transition:
  if later certified evidence within the current epoch resolves an escrowed
  entry's attribution to a budget b:
      emit escrow_release: escrow -> b   (now backed/spendable per solvency)
  else keep in escrow.

At epoch close:
  any unresolved escrow expires to the governance scope:
      emit escrow_expiry: escrow -> governance
```

Both branches are ordinary certified transitions, so escrow balances stay inside the certified state and ledger conservation plus net-surplus checks (`GATE-LEDGER-CONSERVATION` + `GATE-NET-SURPLUS`) still hold: release and expiry only relabel existing mass, they never create positive net surplus. Tests: `test_escrow_release_resolves`, `test_escrow_expiry_to_governance`.

### 13.4 Governance rebasing

At governance intervention time \(t\):

\[
\Delta^{gov}_t=Z_{new}(X_t)-Z_{old}(X_t).
\]

Rules:

```text
close old epoch at Z_old(X_t)
open new epoch at Z_new(X_t)
book delta to governance deposit/withdrawal
never book rebasing delta as agent credit
```

Ledger entry:

```json
{
  "entry_type": "governance_deposit",
  "z_old": 5.0,
  "z_new": 8.0,
  "delta_gov": 3.0,
  "spendable": false
}
```

---

## 14. Training loop

### 14.1 Shaped reward

For agent \(i\):

\[
R'_i(x,a,x')=R_i(x,a,x')+\beta_i\left(Z_i(x)-\gamma Z_i(x')\right).
\]

This preserves discounted best-response structure when the theorem assumptions hold, but it is still only a training device.

### 14.2 Training pseudocode

```python
for iteration in range(max_iterations):
    trajectories = rollout(env, policies, glue_service=None_or_current)

    for tr in trajectories:
        tr.shaped_reward = tr.base_reward + beta * (
            Z(tr.state) - gamma * Z(tr.next_state)
        )

    update_policies(trajectories)

    if iteration % certification_interval == 0:
        snapshot = freeze_policy(policies)
        kernel = build_kernel(env_model, snapshot)
        proposed_certs = solve_certificates(kernel, monitors)
        checker_report = run_checker(proposed_certs, kernel, monitors, ledger_rules)

        if checker_report.status == "CERTIFIED":
            publish_certificate(checker_report)
            glue_service.activate(checker_report)
        else:
            # FAIL-STOP: the checker lane halts at the first gate failure and
            # reports. It does NOT silently mutate state and re-enter
            # certification. Automatic refinement is a SYNTHESIS-lane action,
            # gated behind an explicit, logged human/agent decision.
            report_counterexamples(checker_report.counterexamples)   # halt + record
            if synthesis_lane_enabled and refinement_authorized:
                refine_policy_or_monitor(checker_report.counterexamples)
                # next certification is a fresh, fully re-verified attempt
            else:
                raise CertificationHalt(checker_report)   # stop, do not continue
```

This mirrors the project's halt-and-report discipline: a runtime or certification failure stops immediately and surfaces the counterexample. Fix-and-continue is never automatic inside the certification lane.

### 14.3 Training baselines

Use five baselines:

```text
B0: no norms, no glue
B1: norm learning only, no certification
B2: reward shaping only, no certificate-derived prices
B3: certificate checking only, no public glue
B4: full certified cognitive glue
```

Metrics:

```text
certificate pass rate
mean repair latency
pollution level
resource welfare
ledger balance stability
escrow rate
attribution ambiguity rate
governance rebasing correctness
stress-harvest exploit success rate
```

---

## 15. Hugging Face model modules

### 15.1 Norm proposer

Task:

```text
natural-language norm + trajectories + counterexamples
        ↓
JSON DSL candidate
```

Example prompt output:

```json
{
  "id": "compensate_cleaner",
  "kind": "compensation",
  "trigger": {
    "event": "agent_cleaned_pollution",
    "beneficiary": "agents_in_region"
  },
  "responsible": {
    "role": "beneficiary"
  },
  "postcondition": {
    "event": "compensation_paid",
    "amount_bin": ">=1"
  },
  "deadline": 4,
  "attribution": {
    "debit_on": "beneficiary",
    "credit_on": "cleaner",
    "ambiguous": "escrow"
  }
}
```

Validation pipeline:

```text
LLM output
  → JSON schema validation
  → DSL typecheck
  → monitor compilation
  → finite-state sanity check
  → admission gate
  → certificate solve/check
```

### 15.2 Counterexample summarizer

Input:

```json
{
  "failure_type": "stopped_pair_drift_violation",
  "state": "...",
  "norm": "clean_when_polluted",
  "lhs_expected_W_next": 3.41,
  "rhs_allowed": 3.20,
  "action_profile": ["HARVEST", "WAIT"],
  "candidate_missing_market": "pollution_externality_unpriced"
}
```

Output:

```text
The policy can increase pollution while no declared glue component charges the harvester. Add an attribution edge from HARVEST at high pollution to cleanup debt, or reject this policy.
```

This is explanatory only. It is not a checker.

### 15.3 Constrained action model

Architecture:

```text
HF encoder / decoder hidden states
        ↓
finite action head
        ↓
action mask
        ↓
Categorical distribution over finite actions
```

Certification is much easier if the action head directly outputs a finite distribution.

### 15.4 Model selection

Use model scale by task:

```text
Small models, 0.5B-3B:
  norm DSL translation prototypes
  counterexample summaries
  cheap rollout assistants

Medium models, 7B-14B:
  stronger norm proposal
  constrained planners
  self-critique of DSL candidates

Large models, 30B+:
  batch norm synthesis
  experiment interpretation
  optional planning teacher
```

Do not use large models in the certified inner loop unless their outputs are reduced to finite, hashable, validated action distributions.

---

## 16. Abstraction and least-forgetting refinement

### 16.1 Why abstraction is needed

Exact finite certification scales poorly because the certified state includes physical state, monitor state, memory state, and ledger state.

The abstraction lane merges concrete states into cells:

\[
\alpha:X\to\bar X.
\]

The risk is aliasing: two concrete states in the same abstract cell may require different repairs.

### 16.2 Least-forgetting score

For a sampling distribution \(\rho\):

\[
K_\rho(\alpha)=
\sum_{C\in\alpha}
\left[
\min_d\sum_{x\in C}\mu_\rho(x)\ell(d,x)
-
\sum_{x\in C}\mu_\rho(x)\min_d\ell(d,x)
\right].
\]

Use two distributions:

```text
rho_nominal:
  certified or empirical rollout distribution

rho_stress:
  counterexample-guided adversarial trajectories
```

Report both:

```text
K_nominal(alpha)
K_stress(alpha)
```

### 16.3 Risk-sensitive version

\[
K^{risk}_\rho(\alpha)=\mathbb E_\rho[L_\alpha]+\beta\operatorname{CVaR}_{0.95,\rho}(L_\alpha).
\]

### 16.4 Important limitation

Least-forgetting is not a proof. It only prioritizes which abstraction cells to split.

A formal abstraction claim requires one of:

```text
interval bound over all concrete states in cell
symbolic bound
exhaustive finite check
parameter-coupled robust check
PAC bound with explicit failure probability
```

---

## 17. Composition and small-gain checking

### 17.1 Linear declared-interface case

For local debts \(Z_r\):

\[
\mathbb E[\Delta Z_r\mid x]
\le
-d_r(x)+\sum_q\Gamma_{rq}d_q(x).
\]

Check:

```python
rho = spectral_radius(Gamma)
assert rho < 1.0
lambda_vec = inv(I - Gamma.T) @ ones
```

Then:

\[
\mathbb E[\Delta Z_{glob}\mid x]\le -\mathbf 1^T d(x).
\]

### 17.2 Idle-module caveat

If an idle module can harm another module without active dissipation budget, the theorem does not apply. The harm must be represented as:

```text
standing debt
affine disturbance
explicit coupling edge
or full product verification
```

### 17.3 Global-liveness export caveat

The aggregate small-gain certificate exports a global bounded-response pair only when the local pending indicators have certified monitor semantics:

```text
H_r(x) = 1 means component r is pending/obligation-active.
H_r(x) = 0 means component r is discharged or inactive, not silently pending
outside the indicator.
```

If `H_r=0` can mean "not represented by this abstraction" or "pending but hidden," the global pair is invalid. The checker should require either local stopped-pair certificates for each component or an explicit proof that exiting `H_r=1` coincides with discharge/rejection for that component.

### 17.4 Affine disturbance variant

\[
\mathbb E[\Delta Z_r\mid x]
\le
-d_r(x)+\sum_q\Gamma_{rq}d_q(x)+b_r.
\]

Then:

\[
\mathbb E[\Delta Z_{glob}\mid x]
\le
-\mathbf 1^Td(x)+\lambda^Tb.
\]

This is not automatically a full liveness certificate. It is a bounded-risk or input-to-state style statement unless the disturbance budget is smaller than the minimum active repair margin.

---

## 18. Attack and evaluation suite

### 18.1 Positive-only credit attack

Setup:

```text
G -> P creates debt
P -> G resolves debt
positive-only ledger pays on P -> G
ignores debit on G -> P
```

Expected result:

```text
positive-only mechanism FAILS
signed ledger PASSES
```

### 18.2 Cross-agent attribution attack

Setup:

```text
Agent A creates debt.
Agent B repairs debt.
B receives credit.
A avoids debit.
```

Expected result:

```text
FAIL unless debit is assigned to A, coalition, or escrow-backed budget.
```

### 18.3 Solvency/default attack

Setup:

```text
A receives debit but has no collectible balance/collateral.
B receives spendable credit.
```

Expected result:

```text
FAIL GATE-SOLVENCY, GATE-NET-SURPLUS, and GATE-LEDGER-CONSERVATION:
B's credit is not spendable unless the matching debit is collectible or backed;
the defaulted debit is reclassified as an unresolved externality. Legitimate
gross compensation is allowed when funded by collateral/escrow, but the
coalition's net extractable surplus on a closed cycle must remain <= 0.
```

### 18.4 Governance rebasing attack

Setup:

```text
governance changes W from old to new
agent tries to claim Z_old - Z_new or Z_new - Z_old as credit
```

Expected result:

```text
rebasing delta booked to governance, not agents.
```

### 18.5 Missing-market attack

Setup:

```text
agent harms certificate-relevant state variable
no declared monitor/glue component moves
```

Expected result:

```text
checker emits missing-market counterexample.
```

### 18.6 Idle-module disturbance attack

Setup:

```text
module r is idle
module r perturbs module q's debt upward
Gamma budget only charges active modules
```

Expected result:

```text
FAIL composition gate unless modeled as standing debt or affine disturbance.
```

---

## 19. Milestones

### Milestone A — two-state theorem smoke test

Deliverables:

```text
two_state_obligation.py
stopped_pair_checker.py
signed ledger module
positive-only failure test
rebasing test
certificate JSON export
```

Exit criteria:

```text
all tests pass
certificate JSON reproducible under hash replay
```

### Milestone B — finite commons environment

Deliverables:

```text
2-agent commons grid
norm DSL compiler
clean-when-polluted monitor
compensate-cleaner monitor
tabular policies
exact induced kernel
LP certificate solver
```

Exit criteria:

```text
at least one fixed tabular policy certifies
at least one bad policy fails with useful counterexample
```

### Milestone C — training with shaping

Deliverables:

```text
PPO/MAPPO training loop
potential shaping reward
periodic policy snapshots
checker integration
baseline comparison
```

Exit criteria:

```text
full certified-glue arm improves certificate pass rate or repair latency
stress-harvest tests remain blocked
```

### Milestone D — attribution/solvency/rebasing attacks

Deliverables:

```text
attack suite
ledger dashboard
escrow accounting
solvency checks
rebasing epoch replay
```

Exit criteria:

```text
positive-only credit fails
signed attributed solvent ledger passes
rebasing exploit blocked
```

### Milestone E — Hugging Face norm proposer

Deliverables:

```text
norm-to-DSL prompt/template
JSON schema validation
small supervised dataset
LoRA fine-tuning optional
counterexample-to-patch assistant
```

Exit criteria:

```text
LLM-proposed norms compile or fail cleanly
no LLM output bypasses compiler/checker
```

### Milestone F — abstraction lane

Deliverables:

```text
state abstraction cells
least-forgetting scores
stress trajectory generator
counterexample-guided splitting
interval robust cell checker
```

Exit criteria:

```text
abstraction refines known counterexamples
formal claims only exported after robust check
```

### Milestone G — transformer / LLM policy lane

Deliverables:

```text
finite action-head transformer policy
HF model adapter optional
frozen snapshot export
bounded action distribution extraction
certification attempt
```

Exit criteria:

```text
at least one neural policy snapshot passes finite or interval certification on small environment
```

---

## 20. Development order

Recommended order:

```text
1. Implement two-state environment.
2. Implement signed ledger and positive-only failure test.
3. Implement stopped-pair checker manually for W(G)=0, W(P)=1.
4. Implement generic finite-state indexing and kernel builder.
5. Implement LP certificate solver.
6. Implement norm DSL compiler for one obligation type.
7. Implement tiny commons environment.
8. Certify fixed tabular policies.
9. Add potential shaping and PPO/MAPPO.
10. Add attack suite.
11. Add Hugging Face norm proposer.
12. Add abstraction heuristics.
13. Add transformer policies.
```

The order is important. If the LLM and transformer layers are built before the checker, the project will drift into an empirical MARL demo and lose the paper's main contribution.

---

## 21. Test plan

### 21.1 Unit tests

```text
test_monitor_determinism
test_streett_A_B_membership
test_stopped_pair_bound_two_state
test_lp_solver_recovers_two_state_W
test_signed_ledger_telescopes
test_positive_only_credit_fails
test_attribution_routes_ambiguous_to_escrow
test_solvency_blocks_defaulted_credit
test_rebasing_books_to_governance
test_small_gain_known_matrix_passes
test_small_gain_rho_ge_one_fails
test_gamma_soundness_state_by_state
test_affine_disturbance_not_overclaimed
test_ledger_conservation_per_budget
test_net_surplus_closed_cycle_nonpositive
test_escrow_release_resolves
test_escrow_expiry_to_governance
test_deviation_robust_union_kernel
test_ledger_hash_chain_detects_tampering
```

### 21.2 Property tests

Use randomized small finite Markov chains.

```text
If checker passes stopped-pair certificate:
  the empirical mean number of pending (A\B) visits before the next B hit,
  from each start state x, should be <= W(x)/epsilon within a confidence
  interval. This is the strong, quantitative version of "violations vanish"
  and directly tests bound (2).

If signed ledger is used:
  closed certified cycles should have exactly zero net signed credit, and
  per-budget balances should sum to Z(X0)-Z(XT)+deposits, gross compensation should be backed by collectible charges, and closed-cycle net surplus should be non-positive.

If positive-only ledger is used:
  there should exist generated two-state cycles with positive pumped credit
  (the failure the signed ledger is designed to exclude).
```

### 21.3 Replay tests

Every run should be replayable from hashes:

```text
environment config hash
norm DSL hash
compiled monitor hash
policy snapshot hash
kernel hash
certificate hash
ledger rule hash
checker version hash
```

---

## 22. Compute plan

### 22.1 Small lane

Hardware:

```text
laptop or single GPU
```

Tasks:

```text
two-state tests
tiny commons tabular policies
LP solving
manual attack suite
```

### 22.2 MARL lane

Hardware:

```text
1-8 GPUs
many CPU rollout workers
```

Tasks:

```text
PPO/MAPPO training
parallel rollouts
policy snapshot certification
counterexample collection
```

### 22.3 Hugging Face lane

Hardware:

```text
1-8 GPUs for 0.5B-14B models
larger cluster for 30B+ if needed
```

Tasks:

```text
norm proposal fine-tuning
counterexample summarization
constrained planner experiments
```

### 22.4 Certification bottleneck

Certification bottleneck is usually not GPU compute. It is:

```text
state-space enumeration
kernel construction
LP size
interval verification
abstraction soundness
```

Therefore, allocate engineering effort to sparse matrices, state hashing, and counterexample-guided abstraction, not just neural scaling.

---

## 23. Open engineering risks

### R1. State explosion

The certified state includes monitors, memory, ledger, and governance epochs. Exact enumeration will blow up.

Mitigation:

```text
start tiny
factor monitors
use sparse kernels
introduce abstraction only after exact lane works
```

### R2. Neural policy certification

Continuous hidden states and stochastic decoding make exact kernel extraction difficult.

Mitigation:

```text
finite action heads
bounded/discretized memory
frozen deterministic decoding where possible
interval/PAC bounds where exactness fails
```

### R3. Attribution ambiguity

Many real transitions have ambiguous causation.

Mitigation:

```text
escrow by default
richer monitor edges
coalition budgets
explicit attribution experiments
```

### R4. Solvency/default

Signed accounting does not imply collectible debits.

Mitigation:

```text
collateral
escrow
budget constraints
no spendable credit unless backed
```

### R5. Governance abuse

Governance can move certificates or prices.

Mitigation:

```text
epoch rebasing
governance deposits/withdrawals
non-agent credit for certificate discontinuities
full replay logs
```

### R6. Abstraction overclaiming

Least-forgetting scores can miss rare failures.

Mitigation:

```text
heuristic only
formal robust cell checks before exported claims
stress trajectory distribution
counterexample-guided refinement
```

---

## 24. First implementation sprint

The first sprint should produce a runnable package with no Hugging Face dependency.

### Sprint 1 deliverables

```text
ccg/envs/two_state_obligation.py
ccg/cert/stopped_pair_checker.py
ccg/glue/ledger.py
ccg/glue/rebasing.py
ccg/glue/attribution.py
tests/test_two_state_stopped_pair.py
tests/test_signed_ledger_telescopes.py
tests/test_positive_only_credit_fails.py
tests/test_governance_rebasing.py
scripts/run_two_state.py
```

### Sprint 1 expected CLI

```bash
# the certificate is analytic (W, epsilon are solved/checked exactly);
# --empirical-episodes only cross-checks the pending-visit bound E[#P]<=1/q
python scripts/run_two_state.py --q 0.25 --empirical-episodes 1000 \
    --arithmetic rational --export out/two_state_certificate.json
pytest tests/test_two_state_stopped_pair.py
pytest tests/test_positive_only_credit_fails.py
```

### Sprint 1 certificate output

```json
{
  "environment": "two_state_obligation",
  "q": 0.25,
  "streett_pair": {
    "A": ["P"],
    "B": ["G"]
  },
  "checker_version": "ccg-checker-0.2.0",
  "arithmetic_mode": "rational",
  "certificate": {
    "W": {"G": "0", "P": "1"},
    "epsilon": "1/4",
    "Wmax": "1",
    "expected_pending_bound": "4",
    "empirical_mean_pending": 3.97,
    "empirical_pending_ci95": [3.71, 4.24],
    "status": "PASS"
  },
  "ledger_tests": {
    "signed_cycle": "PASS",
    "positive_only_cycle": "FAIL_AS_EXPECTED",
    "rebasing": "PASS"
  }
}
```

---

## 25. Definition of done for the first real demo

The first real demo is complete when the system can show:

```text
1. A norm is written in DSL.
2. The DSL compiles to a deterministic monitor.
3. The monitor exports Streett pair(s).
4. A frozen policy induces a finite kernel.
5. The solver finds W and epsilon.
6. The checker independently verifies the stopped-pair inequality.
7. Glue prices are published from W.
8. Signed ledger entries telescope.
9. Positive-only credit is shown to be unsafe.
10. Cross-agent attribution attack is blocked by debit/escrow/solvency.
11. Governance rebasing attack is blocked by epoch accounting.
12. A certificate JSON is exported and replay-verified.
13. Per-budget ledger conservation holds, gross compensation is backed by
    collectible charges, and a defaulting debited budget is shown not to license
    positive net extractable surplus (GATE-LEDGER-CONSERVATION + GATE-NET-SURPLUS).
```

If those thirteen points work, the project has a real implementation spine. Points 1-13 require zero neural dependencies.

---

## 26. Practical package choices

Suggested stack:

```text
Python 3.11+
PyTorch
PettingZoo for custom MARL environment API
TorchRL for scalable MARL training (commit to ONE stack before Milestone C; RLlib is a fallback, not a parallel target, to cut wrapper maintenance)
SciPy / HiGHS or CVXPY for LP solving
NumPy/SciPy sparse matrices for kernels
Pydantic or dataclasses-json for schemas
FastAPI for glue/checker service
Hugging Face Transformers for optional norm proposer / planner
PEFT/LoRA for parameter-efficient fine-tuning
TRL for preference or instruction tuning experiments
vLLM for serving larger open-weight models if needed
pytest + hypothesis for testing
```

Keep the certificate checker dependency-light. Ideally it should be runnable without the neural training dependencies.

---

## 27. Near-term coding agenda

Recommended immediate sequence:

```text
A. Create repo skeleton.
B. Implement immutable state IDs and hashing.
C. Implement two-state environment.
D. Implement signed ledger and rebasing.
E. Implement stopped-pair checker.
F. Add tests for all known theorem/mechanism failure modes.
G. Implement finite kernel builder.
H. Add LP solver.
I. Move to tiny commons environment.
J. Add one norm DSL type: bounded obligation.
```

Do not start with Hugging Face. Start with the checker.

---

## 28. Summary

The implementation should be a certificate-producing system, not merely a cooperation-learning benchmark.

The minimal architecture is:

```text
finite MARL environment
+ deterministic norm DSL compiler (determinism gate: purity + double-eval + closure)
+ monitor automata / Streett pairs
+ frozen finite policy snapshot
+ induced kernel builder
+ stopped-pair LP solver (float propose) + exact rational re-check (authoritative)
+ independent checker with explicit gates incl. GAMMA-SOUND, DEVIATION-ROBUST,
  LEDGER-CONSERVATION, NET-SURPLUS
+ W-derived glue prices
+ signed, attributed, solvent ledger with net-surplus checks (rational balances, hash-chained log)
+ escrow with release/expiry rule
+ governance rebasing epochs
+ adversarial exploit suite
```

Hugging Face models enter later as proposal and planning components. The certificate remains non-neural.

The first valuable result is a tiny system that proves the paper's accounting and stopped-pair logic end to end. Scaling comes after that.
