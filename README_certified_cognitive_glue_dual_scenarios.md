# Certified Cognitive Glue

**Verification-first design patterns for auditable multi-agent economies, support practices, and agent-mediated organizations.**

Certified Cognitive Glue (CCG) is a research framework for building multi-agent systems whose cooperation claims are not just empirical. A CCG system turns formal progress certificates into public coordination signals: repair debt, urgency, scarcity prices, signed credit, ledger state, and governance records.

The repository contains the core CCG design plus two scenario design documents:

1. **Seb Krier / Agent Economy Scenario** — an auditable company-transition simulation for AI-agent adoption, tacit knowledge, outsourcing, privacy, displacement, governance, and adversarial organizational attacks.
2. **Peli Grietzer / Eudaimonic Non-Domination Scenario** — a support-practice design for preserving the conditions under which human, ecological, animal, cultural, and intergenerational flourishing can remain participant-governed rather than externally optimized.

The shared idea is:

> Agents may learn, plan, propose norms, and coordinate through public glue prices, but final certification is performed by a deterministic checker over explicit monitors, state, ledgers, contracts, provenance, and governance records.

---

## Status

This repository is currently a **research-design and implementation-blueprint repository**. The documents are mature enough to guide a first implementation, but the codebase is not yet a complete certified MARL system.

The intended build order is:

```text
1. Two-state theorem smoke test.
2. Exact rational checker and signed ledger.
3. Tiny finite organization / task economy.
4. Seb Krier Agent Economy scenario.
5. Adversarial contract / collateral / provenance / governance gates.
6. Peli Grietzer Eudaimonic Non-Domination scenario.
7. Optional neural policy and Hugging Face norm-proposal modules.
```

Do **not** start with large language models. Start with the checker.

---

## Repository documents

Recommended repository layout:

```text
certified-cognitive-glue/
  README.md

  docs/
    core/
      certified_cognitive_glue_paper.md
      implementation_framework.md
      certificate_format.md
      ledger_format.md
      norm_dsl.md
      threat_model.md

    scenarios/
      certified_agent_economy_scenario.md
      eudaimonic_certified_agent_economy.md

    changelogs/
      certified_agent_economy_changelog.md
      eudaimonic_certified_agent_economy_changelog.md
```

### Scenario documents

| Document | Role | Main question |
|---|---|---|
| `docs/scenarios/certified_agent_economy_scenario.md` | Seb Krier / Agent Economy scenario | Can an organization transition to agent-mediated workflows while preserving certified obligations for privacy, auditability, human transition, tacit-knowledge provenance, oversight, and anti-harvest accounting? |
| `docs/scenarios/eudaimonic_certified_agent_economy.md` | Peli Grietzer / Eudaimonic Non-Domination scenario | Can AI systems act as support practices that preserve non-domination, present virtue floors, participant-governed practice capacity, ecological integrity, animal-welfare accountability, and future-facing obligations without optimizing a scalar “flourishing” target? |

---

## Core CCG idea

Given a frozen multi-agent policy, augment the environment state with every variable needed for certification:

```text
CertifiedState = PhysicalState
               + MonitorState
               + AgentControllerMemory
               + NormBeliefTransducerState
               + LedgerState
               + GovernanceEpochState
               + DeviationClassState
               + Contract/Collateral/Provenance State
               + RuntimeIntegrityState
```

The fixed policy induces a Markov chain over certified states. For each admitted obligation, the checker searches for a bounded stopped-pair certificate `W` satisfying:

```text
E[W(next) | state = x] <= W(x) - epsilon * 1{x is pending}
```

before the next discharge state. If the certificate passes, `W` becomes the source of public glue prices:

```text
raw repair debt         = W(x)
time-normalized debt    = W(x) / epsilon
scarcity price          = weighted sum of normalized debts
signed certificate move = Z(x) - Z(next)
```

The raw certificate debt `W` is used for theorem checking and ledger telescoping. The normalized quantity `W/epsilon` is used as a public coordination price with units of expected pending steps.

---

## What the checker certifies

The checker does **not** certify open-ended goodness, full alignment, inner flourishing, or moral legitimacy.

It certifies formal claims of the following kind:

```text
Under this frozen policy,
inside this declared state space,
with these monitors,
with these contracts and provenance records,
under this deviation class,
using this ledger and governance rule,
these obligations are discharged with bounded expected pending time,
and these reward/credit/waiver pathways cannot silently mint net extractable surplus.
```

A CCG certificate is therefore a **public audit artifact**, not a philosophical proof of goodness.

---

## Shared theorem objects

### Stopped-pair certificates

For a Streett pair `(A, B)`, where `A` is the pending/bad region and `B` is the discharge region, the checker verifies a bounded certificate `W` such that for all states outside `B`:

```text
E[W(next) | x] <= W(x) - epsilon * 1{x in A \ B}
```

This proves that the system cannot visit `A` infinitely often while visiting `B` only finitely often.

### Signed ledger

The ledger records signed certificate change:

```text
C(x, next) = Z(x) - Z(next)
```

Positive entries are credits. Negative entries are debits. A positive-only ledger is explicitly unsafe because a debt-create/debt-repair loop can pump rewards.

### Net-surplus conservation

Gross compensation is allowed. For example, one actor may create a debt and another may repair it and receive payment from the first actor's collateral.

The forbidden outcome is positive **net extractable surplus** from cycles, resets, budget default, ambiguous attribution, hidden coalitions, fake contracts, or governance rebasing.

For any accountable coalition `S`:

```text
net_surplus(S) =
    spendable credits received by S
  - collectible debits charged to S
  - collateral locked by S
  - escrow charges assigned to S
  - governance deposits credited to S
```

A closed certified cycle with no exogenous or governance deposit must satisfy:

```text
net_surplus(S) <= 0
```

for every accountable coalition in the certified roster.

### Small-gain composition

Local certificates compose only when all cross-module harms are declared and bounded by a gain matrix `Gamma` satisfying:

```text
rho(Gamma) < 1
```

The checker must verify both:

```text
GATE-SMALL-GAIN      spectral radius condition
GATE-GAMMA-SOUND     state-by-state soundness of declared interface budgets
```

The spectral-radius check alone is not enough.

---

## Scenario 1: Seb Krier / Certified Agent Economy

The Certified Agent Economy scenario models a simulated organization transitioning from human-heavy workflows to agent-mediated or agent-first workflows.

The organization includes:

```text
human workers
AI worker agents
manager agents
compliance / governance agents
agent-first contractors
task queues
knowledge bases
legacy systems
customers
regulators
human transition obligations
```

The central question is:

> Can a firm adopt increasingly capable agents without hiding privacy costs, tacit-knowledge extraction, displacement costs, outsourcing accountability gaps, principal-agent failures, governance waivers, or reward-hacking loops?

### Core organizational obligations

The scenario includes monitors for:

```text
privacy logging
tacit-knowledge resolution
human displacement transition
oversight for sensitive tasks
legacy-routine review
delegation provenance
outsourcing provenance
```

Anti-stress-harvest safety is **not** a priced norm. It is enforced by mechanism gates:

```text
GATE-LEDGER-TELESCOPE
GATE-NET-SURPLUS-CONSERVATION
GATE-SOLVENCY
GATE-REBASING
GATE-COALITION-ROSTER
```

### Agent-economy experiments

Planned experiments include:

```text
augmentation versus replacement
tacit-knowledge bottleneck
legacy firm versus clean-slate agent-first startup
principal-agent friction
offloading to agent-first contractors
human oversight regulation
default / stress-harvest attacks
fake contract / fake collateral attacks
governance waiver floods
```

### Main implementation warning

Revenue, operating cost, and other economic metrics should be logged for dashboards but kept out of the certified kernel unless a monitor actually depends on them. The certified state should include only variables that affect monitors, policies, ledgers, governance epochs, contracts, provenance, or certificate values.

---

## Scenario 2: Peli Grietzer / Eudaimonic Non-Domination

The Eudaimonic Non-Domination scenario extends CCG from organizational automation to support practices.

It explicitly does **not** try to certify “inner flourishing.” That would recreate the scalarization failure the design is meant to avoid.

Instead, it certifies the enabling conditions under which participants can continue pursuing internal practice goods without being dominated by an external optimizer.

The working frame is:

> Certify non-domination, present virtue floors, corrigibility, ecological/animal/future externality visibility, and participant-governed practice-light-cone capacity. Do not certify flourishing itself.

### Practice-light-cone capacity

The positive proxy is a vector, not a scalar:

```text
K_P = (L_P, C_P, R_P, D_P, A_P, E_P, G_P)
```

where:

| Component | Meaning |
|---|---|
| `L_P` | practice light-cone coverage |
| `C_P` | coupling integrity |
| `R_P` | viable repertoire / non-collapse |
| `D_P` | participant self-determination |
| `A_P` | adaptive recovery |
| `E_P` | externality internalization |
| `G_P` | apprenticeship / goal-regeneration |

These dimensions are not optimized into one “flourishing score.” They are floor constraints, recovery obligations, and participant-governed review objects.

### Eudaimonic support gates

The scenario includes gates such as:

```text
GATE-PRESENT-VIRTUE-FLOOR
GATE-NO-DOMINATION-BY-SCALAR
GATE-PARTICIPANT-AUTHORITY
GATE-PRACTICE-LIGHT-CONE-COVERAGE
GATE-COUPLING-INTEGRITY
GATE-REPERTOIRE-NONCOLLAPSE
GATE-GOAL-REVISION-AUTHORITY
GATE-PERTURBATION-RECOVERY
GATE-EXTERNALITY-INTERNALIZATION
GATE-ECOLOGICAL-TRUSTEE
GATE-ANIMAL-WELFARE-TRUSTEE
GATE-FUTURE-OBLIGATION-TRUSTEE
```

### Eudaimonic experiments

Planned experiments include:

```text
future-kindness optimizer attack
support-practice takeover attack
weak predictive optimizer versus strong-coupling support
earth-for-compute / supplier-laundering attack
repertoire collapse attack
participant goal-revision attack
practice-light-cone stress test
ecological and animal-welfare missing-market tests
```

---

## Shared adversarial-resilience layer

Both scenario documents use the same adversarial hardening principle:

> Anything that can discharge a debt, create spendable credit, authorize a waiver, import a sub-certificate, change a practice-capacity floor, change a coalition roster, or alter the deployed policy must itself be a certified, replayable, challengeable object.

Cryptographic security is useful but insufficient. The repository treats security as defense in depth:

```text
cryptographic identity and signatures
contract certificate objects
collateral and solvency registry
evidence provenance and multi-source attestation
challenge / whistleblower / appeal channels
supplier assurance envelopes
runtime and policy integrity checks
coalition-risk detection
governance anti-capture controls
delayed settlement and budget freeze
fail-stop / degraded-mode operation
red-team attack suite
```

### Adversarial checker gates

```text
GATE-CONTRACT-AUTHORITY
  signer has authority for this obligation type.

GATE-CONTRACT-SCOPE
  contract covers the exact obligation being discharged.

GATE-CONTRACT-SOLVENCY
  backing budget/collateral is live, unexpired, and not double-pledged.

GATE-CONTRACT-NONREPLAY
  old contract cannot be reused for a new obligation.

GATE-CONTRACT-REVOCATION
  contract is not revoked at transition time.

GATE-CONTRACT-SUBCERT
  contractor/supplier sub-certificate verifies and composes.

GATE-COLLATERAL-NO-DOUBLE-PLEDGE
  no collateral object backs multiple live obligations beyond its capacity.

GATE-EVIDENCE-PROVENANCE
  discharge evidence has signed provenance and adequate source diversity.

GATE-CHALLENGE-CHANNEL
  participants, workers, auditors, or trustees can challenge false evidence.

GATE-RUNTIME-INTEGRITY
  deployed policy, prompt, adapter, action mask, and checker match certified hashes.

GATE-COALITION-ROSTER
  side-transfer-capable actors are merged or explicitly represented.

GATE-GOVERNANCE-ANTI-CAPTURE
  waivers and rebasing events obey authority, rate-limit, appeal, and budget rules.
```

### Red-team attacks

```text
fake contract
fake collateral
double-pledged collateral
stale contract replay
forged sub-certificate
supplier telemetry fraud
policy snapshot substitution
checker binary substitution
off-ledger coalition
governance waiver flood
proxy decomposition attack
escrow flooding
denial-of-credit attack
missing-market laundering
outsourcing accountability laundering
```

The target is not invulnerability. The target is exploit legibility and containment:

```text
Every economically or ethically relevant exploit must be blocked,
routed to escrow/governance debt,
or surfaced as a checker failure with a replayable counterexample.
```

---

## Planned code layout

```text
certified-cognitive-glue/
  ccg/
    envs/
      two_state_obligation.py
      commons_grid.py
      agent_economy.py
      eudaimonic_support_practice.py

    state/
      schemas.py
      hashing.py
      serialization.py
      finite_index.py
      invariant.py
      coalition_roster.py

    norms/
      dsl.py
      parser.py
      schema.json
      typechecker.py
      compiler.py
      examples/

    monitors/
      automata.py
      streett.py
      timers.py
      monitor_runtime.py

    cert/
      kernel_builder.py
      lp_solver.py
      rational_checker.py
      interval_checker.py
      stopped_pair_checker.py
      small_gain.py
      gamma_soundness.py
      conservation.py
      net_surplus.py

    glue/
      prices.py
      ledger.py
      attribution.py
      escrow.py
      rebasing.py
      solvency.py
      api.py

    adversarial/
      identity.py
      contracts.py
      collateral.py
      provenance.py
      runtime_integrity.py
      supplier_assurance.py
      governance_controls.py
      challenges.py
      coalition_risk.py
      incident_response.py

    scenarios/
      agent_economy/
        env.py
        monitors.py
        tasks.py
        actors.py
        experiments.py

      eudaimonic/
        env.py
        practices.py
        trustees.py
        light_cone.py
        experiments.py

    policies/
      tabular.py
      mlp.py
      gru.py
      transformer_policy.py
      hf_action_head.py
      action_masks.py
      snapshot.py

    train/
      rollout.py
      shaping.py
      ppo.py
      mappo.py
      torchrl_train.py

    attacks/
      positive_only_credit.py
      attribution_collusion.py
      rebasing_attack.py
      fake_contract.py
      fake_collateral.py
      forged_subcertificate.py
      policy_substitution.py
      governance_waiver_flood.py
      proxy_decomposition.py
      missing_market_laundering.py

    services/
      rest_api.py
      dashboard.py
      replay.py
```

---

## Installation

The first implementation lane should have no Hugging Face dependency.

```bash
git clone https://github.com/<your-org>/certified-cognitive-glue.git
cd certified-cognitive-glue
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

Suggested core dependencies:

```text
Python 3.11+
NumPy
SciPy / HiGHS
Pydantic or dataclasses-json
FastAPI
pytest
hypothesis
PyTorch
PettingZoo
TorchRL
```

Optional later dependencies:

```text
Transformers
PEFT / LoRA
TRL
vLLM
```

---

## Quickstart target: two-state theorem smoke test

The first target is a two-state monitor:

```text
G = good / discharged
P = pending
A = {P}
B = {G}
W(G) = 0
W(P) = 1
P -> G with probability q
P -> P with probability 1 - q
```

The certificate margin is:

```text
epsilon = q
```

Expected pending visits before discharge:

```text
E[# P visits before G] <= 1 / q
```

Planned CLI:

```bash
python scripts/run_two_state.py \
  --q 0.25 \
  --empirical-episodes 1000 \
  --arithmetic rational \
  --export out/two_state_certificate.json

pytest tests/test_two_state_stopped_pair.py
pytest tests/test_positive_only_credit_fails.py
pytest tests/test_signed_ledger_telescopes.py
pytest tests/test_governance_rebasing.py
pytest tests/test_net_surplus.py
```

Expected certificate output:

```json
{
  "environment": "two_state_obligation",
  "q": "1/4",
  "streett_pair": {
    "A": ["P"],
    "B": ["G"]
  },
  "arithmetic_mode": "rational",
  "certificate": {
    "W": {"G": "0", "P": "1"},
    "epsilon": "1/4",
    "Wmax": "1",
    "expected_pending_bound": "4",
    "status": "PASS"
  },
  "ledger_tests": {
    "signed_cycle": "PASS",
    "positive_only_cycle": "FAIL_AS_EXPECTED",
    "rebasing": "PASS",
    "net_surplus": "PASS"
  }
}
```

---

## Certificate package

A successful run exports a reproducible JSON certificate:

```json
{
  "environment_hash": "...",
  "policy_hash": "...",
  "monitor_hash": "...",
  "norms": ["privacy_logging", "tacit_resolution"],
  "streett_pairs": ["privacy_logging_pair"],
  "certificates": {
    "privacy_logging_pair": {
      "unit": "raw_W",
      "epsilon": "1/4",
      "W_current": "2",
      "Wmax": "5",
      "status": "PASS"
    }
  },
  "glue_prices": {
    "unit": "normalized_W_over_epsilon_steps",
    "privacy_price": "8"
  },
  "ledger_rules_hash": "...",
  "contract_registry_hash": "...",
  "collateral_registry_hash": "...",
  "evidence_provenance_hash": "...",
  "runtime_integrity_hash": "...",
  "coalition_roster_hash": "...",
  "rebasing_log_hash": "...",
  "deviation_class_hash": "...",
  "checker_status": "CERTIFIED"
}
```

---

## First implementation sprint

Build the non-neural spine first:

```text
A. Create repo skeleton.
B. Implement immutable state IDs and hashing.
C. Implement two-state environment.
D. Implement signed rational ledger and rebasing.
E. Implement stopped-pair checker.
F. Add tests for known theorem/mechanism failure modes.
G. Implement finite kernel builder.
H. Add LP solver.
I. Add contract/collateral/provenance object schemas.
J. Add fake-contract and fake-collateral tests.
K. Move to tiny agent-economy scenario.
L. Add eudaimonic support-practice scenario after the organizational spine works.
```

Definition of done for the first demo:

```text
1. A norm is written in DSL.
2. The DSL compiles to a deterministic monitor.
3. The monitor exports Streett pair(s).
4. A frozen policy induces a finite kernel.
5. The solver finds W and epsilon.
6. The checker independently verifies the stopped-pair inequality.
7. Glue prices are published from W / epsilon.
8. Signed ledger entries telescope.
9. Positive-only credit is shown to be unsafe.
10. Cross-agent attribution attack is blocked by debit/escrow/solvency.
11. Governance rebasing attack is blocked by epoch accounting.
12. A certificate JSON is exported and replay-verified.
13. Per-budget ledger conservation holds.
14. Defaulting debit budgets do not license spendable repair credit.
15. Closed cycles permit gross compensation but no positive net extractable surplus.
16. Fake contracts and fake collateral fail.
17. Runtime policy substitution fails.
18. Governance waiver flooding is visible, budgeted, and rate-limited.
```

---

## What this is not

This is not a system that proves agents are good.

It does not certify arbitrary humans, full moral legitimacy, open-ended flourishing, or behavior outside modeled dynamics. It also does not make cryptography do institutional work that belongs to governance, audits, dispute resolution, coalition modeling, or human challenge channels.

The strongest claim is narrower and more useful:

> Given explicit models, monitors, evidence, authority, contracts, budgets, ledgers, and policies, the checker can make many cooperation failures legible, replayable, and non-silent.

---

## Citation / manuscript

This repository accompanies the manuscript:

```bibtex
@misc{schulz2026certifiedcognitiveglue,
  title  = {Certified Cognitive Glue: Value-Function Prices for Auditable Cooperation in Multi-Agent Systems},
  author = {Benjamin John Schulz},
  year   = {2026},
  note   = {Draft manuscript}
}
```

---

## License

License is not yet specified. Recommended defaults:

```text
MIT or Apache-2.0 for code
CC-BY-4.0 for documentation/paper text
```

Choose before public release.
