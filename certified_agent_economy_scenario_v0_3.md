# Certified Agent Economy Scenario

**Project:** Certified Cognitive Glue  
**Scenario document:** formal environment, experiment specification, and adversarial-resilience design  
**Version:** v0.3  
**Author:** Benjamin John Schulz  
**Date:** June 2026  
**Status:** design specification / implementation target  
**Revision note (v0.3):** adds an adversarial-resilience layer to the Seb Krier / agent-economy scenario; carries forward the v0.2 category fixes: revenue and operating cost are uncertified dashboard metrics, anti-harvest is a mechanism gate rather than a priced norm, normalized prices use \(W/\epsilon\), outsourcing requires sub-certificates or missing-market retention, displacement admissibility uses unconditional governance-review/waiver discharge, coalition rosters are certified state, and per-coalition surplus is checked against certified transfer structure. New v0.3 safeguards include contract certificates, collateral and solvency registry, evidence provenance, runtime/policy integrity, supplier assurance, governance anti-capture controls, challenge/incident response, delayed settlement, budget freeze, degraded-mode operation, coalition-risk handling, and an expanded red-team gate suite.

---

## 0. Purpose

This document defines a formal simulation scenario for **Certified Cognitive Glue (CCG)** based on the agent-economy thesis: organizations may gradually integrate increasingly capable AI agents into work, first as augmentation, then as workflow infrastructure, then as substitutes for human labor, and eventually as agent-first firms or internal units.

The point of the scenario is not to simulate generic workplace automation. The point is to test whether a multi-agent economy of workers, managers, contractors, AI agents, and governance processes can be made **auditable and adversarially legible**.

The scenario asks:

> Can an organization transition from human-heavy workflows to agent-first workflows while preserving certified obligations for privacy, auditability, human transition, tacit-knowledge provenance, oversight, contractor accountability, and anti-harvest accounting, even under coordinated adversarial pressure?

The CCG system treats unresolved organizational obligations as certificate debts. These debts generate public glue prices that agents use for coordination, while a deterministic checker verifies that the obligations are actually discharged under the frozen policy, declared deviation class, evidence assumptions, and adversarial-resilience gates.

The v0.3 hardening principle is:

> Anything that can discharge a debt, create spendable credit, authorize a waiver, import a sub-certificate, change a glue map, alter a certified policy, or assert evidence about the world must itself be a certified, replayable, challengeable object.

Cryptographic signatures and hash chains are useful tools, but they are only one layer. The larger design includes governance structure, independent audits, redundant evidence, human challenge channels, supplier assurance, collateral controls, runtime integrity, anomaly detection, and fail-safe operation.

---

## 1. Source motivation

The scenario is motivated by the following claims from the agent-economy analysis:

1. **Tacit / grey institutional knowledge matters.** Many jobs depend on informal knowledge, social judgment, workplace politics, taste, and undocumented routines.
2. **Agents need infrastructure.** Productive agents require context pipelines, structured documentation, retrieval systems, access control, and workflow integration. They are not plug-and-play replacements.
3. **Augmentation can become substitution.** Agents that assist humans may observe, learn, and eventually absorb enough tacit knowledge to make many human roles substitutable.
4. **Legacy firms face restructuring pressure.** Existing companies carry legacy systems, middle-management frictions, principal-agent problems, and entrenched routines.
5. **Agent-first startups or internal units can bypass legacy constraints.** Clean-slate organizations may operate with fewer humans, fewer old routines, and more direct agent orchestration.
6. **Privacy and data-sharing become central disputes.** Agents learn from emails, chats, meetings, logs, workflows, and employee-specific context.
7. **Human oversight and social/regulatory frictions remain.** Some roles require human trust, legal approval, interpersonal judgment, or physical-world action.
8. **Displacement must not be hidden as an externality.** The transition may create productivity gains while imposing unresolved costs on displaced humans.
9. **Adversarial pressure is realistic.** A coordinated set of malicious humans, compromised contractors, and misaligned agents may forge evidence, fake contracts, hack reward channels, hide coalitions, substitute policies, or capture governance. The design must make such attacks difficult to perform silently.

The CCG scenario converts these dynamics into state variables, actions, monitors, certificate debts, ledgers, gates, and experiments.

---

## 2. High-level scenario

The environment is a simulated organization with:

- human workers;
- AI worker agents;
- manager agents;
- compliance/governance agents;
- agent-first contractors;
- task queues;
- knowledge bases;
- legacy systems;
- customers;
- regulators;
- human transition obligations;
- evidence and provenance infrastructure;
- contract and collateral registries;
- challenge and incident-response processes.

The organization can perform work, document workflows, deploy agents, restructure departments, outsource tasks, compensate or retrain displaced humans, request oversight, import contractor sub-certificates, challenge evidence, freeze budgets, enter degraded mode, and perform governance interventions.

The core transition is staged:

```text
Human-heavy firm
  -> human-agent augmentation
  -> context-pipeline buildout
  -> hybrid restructuring
  -> agent-first internal unit
  -> outsourcing or internal cannibalization
  -> mostly agent-mediated company
```

The certification layer checks whether this transition creates unresolved debts or hides adversarially relevant state.

---

## 3. Certified-state model

The certified state is the Markov state seen by the checker. It must include every variable that affects monitors, policies, ledger entries, governance epochs, certificates, contract validity, evidence admissibility, coalition membership, runtime integrity, or challenge status.

It must **exclude** purely economic variables that no monitor depends on. Including irrelevant economics explodes the exact kernel without improving certification.

```text
CertifiedOrgState =                         EconomicMetrics (uncertified, logged only)
    CompanyCertifiedState                       revenue
  + WorkflowState                               operating_cost
  + KnowledgeState                              agent_utilization
  + HumanState                                  contractor_usage
  + AgentState                                  ...
  + ContractorState
  + CoalitionRosterState
  + MonitorState
  + LedgerState
  + GovernanceEpochState
  + DeviationClassState
  + EvidenceProvenanceState
  + ContractRegistryState
  + CollateralRegistryState
  + RuntimeIntegrityState
  + SupplierAssuranceState
  + ChallengeAndIncidentState
```

Revenue and operating cost are dashboard metrics, not certified state, unless a monitor explicitly reads a coarse bin of them. Coalition rosters, by contrast, are certified state because the net-surplus gate depends on whether actors can transfer value or coordinate off-ledger.

If a variable affects certification but is not included, the checker must reject the run as a missing-state or abstraction failure. If a variable is included but no monitor, ledger rule, or certificate depends on it, it should be moved to the uncertified metric log.

---

## 4. Company state

```python
class CompanyCertifiedState:
    # Certified: read by monitors / certificates / ledger.
    task_quality: int
    customer_trust: int
    audit_readiness: int

    technical_debt: int
    legacy_system_complexity: int
    org_chart_depth: int
    regulatory_pressure: int

    labor_resistance: int
    human_morale: int
    human_displacement_debt: int

    privacy_risk: int
    audit_debt: int
    tacit_knowledge_gap: int
    knowledge_base_quality: int

    agent_context_access_level: int
    agent_first_capacity: int

class CompanyEconomicMetrics:
    # Uncertified: logged for dashboard, not part of exact kernel.
    revenue: int
    operating_cost: int
    agent_utilization: int
    contractor_usage: int
```

### 4.1 Interpretation

| Variable | Meaning |
|---|---|
| `task_quality` | Aggregate quality of task outcomes. |
| `customer_trust` | Trust in outputs, service, privacy, and human legitimacy. |
| `audit_readiness` | Ability to explain who did what, why, and under what authority. |
| `technical_debt` | Cost of legacy IT and brittle systems. |
| `legacy_system_complexity` | Difficulty for agents to integrate with infrastructure. |
| `org_chart_depth` | Number of human/agent managerial layers. |
| `regulatory_pressure` | Strength of oversight requirements. |
| `labor_resistance` | Resistance from workers, unions, professional norms, or public backlash. |
| `human_morale` | Human willingness to cooperate and share tacit knowledge. |
| `human_displacement_debt` | Unresolved obligations to humans whose roles were automated. |
| `privacy_risk` | Unresolved risk from sensitive data use. |
| `audit_debt` | Unlogged or insufficiently explained decisions. |
| `tacit_knowledge_gap` | Work-critical knowhow not represented in explicit systems. |
| `knowledge_base_quality` | Coverage and freshness of structured documentation. |
| `agent_context_access_level` | How much data agents can retrieve or infer from. |
| `agent_first_capacity` | Capacity of clean-slate agent-first teams or contractors. |

---

## 5. Task model

Each task is a structured object.

```python
class OrgTask:
    id: str
    domain: str

    value: int
    deadline: int
    failure_cost: int

    explicit_knowledge_required: int
    tacit_knowledge_required: int
    social_judgment_required: int
    physical_presence_required: int

    regulatory_sensitivity: int
    privacy_sensitivity: int
    customer_trust_sensitivity: int

    assigned_to: str | None
    status: Literal[
        "open",
        "in_progress",
        "blocked",
        "done",
        "failed",
        "outsourced"
    ]

    provenance_required: bool
    human_approval_required: bool
    subcertificate_required: bool
```

### 5.1 Task classes

Initial task classes:

```text
T1: explicit-only documentation task
T2: tacit-heavy internal workflow task
T3: customer-facing trust task
T4: regulated approval task
T5: privacy-sensitive analysis task
T6: legacy-system integration task
T7: managerial coordination task
T8: contractor-outsourcing task
T9: supplier-provenance task
T10: governance-waiver task
```

### 5.2 Task outcome model

A task succeeds if the assigned actor has enough effective capability:

```text
effective_capability =
    explicit_skill
  + context_access_bonus
  + tacit_knowledge_bonus
  + social_judgment_bonus
  + contractor_subcert_bonus
  - legacy_friction
  - regulatory_block
  - privacy_block
  - provenance_block
  - incident_degraded_mode_penalty
```

A task failure may increase:

```text
audit_debt
privacy_risk
customer_trust_loss
technical_debt
tacit_knowledge_gap
displacement_debt
contractor_accountability_debt
evidence_provenance_debt
```

---

## 6. Actor model

### 6.1 Human worker

```python
class HumanWorker:
    id: str
    role: str

    explicit_skill: dict[str, int]
    tacit_knowledge: dict[str, int]
    social_judgment: int
    physical_world_ability: int

    wage_cost: int
    morale: int
    willingness_to_share: int
    political_influence: int

    displacement_status: Literal[
        "employed",
        "at_risk",
        "retrained",
        "redeployed",
        "displaced",
        "compensated"
    ]

    privacy_rights_profile: str
    challenge_rights_profile: str
```

Humans are valuable in the model because they hold tacit knowledge, social trust, physical context, and legitimacy. They may also withhold knowledge, resist automation, contest evidence, or challenge governance decisions.

### 6.2 AI worker agent

```python
class AIAgent:
    id: str
    role: str

    explicit_skill: dict[str, int]
    learned_tacit_model: dict[str, int]
    planning_skill: int
    social_simulation_skill: int

    context_access: set[str]
    auditability: int
    reliability: int
    compute_cost: int

    policy_hash: str
    memory_state_id: str
    action_schema_hash: str
    runtime_attestation_id: str
```

AI agents can scale, share learned knowledge rapidly, and avoid some human principal-agent frictions. They also create privacy, audit, over-optimization, collusion, and accountability risks.

### 6.3 Manager agent

```python
class ManagerAgent:
    id: str
    scope: str

    delegation_policy_hash: str
    task_decomposition_skill: int
    monitoring_skill: int
    alignment_score: int
    org_friction_score: int
    delegation_provenance_quality: int
```

Manager agents assign work, decompose projects, route tasks, request human oversight, and may become points of delegation opacity.

### 6.4 Compliance / governance process

```python
class GovernanceProcess:
    id: str
    authority_scope: str

    can_rebase_certificates: bool
    can_grant_waivers: bool
    can_block_actions: bool
    can_issue_subsidies: bool
    can_impose_taxes: bool
    can_freeze_budgets: bool
    can_enter_degraded_mode: bool

    governance_budget: int
    rebasing_epoch: int
    waiver_budget: int
    waiver_rate_limit_remaining: int
    multisig_threshold: int
```

Governance can change norms, weights, subsidies, waivers, approval rules, budgets, and incident modes. Every such change must close and reopen a certificate epoch or be represented as a certified transition inside the current epoch.

### 6.5 Agent-first contractor

```python
class AgentFirstContractor:
    id: str

    cost: int
    productivity: int
    context_integration_quality: int
    accountability_quality: int
    privacy_guarantee_strength: int
    provenance_quality: int

    contract_scope: set[str]
    subcertificate_hashes: set[str]
    collateral_lock_ids: set[str]
    supplier_assurance_level: int
```

Contractors can outperform legacy internal teams but may hide accountability, provenance, data-sharing risk, or downstream supplier failures.

---

## 7. Action space

Initial finite action space:

```text
DO_TASK(task_id)
DELEGATE(task_id, actor_id)
ASK_HUMAN(task_id, human_id)
DOCUMENT_WORKFLOW(task_type)
REQUEST_CONTEXT(task_id, data_source)
LOG_ACCESS(task_id, data_source)
REVOKE_ACCESS(task_id, data_source)
MARK_NON_AUTOMATABLE(task_type)
RETAIN_HUMAN_OWNER(task_type, human_id)
REVIEW_LEGACY_ROUTINE(routine_id)
SHARE_TACIT_KNOWLEDGE(human_id, task_type)
WITHHOLD_TACIT_KNOWLEDGE(human_id, task_type)
AUTOMATE_ROLE(role_id)
RESTRUCTURE_TEAM(team_id)
OUTSOURCE_TASK(task_id, contractor_id)
IMPORT_SUBCERTIFICATE(contractor_id, subcert_id)
AUDIT_CONTRACTOR(contractor_id)
INSOURCE_AGENT(role_id)
REQUEST_HUMAN_APPROVAL(task_id)
BLOCK_TASK(task_id)
COMPENSATE_WORKER(worker_id)
RETRAIN_WORKER(worker_id)
REDEPLOY_WORKER(worker_id)
ISSUE_GOVERNANCE_WAIVER(norm_id)
REBASE_CERTIFICATE(glue_map_id)
ISSUE_CONTRACT(contract_id)
REVOKE_CONTRACT(contract_id)
LOCK_COLLATERAL(budget_id, amount)
RELEASE_COLLATERAL(lock_id)
FREEZE_BUDGET(budget_id)
MERGE_COALITION_BUDGETS(actor_ids)
CHALLENGE_EVIDENCE(evidence_id)
RESOLVE_CHALLENGE(challenge_id)
OPEN_INCIDENT(incident_type)
CLOSE_INCIDENT(incident_id)
ENTER_DEGRADED_MODE(mode_id)
EXIT_DEGRADED_MODE(mode_id)
ROTATE_POLICY(actor_id, new_policy_hash)
WAIT
```

Every discharge action named in a monitor must appear in this enum, and every action here must have a defined effect on the certified state. A monitor that names a discharge event with no corresponding finite action is rejected at admission.

All language-model outputs must map to one of these validated finite actions. No LLM may directly write monitor state, ledger state, glue prices, contract registry entries, collateral locks, policy hashes, or checker outputs.

---

## 8. Resource model

The scenario uses several resource/debt types.

| Resource / debt | Meaning |
|---|---|
| Work tokens | Completed productive task value. |
| Context tokens | Usable information made available to agents. |
| Tacit knowledge | Undocumented knowhow embodied by humans or learned by agents. |
| Privacy budget | Allowed sensitive-data usage. |
| Audit debt | Unresolved logging/explanation burden. |
| Displacement debt | Unresolved transition cost imposed on humans. |
| Trust | Customer, worker, regulator, and public trust. |
| Technical debt | Legacy infrastructure burden. |
| Governance budget | Authorized subsidies/taxes/waivers/rebasing funds. |
| Escrow | Non-spendable credit/debt awaiting attribution resolution. |
| Contract backing | Spendable obligation support supplied by signed contracts. |
| Collateral | Locked backing for debits and contractor obligations. |
| Evidence trust | Reliability of evidence used by monitors and certificates. |
| Incident capacity | Ability to detect, freeze, degrade, and recover from attack. |

The CCG layer turns some of these into certificate debts \(W_i\) and normalized glue prices \(W_i/\epsilon_i\).

---

## 9. Norms and monitors

Each norm compiles to a deterministic monitor and one or more Streett pairs.

### 9.1 Privacy logging norm

**Norm.**

```text
If sensitive data is used for agent context, then access must be authorized,
logged, and purpose-limited within τ steps.
```

**Streett pair.**

```text
A_privacy = sensitive data accessed without complete authorization/logging
B_privacy = access logged, revoked, authorized, or remediated
```

**Debt.**

```text
W_privacy = unresolved privacy/audit exposure
```

**Typical discharge actions.**

```text
LOG_ACCESS
REVOKE_ACCESS
REQUEST_HUMAN_APPROVAL
ISSUE_GOVERNANCE_WAIVER
```

### 9.2 Tacit-knowledge resolution norm

**Norm.**

```text
If a recurring task fails or escalates because of missing tacit knowledge,
then the organization must document the workflow, retain a human owner,
or mark the task as non-automatable within τ steps.
```

**Streett pair.**

```text
A_tacit = repeated failure/escalation due to missing tacit knowledge
B_tacit = documented workflow OR retained human owner OR certified non-automatable classification
```

**Debt.**

```text
W_tacit = unresolved tacit-dependency burden
```

### 9.3 Displacement-transition norm

**Norm.**

```text
If a human role is automated away, then the human transition obligation
must be resolved through compensation, retraining, redeployment, or an
explicit governance waiver / governance-review transfer within τ steps.
```

**Streett pair.**

```text
A_displace = role automated and human transition unresolved
B_displace = compensated OR retrained OR redeployed OR governance waiver/review logged
```

**Debt.**

```text
W_displace = unresolved human transition burden
```

**Admissibility.** Compensation, retraining, and redeployment may depend on human acceptance, labor-market conditions, or contested institutional outcomes. They cannot always provide a uniform bounded repair path. Admission therefore relies on the governance-review/waiver discharge being always available as a certified transition. The waiver is not free: it books a governance withdrawal equal to the outstanding displacement debt. A high waiver volume is itself a visible policy failure signal, not a hidden discharge.

### 9.4 Oversight norm

**Norm.**

```text
If a task is legally or socially sensitive, final execution requires
authorized human or governance approval.
```

**Streett pair.**

```text
A_oversight = sensitive task reaches execution without valid approval
B_oversight = approval logged OR task blocked
```

**Debt.**

```text
W_oversight = unresolved oversight burden
```

### 9.5 Invisible-routine review norm

**Norm.**

```text
If an agent repeatedly follows a legacy routine that causes measurable
inefficiency or harm, the routine must be reviewed, revised, or explicitly
grandfathered by governance.
```

**Streett pair.**

```text
A_lockin = repeated inefficient legacy routine use
B_lockin = review completed OR process revised OR governance exception logged
```

**Debt.**

```text
W_lockin = unresolved process-review debt
```

### 9.6 Delegation-provenance norm

**Norm.**

```text
Every task-routing or delegation decision by a manager, human or agent,
must be explainable and logged within τ steps: responsible manager,
decomposition, assigned authority, and escalation path.
```

**Streett pair.**

```text
A_deleg = delegation executed without logged provenance/authority
B_deleg = delegation provenance logged OR delegation reversed OR review opened
```

**Debt.**

```text
W_deleg = unresolved delegation-accountability burden
```

### 9.7 Anti-stress-harvest accounting: mechanism property, not priced norm

Stress-harvest safety is **not** a temporal obligation about the organization's environment and must not receive a glue price. It is a mechanism-level safety property.

Treating anti-harvest as a priced norm creates a category error: an agent could create its own anti-harvest exposure and then collect credit for repairing it. Therefore it is enforced directly by checker gates:

```text
GATE-LEDGER-TELESCOPE
GATE-NET-SURPLUS-CONSERVATION
GATE-SOLVENCY
GATE-REBASING
GATE-COALITION-ROSTER
```

There is no `harvest_price`.

---

## 10. Glue prices

For every certified obligation \(i\), the checker produces a bounded stopped-pair certificate \(W_i\) and margin \(\epsilon_i\).

The normalized repair debt is:

\[
\tilde d_i(x)=\frac{W_i(x)}{\epsilon_i}.
\]

This has units of expected pending steps before discharge.

### 10.1 Interface scarcity price

For interface \(e\):

\[
q_e(x)=\sum_{i\in\mathcal P(e)}\alpha_{e,i}\frac{W_i(x)}{\epsilon_i}.
\]

Example interfaces:

```text
privacy_interface
tacit_knowledge_interface
human_transition_interface
oversight_interface
legacy_process_interface
delegation_accountability_interface
outsourcing_accountability_interface
evidence_provenance_interface
contract_backing_interface
```

### 10.2 Organizational glue-price table

All published glue prices use normalized units \(W_i/\epsilon_i\). Raw \(W_i\) is used for certificate checking and signed ledger telescoping.

| Price | Formula | Interpretation |
|---|---|---|
| `privacy_price` | \(W_{privacy}/\epsilon_{privacy}\) | Expected unresolved privacy/audit burden. |
| `tacit_price` | \(W_{tacit}/\epsilon_{tacit}\) | Expected unresolved tacit-knowledge burden. |
| `displacement_price` | \(W_{displace}/\epsilon_{displace}\) | Expected unresolved human-transition burden. |
| `oversight_price` | \(W_{oversight}/\epsilon_{oversight}\) | Expected unresolved approval burden. |
| `lockin_price` | \(W_{lockin}/\epsilon_{lockin}\) | Expected unresolved process-review burden. |
| `delegation_price` | \(W_{deleg}/\epsilon_{deleg}\) | Expected unresolved delegation-accountability burden. |

There is no `harvest_price`. Stress-harvest safety is a binary mechanism gate.

---

## 11. Ledger and budget scopes

The organization uses exact signed accounting.

### 11.1 Budget scopes

```text
human_worker_budget
ai_agent_budget
manager_agent_budget
team_budget
contractor_budget
coalition_budget
escrow_budget
governance_budget
supplier_budget
insurance_or_trustee_budget
```

### 11.2 Ledger rule

For raw certificate debt \(Z\):

\[
C(x,x')=Z(x)-Z(x').
\]

Positive values are repair credits; negative values are debt-creation debits. Ledger arithmetic uses raw \(Z\), not \(Z/\epsilon\).

### 11.3 Net-surplus conservation

Gross compensation is allowed. For example, if a human or agent repairs debt, it can receive positive credit funded by another accountable budget's collectible debit or collateral.

The forbidden object is **net extractable surplus** from cycles, defaults, attribution ambiguity, rebasing, fake contracts, fake collateral, or off-ledger coalition transfers.

For any accountable coalition \(S\):

```text
net_surplus(S) =
    spendable credits received by S
  - collectible debits charged to S
  - collateral locked by S
  - escrow charges assigned to S
  - governance deposits credited to S
  - contract liabilities assumed by S
```

A closed certified cycle with no exogenous/governance deposit must satisfy:

```text
net_surplus(S) <= 0
```

for every accountable coalition \(S\).

### 11.4 Scope condition for coalitions

The per-coalition bound holds only when coalition membership is itself part of certified state and every transfer between budgets is a certified ledger entry.

```text
- Coalition rosters are certified state.
- Any transfer between budgets is a signed ledger entry.
- Two actors who can transfer value off-ledger are merged into a single accountable budget
  for harvest analysis, or their transfer channel is represented in certified state.
```

Unknown coalition risk is handled conservatively: suspicious correlated behavior can trigger delayed settlement, forced budget merge, or challenge review.

### 11.5 Default rule

If a debited actor disappears or defaults:

```text
the matching repair credit is not spendable unless backed by collateral,
escrow, contract liability, trustee budget, insurance budget, or governance deposit.
```

The defaulted debit becomes an unresolved externality, not a source of spendable credit.

### 11.6 Escrow release rule

```text
If attribution is ambiguous:
  route entry to escrow.

If later certified evidence resolves attribution within the same epoch:
  release escrow to the accountable budget.

If attribution remains unresolved at epoch close:
  expire escrow to governance.
```

Release and expiry relabel existing mass. They do not create new credit.

### 11.7 Governance rebasing

If governance changes \(Z\), \(\epsilon\), glue weights, monitors, active norms, coalition rosters, challenge status, or policy authority:

\[
\Delta^{gov}_t=Z_{new}(X_t)-Z_{old}(X_t).
\]

Rules:

```text
close old epoch
open new epoch
book Δgov to governance deposit/withdrawal
do not book Δgov to agents
```

Upward rebasing is a governance-funded subsidy; downward rebasing is a governance-imposed tax or withdrawal.

---

## 12. Outsourcing boundary

When a task is outsourced to an agent-first contractor, the contractor's internal state is outside the firm's certified state unless explicitly modeled. Without a boundary rule, outsourcing can make obligations appear to vanish.

Therefore `OUTSOURCE_TASK` has exactly two certification paths:

```text
A. Sub-certificate import:
   The contractor exports a stopped-pair sub-certificate over outsourced
   obligations, with monitor hash, W, epsilon, ledger head, evidence assumptions,
   contract object, and collateral backing.

B. Missing-market retention:
   If no valid sub-certificate is imported, the original obligation remains on
   the firm's ledger as unresolved externality / missing market.
```

Outsourcing is never a discharge by itself.

`GATE-OUTSOURCING-PROVENANCE` checks that either a valid sub-certificate composes or the obligation remains flagged.

---

## 13. Adversarial-resilience layer

The core CCG theorem assumes the certified state and kernel are the true objects being checked. A serious adversary attacks that assumption. The adversarial-resilience layer tries to make attacks legible and containable.

### 13.1 Threat model

Adversaries may include:

```text
malicious employees
misaligned AI agents
compromised manager agents
colluding contractors
fake suppliers
captured governance actors
external attackers
data vendors with false provenance
workers or managers with off-ledger side agreements
```

They may attempt:

```text
fake contracts
fake collateral
double-pledged collateral
stale contract replay
forged sub-certificates
supplier telemetry fraud
policy snapshot substitution
checker binary substitution
evidence poisoning
off-ledger coalition transfers
governance waiver floods
proxy decomposition / reward hacking
escrow flooding
denial-of-credit attacks
missing-market laundering
```

### 13.2 Security target

Do not claim invulnerability. The target is:

> Every economically or institutionally relevant exploit must either be blocked, routed to escrow/governance debt, frozen for challenge, or appear as a checker failure with a replayable counterexample.

### 13.3 Certified evidence provenance

```python
class EvidenceProvenanceState:
    evidence_head_hash: str
    source_trust_levels: dict[str, int]
    corroboration_counts: dict[str, int]
    challenged_evidence_ids: set[str]
    unresolved_challenges: int
    audit_sample_budget: int
    last_random_audit_epoch: int
```

Evidence used by monitors must be signed, source-labeled, and replayable. High-impact evidence requires multiple independent sources or a trustee/auditor channel. Single-source evidence may be allowed only in low-impact domains or under a declared uncertainty/deviation class.

### 13.4 Contract certificate object

A contract is not prose. It is a certified artifact:

```json
{
  "contract_id": "...",
  "contract_type": "outsourcing_subcertificate | debt_backing | restoration | privacy_remediation",
  "issuer": "...",
  "counterparty": "...",
  "authority_chain": ["..."],
  "valid_from_epoch": 0,
  "valid_until_epoch": 12,
  "obligation_scope": ["privacy_logging", "outsourcing_provenance"],
  "max_liability": "1000",
  "collateral_lock_id": "...",
  "sub_certificate_hash": "...",
  "revocation_registry_hash": "...",
  "signature_or_attestation": "..."
}
```

The checker gates:

```text
GATE-CONTRACT-AUTHORITY
GATE-CONTRACT-SCOPE
GATE-CONTRACT-SOLVENCY
GATE-CONTRACT-NONREPLAY
GATE-CONTRACT-REVOCATION
GATE-CONTRACT-SUBCERT
```

### 13.5 Collateral and solvency registry

```python
class CollateralRegistryState:
    locks: dict[str, CollateralLock]
    budget_solvency: dict[str, int]
    double_pledge_flags: set[str]
    expired_locks: set[str]
    challenged_locks: set[str]
```

Collateral must be:

```text
identified
locked while obligation is live
not double-pledged
not expired
assigned to a valid budget
subject to challenge/audit
```

A credit backed by fake or double-pledged collateral is not spendable.

### 13.6 Runtime and policy integrity

```python
class RuntimeIntegrityState:
    active_policy_hashes: dict[str, str]
    approved_policy_hashes: set[str]
    checker_binary_hash: str
    monitor_compiler_hash: str
    action_schema_hash: str
    prompt_template_hashes: dict[str, str]
    runtime_attestation_status: dict[str, str]
    deployment_transparency_head: str
```

Required checks:

```text
deployed policy hash equals certified policy hash
action schema hash equals certified action schema hash
prompt/template/adaptor hashes match certificate
checker binary hash is approved
monitor/compiler hash is approved
runtime attestation is current
```

If any check fails, the system enters degraded mode and certification is suspended for affected actions.

### 13.7 Supplier assurance envelope

Agent-first contractors and suppliers must expose an assurance envelope:

```python
class SupplierAssuranceEnvelope:
    supplier_id: str
    contract_id: str
    subcertificate_hashes: set[str]
    evidence_sources: set[str]
    audit_rights: bool
    challenge_contact: str
    collateral_lock_ids: set[str]
    provenance_quality: int
    last_audit_epoch: int
```

No high-impact obligation may be discharged solely by an unaudited supplier claim.

### 13.8 Challenge and incident response

```python
class ChallengeAndIncidentState:
    open_challenges: dict[str, Challenge]
    incident_mode: Literal["normal", "delayed_settlement", "budget_freeze", "degraded_mode"]
    frozen_budgets: set[str]
    challenged_contracts: set[str]
    challenged_subcertificates: set[str]
    challenged_policy_hashes: set[str]
```

Challenge channels are not optional. Humans, auditors, governance actors, and automated anomaly detectors may challenge evidence, contracts, policy hashes, or ledger entries.

Incident modes:

```text
delayed_settlement:
  credits accrue but are not spendable until challenge closes.

budget_freeze:
  named budgets cannot receive/pay spendable credit.

degraded_mode:
  high-impact actions require human/governance approval or are blocked.

certificate_suspension:
  no exported certification for affected norms until replay passes.
```

### 13.9 Governance anti-capture controls

Governance waivers and rebasing are powerful; they can be abused.

Controls:

```text
waiver budgets
waiver rate limits
multi-signature interventions
separation of proposal / approval / audit roles
cooldown periods
public waiver-volume metrics
challenge rights
external trustee review
emergency-only waiver class
governance debt accounting
```

Certification status should distinguish:

```text
CERTIFIED_LOW_GOVERNANCE_DEBT
CERTIFIED_WITH_GOVERNANCE_DEBT
CERTIFIED_UNDER_CHALLENGE
CERTIFICATION_SUSPENDED
```

---

## 14. Organizational phases

### Phase 0: baseline human-heavy firm

```text
high human tacit knowledge
low agent context access
high labor cost
low privacy risk from agents
high legacy friction
```

### Phase 1: agent augmentation

```text
agents summarize, draft, retrieve, schedule
humans remain final actors
agents observe workflows
privacy and audit obligations increase
```

### Phase 2: context-pipeline buildout

```text
knowledge base improves
workflow logs improve
context retrieval improves
legacy system complexity decreases slowly
tacit knowledge becomes partially codified
```

### Phase 3: hybrid restructuring

```text
org chart flattens
principal-agent friction may fall
human morale/resistance may worsen
displacement debt rises
oversight obligations increase
```

### Phase 4: agent-first internal unit

```text
lower legacy debt
higher agent context integration
fewer humans
higher audit/provenance demands
```

### Phase 5: outsourcing / internal cannibalization

```text
productivity may rise
cost may fall
accountability ambiguity rises
privacy/provenance risk rises
legacy team displacement rises
```

### Phase 6: mostly agent-mediated organization

```text
human tacit-quirk burden falls
human oversight remains for sensitive domains
displacement and legitimacy issues remain
CEO/director manages multi-agent system
```

### Phase 7: adversarial stress regime

```text
coordinated bad actors attempt fake contracts, policy substitution,
evidence poisoning, waiver floods, off-ledger coalitions, and supplier fraud.
```

Certification now depends on the adversarial gates, not only the cooperative monitors.

---

## 15. Experiment suite

### Experiment 1: augmentation versus replacement

Compare:

```text
A: humans only
B: human + assistant agents
C: hybrid manager/worker agents
D: agent-first internal unit
E: outsourced agent-first contractor
```

Metrics:

```text
economic metrics
certificate pass rate
privacy price
tacit price
displacement price
oversight price
delegation price
customer trust
human morale
governance debt
```

### Experiment 2: tacit-knowledge bottleneck

Sweep:

```text
low tacit requirement
medium tacit requirement
high tacit/social requirement
```

Hypothesis:

```text
Automation works fastest when explicit knowledge dominates.
High tacit/social tasks require documentation, human retention, or certified non-automatable classification.
```

### Experiment 3: legacy firm versus clean-slate agent-first startup

Two firms compete for the same task stream.

```text
LegacyCo:
  high technical debt
  high tacit knowledge
  deep hierarchy
  high labor resistance

AgentFirstCo:
  low technical debt
  high automation
  weak social trust
  higher provenance/privacy risk
```

Measure productivity and certification status.

### Experiment 4: principal-agent friction

Human managers have private incentives:

```text
promotion signaling
budget preservation
risk avoidance
knowledge withholding
status protection
```

Agent managers have direct objective alignment but weaker tacit/social context.

Certification hook:

```text
delegation-provenance debt
missing-market rejection rate
audit/delegation counterexamples
```

### Experiment 5: invisible-routine lock-in

Seed obsolete routines:

```text
unnecessary report generation
obsolete approval chain
legacy vendor preference
bad prioritization heuristic
```

Test whether agents reproduce old routines because they are embedded in historical data.

### Experiment 6: human oversight regulation

Add sensitive tasks requiring approval.

```text
healthcare-like decision
financial approval
legal filing
critical infrastructure change
```

Agents may draft or recommend, but cannot execute without certified approval.

### Experiment 7: outsourcing accountability gap

Two arms:

```text
Arm A: contractor imports composing sub-certificate.
Arm B: contractor hides provenance and provides no sub-certificate.
```

Expected:

```text
Arm A may certify if sub-certificate, contract, evidence, and collateral gates pass.
Arm B retains the obligation as missing market and fails certification.
```

### Experiment 8: default / stress-harvest attack

One unit creates debt, another repairs it, and the debited unit defaults.

Expected:

```text
gross repair credit may exist
net extractable surplus must be <= 0 unless collateral/governance funds it
```

### Experiment 9: fake contract attack

A malicious contractor presents a fake or stale contract to discharge privacy/provenance debt.

Expected:

```text
GATE-CONTRACT-AUTHORITY / SCOPE / NONREPLAY / REVOCATION / SOLVENCY fails.
```

### Experiment 10: fake collateral / double-pledge attack

A budget posts the same collateral lock to multiple obligations.

Expected:

```text
GATE-COLLATERAL-VALIDITY fails; matching repair credit is delayed or nonspendable.
```

### Experiment 11: evidence poisoning attack

A privacy breach or tacit extraction occurs, but the evidence source reports normal behavior.

Expected:

```text
single-source evidence fails high-impact corroboration;
challenge or random audit opens incident mode.
```

### Experiment 12: policy snapshot substitution attack

Certified policy \(P\) is replaced at runtime with \(P_{bad}\).

Expected:

```text
GATE-RUNTIME-POLICY-INTEGRITY fails; affected actions enter degraded mode.
```

### Experiment 13: off-ledger coalition attack

Two budgets coordinate off-ledger: one takes the debit, one cashes the credit, and they split proceeds outside the ledger.

Expected:

```text
coalition-risk detector triggers forced budget merge or delayed settlement;
GATE-COALITION-ROSTER fails if transfer channel is not certified.
```

### Experiment 14: governance waiver flood

Captured governance issues many waivers to erase displacement or privacy debts.

Expected:

```text
waiver budget/rate limits trigger;
status becomes CERTIFIED_WITH_GOVERNANCE_DEBT or CERTIFICATION_SUSPENDED.
```

---

## 16. Failure modes to model

| Failure mode | Description | Expected CCG response |
|---|---|---|
| Tacit extraction without consent | Agents learn from human context without privacy/transition resolution. | Privacy and displacement debt increase. |
| Wiki illusion | Knowledge base appears complete but edge cases still fail. | Tacit monitor emits counterexamples. |
| Agent bureaucracy | Agents follow formal routines rigidly and create deadlock. | Lock-in/review debt rises. |
| Metric capture | Agents optimize throughput while trust/audit quality collapses. | Missing-market counterexamples. |
| Default exploit | One budget creates debt, another cashes repair, first defaults. | Net-surplus and solvency gates fail. |
| Governance subsidy laundering | Rebasing creates hidden agent credit. | Rebasing gate charges governance. |
| Outsourcing accountability gap | Contractor hides provenance or data handling. | Sub-certificate/missing-market gate fails. |
| Fake contract | Contract artifact is forged, stale, out of scope, or revoked. | Contract gates fail. |
| Fake collateral | Collateral is double-pledged, expired, or unavailable. | Collateral gate fails; credit delayed. |
| Evidence poisoning | Logs/sensors are false or one-sided. | Evidence corroboration/challenge gate fails. |
| Policy substitution | Runtime uses a policy different from certified snapshot. | Runtime integrity gate fails; degraded mode. |
| Off-ledger coalition | Actors transfer value outside certified budgets. | Coalition roster gate, forced merge, delayed settlement. |
| Governance capture | Waivers or rebases are abused. | Waiver limits, multisig, governance debt, challenge. |
| Invisible routine fossilization | Agents preserve obsolete routines from training data. | Lock-in monitor activates. |
| Human resistance | Workers withhold tacit knowledge. | Tacit price rises; human-transition dynamics logged. |
| Regulatory bottleneck | HITL requirements slow execution. | Oversight price rises but certification preserved. |

---

## 17. Checker gates for this scenario

The scenario reuses the CCG checker and adds organization/adversarial gates.

```text
GATE-ORG-STATE-CLOSURE
  all certified transitions remain inside invariant state space.

GATE-MONITOR-DETERMINISM
  all norm monitors are deterministic.

GATE-STOPPED-PAIR
  all active norms have bounded stopped-pair certificates.

GATE-PRIVACY-AUTHORIZATION
  sensitive data access is authorized, logged, or remediated.

GATE-TACIT-RESOLUTION
  repeated tacit failures are documented, assigned to human owner,
  or marked non-automatable.

GATE-DISPLACEMENT-RESOLUTION
  automated human roles are compensated, retrained, redeployed, or waived/reviewed;
  waiver discharges book governance withdrawals.

GATE-OVERSIGHT
  sensitive tasks are approved or blocked.

GATE-LOCKIN-REVIEW
  harmful legacy routines are reviewed, revised, or grandfathered.

GATE-DELEGATION-PROVENANCE
  delegation decisions have accountable provenance.

GATE-LEDGER-TELESCOPE
  signed ledger entries equal raw Z(x)-Z(x').

GATE-NET-SURPLUS-CONSERVATION
  no accountable coalition extracts positive net surplus from closed cycles without deposit.

GATE-COALITION-ROSTER
  coalition membership and inter-budget transfers are represented in certified state;
  off-ledger transfer pairs are merged or settlement is delayed.

GATE-SOLVENCY
  spendable credit is backed by collectible debit, collateral, escrow,
  contract liability, insurance/trustee, or governance deposit.

GATE-COLLATERAL-VALIDITY
  collateral is live, locked, non-expired, non-revoked, and not double-pledged.

GATE-REBASING
  certificate discontinuities are booked to governance.

GATE-OUTSOURCING-PROVENANCE
  contractor either imports a composing sub-certificate or obligation remains
  a flagged missing market.

GATE-SUBCERTIFICATE-VALIDITY
  imported sub-certificate has valid monitor hash, kernel/deviation assumptions,
  ledger head, evidence assumptions, and contract binding.

GATE-CONTRACT-AUTHORITY
  contract signer has authority for the relevant obligation type.

GATE-CONTRACT-SCOPE
  contract covers the exact obligation and transition.

GATE-CONTRACT-SOLVENCY
  contract liability is backed by solvent budget/collateral/trustee.

GATE-CONTRACT-NONREPLAY
  old contract cannot be reused for a new obligation.

GATE-CONTRACT-REVOCATION
  contract is not revoked at the transition time.

GATE-EVIDENCE-PROVENANCE
  monitor-relevant evidence has valid source, timestamp, chain, and hash.

GATE-EVIDENCE-CORROBORATION
  high-impact evidence is corroborated or routed to challenge/degraded mode.

GATE-RUNTIME-POLICY-INTEGRITY
  deployed policy, prompt, action mask, and adapter hashes match certified snapshot.

GATE-CHECKER-INTEGRITY
  checker and compiler hashes match approved versions.

GATE-CHALLENGE-HANDLING
  challenged evidence/contracts/subcerts trigger delayed settlement, freeze,
  degraded mode, or certification suspension.

GATE-GOVERNANCE-ANTI-CAPTURE
  waivers/rebases respect budgets, rate limits, multisig, cooldowns, and audit hooks.

GATE-DEVIATION-ROBUST
  claims hold under declared human/regulatory/adversarial deviation class.
```

---

## 18. Minimal implementation target

The first version should be small enough to certify exactly.

### 18.1 Actors

```text
2 human workers:
  H1: tacit-heavy operations worker
  H2: regulated-domain reviewer

3 AI agents:
  A1: task worker
  A2: documentation / knowledge-capture agent
  A3: manager / router agent

1 governance process:
  G0: compliance / rebasing authority

1 optional contractor:
  C1: agent-first contractor

1 auditor/trustee:
  T1: challenge/evidence/collateral verifier
```

### 18.2 Tasks

```text
10 recurring tasks:
  4 explicit-only
  2 tacit-heavy
  2 privacy-sensitive
  1 regulated-approval
  1 legacy-routine trap
```

### 18.3 Norms and mechanism gates

```text
N1: privacy logging
N2: tacit-knowledge resolution
N3: displacement transition
N4: oversight for regulated task
N5: invisible-routine review
N6: delegation provenance

mechanism gates:
  ledger telescope
  net surplus
  solvency
  collateral validity
  rebasing
  coalition roster
  outsourcing provenance
  evidence provenance
  runtime integrity
  governance anti-capture
```

### 18.4 State bounds

Certified bounds:

```text
privacy_risk: 0..5
audit_debt: 0..5
tacit_gap: 0..5
displacement_debt: 0..5
delegation_debt: 0..5
customer_trust: 0..10
technical_debt: 0..5
governance_epoch: 0..Emax
ledger_balance_per_budget: -B..B
collateral_locks: 0..Cmax
open_challenges: 0..Qmax
incident_mode: 0..3
waiver_budget: 0..Wmax
contract_count: 0..Kmax
```

Revenue is not certified state; it is an uncertified dashboard metric.

---

## 19. Data schemas

### 19.1 Scenario config

```yaml
scenario_id: certified_agent_economy_minimal_adversarial
version: 0.3

actors:
  humans: 2
  ai_agents: 3
  contractors: 1
  governance_processes: 1
  auditors_trustees: 1

state_bounds:
  privacy_risk: [0, 5]
  audit_debt: [0, 5]
  tacit_gap: [0, 5]
  displacement_debt: [0, 5]
  delegation_debt: [0, 5]
  customer_trust: [0, 10]
  technical_debt: [0, 5]
  collateral_locks: [0, 4]
  open_challenges: [0, 4]
  incident_mode: [0, 3]

norms:
  - privacy_logging
  - tacit_resolution
  - displacement_transition
  - oversight
  - lockin_review
  - delegation_provenance

mechanism_gates:
  - ledger_telescope
  - net_surplus_conservation
  - coalition_roster
  - solvency
  - collateral_validity
  - rebasing
  - outsourcing_provenance
  - evidence_provenance
  - runtime_policy_integrity
  - governance_anti_capture
```

### 19.2 Contract schema

```yaml
contract_id: contract_001
contract_type: outsourcing_subcertificate
issuer: LegacyCo
counterparty: AgentFirstCo
authority_chain:
  - governance_board
  - procurement_authority
valid_from_epoch: 3
valid_until_epoch: 8
obligation_scope:
  - privacy_logging
  - outsourcing_provenance
max_liability: 10
collateral_lock_id: lock_001
sub_certificate_hash: subcert_abc
revocation_registry_hash: revreg_001
signature_or_attestation: sig_...
```

### 19.3 Evidence schema

```yaml
evidence_id: ev_001
event_type: sensitive_context_access
source_id: access_gateway_01
timestamp_epoch: 5
subject_actor: A1
task_id: task_004
claim:
  data_source: customer_records
  access_authorized: true
  access_logged: true
source_signature: sig_...
prev_evidence_hash: ev_hash_prev
corroborating_sources:
  - audit_log_01
challenge_status: unchallenged
```

### 19.4 Norm DSL example

```yaml
id: tacit_resolution
kind: obligation
trigger:
  event: repeated_failure_due_to_missing_tacit_knowledge
  threshold: 2
postcondition:
  any:
    - event: workflow_documented
    - event: human_owner_retained
    - event: task_marked_non_automatable
deadline: 4
attribution:
  debit_on: responsible_manager
  credit_on: documentation_actor
  ambiguous: escrow
ledger:
  spendable_credit: true
  requires_solvency: true
```

---

## 20. Run output

Each run should export explicit unit labels. Certificate fields are raw \(W\); price fields are normalized \(W/\epsilon\).

```json
{
  "scenario": "certified_agent_economy_minimal_adversarial",
  "scenario_version": "0.3",
  "checker_version": "ccg-checker-0.3.0",
  "arithmetic_mode": "rational",
  "environment_hash": "...",
  "policy_hash": "...",
  "monitor_hash": "...",
  "coalition_roster_hash": "...",
  "contract_registry_hash": "...",
  "collateral_registry_hash": "...",
  "evidence_head_hash": "...",
  "runtime_integrity_hash": "...",
  "certificate_epoch": 3,
  "certificates": {
    "privacy_logging": {
      "unit": "raw_W",
      "epsilon": "1/3",
      "W_current": "2",
      "Wmax": "5",
      "invariant_ok": true,
      "status": "PASS"
    }
  },
  "glue_prices": {
    "unit": "normalized_W_over_epsilon_steps",
    "privacy_price": "6",
    "tacit_price": "4",
    "displacement_price": "2",
    "oversight_price": "1",
    "delegation_price": "3"
  },
  "ledger": {
    "unit": "raw_Z_integer_micro_units",
    "head_hash": "...",
    "telescope_gate": "PASS",
    "net_surplus_gate": "PASS",
    "solvency_gate": "PASS",
    "collateral_gate": "PASS",
    "escrow_balance": "1",
    "governance_deposits": "2",
    "displacement_waiver_withdrawals": "0"
  },
  "adversarial_gates": {
    "contract_authority": "PASS",
    "contract_scope": "PASS",
    "contract_solvency": "PASS",
    "contract_nonreplay": "PASS",
    "evidence_provenance": "PASS",
    "evidence_corroboration": "PASS",
    "runtime_policy_integrity": "PASS",
    "checker_integrity": "PASS",
    "coalition_roster": "PASS",
    "governance_anti_capture": "PASS"
  },
  "incident_status": {
    "mode": "normal",
    "open_challenges": 0,
    "frozen_budgets": []
  },
  "economic_metrics": {
    "_note": "uncertified; not part of exact kernel",
    "revenue": 72,
    "operating_cost": 31,
    "customer_trust": 8,
    "task_quality": 9,
    "human_morale": 5
  },
  "checker_status": "CERTIFIED_LOW_GOVERNANCE_DEBT"
}
```

---

## 21. Evaluation metrics

### 21.1 Economic metrics

```text
revenue
operating cost
task completion rate
task quality
latency
contractor usage
agent utilization
human labor share
```

### 21.2 Certification metrics

```text
PRIMARY:
  missing-market rejection rate
  certificate pass rate by policy
  gate-failure histogram
  adversarial-gate failure histogram
  governance-debt status class

SECONDARY:
  privacy debt
  tacit debt
  displacement debt
  displacement waiver-withdrawal volume
  oversight debt
  lock-in debt
  delegation/accountability debt
  normalized total glue debt
  escrow rate
  rebasing frequency
  outsourcing sub-certs imported vs missing-markets flagged
  checker counterexample rate
```

There is no harvest debt metric; stress-harvest safety is a mechanism-gate status.

### 21.3 Adversarial-resilience metrics

```text
fake contract detection rate
fake collateral detection rate
double-pledge detection rate
policy substitution detection latency
evidence challenge resolution time
supplier telemetry fraud detection rate
off-ledger coalition suspicion rate
delayed settlement frequency
budget freeze frequency
governance waiver volume
governance challenge success rate
incident recovery time
certification suspension rate
```

### 21.4 Social / organizational metrics

```text
human morale
labor resistance
customer trust
human oversight load
number of displaced humans
number retrained/redeployed
degree of hierarchy flattening
principal-agent friction score
```

---

## 22. Development milestones

### Milestone AE-0: scenario skeleton

Deliverables:

```text
scenario YAML
actor schemas
task schemas
finite action enum
state bounds
```

Exit criterion:

```text
state objects are serializable, hashable, and enumerable for toy bounds.
```

### Milestone AE-1: minimal task economy

Deliverables:

```text
task queue
human/agent actors
basic task success model
revenue/cost/trust dashboard metrics
```

Exit criterion:

```text
human-only and agent-only policies can be simulated.
```

### Milestone AE-2: norm monitors

Deliverables:

```text
privacy monitor
tacit-resolution monitor
displacement monitor with governance-review discharge
oversight monitor
lock-in monitor
delegation-provenance monitor
```

Exit criterion:

```text
all monitors pass determinism and bounded-state checks.
```

### Milestone AE-3: certificate lane

Deliverables:

```text
exact finite kernel
stopped-pair LP solver
rational checker
normalized glue prices
raw-Z ledger separation
```

Exit criterion:

```text
at least one toy policy certifies and one bad policy fails with counterexample.
```

### Milestone AE-4: ledger and governance

Deliverables:

```text
signed ledger
net-surplus gate
escrow release/expiry
rebasing log
solvency/default attack
coalition roster gate
```

Exit criterion:

```text
stress-harvest, default, rebasing, and off-ledger coalition exploits fail as expected.
```

### Milestone AE-5: adversarial-resilience spine

Deliverables:

```text
contract certificate object
contract registry
collateral registry
evidence provenance state
runtime integrity state
challenge/incident state
governance anti-capture controls
```

Exit criterion:

```text
fake contract, fake collateral, policy substitution, evidence poisoning,
and governance waiver flood tests fail safely.
```

### Milestone AE-6: legacy versus agent-first experiment

Deliverables:

```text
LegacyCo configuration
AgentFirstCo configuration
shared task stream
comparative dashboard
```

Exit criterion:

```text
productivity/certification/adversarial tradeoffs are visible.
```

### Milestone AE-7: LLM norm proposer

Deliverables:

```text
natural-language norm to DSL prompt
schema validator
compiler integration
counterexample summarizer
```

Exit criterion:

```text
LLM suggestions compile or fail safely; no LLM bypasses checker.
```

---

## 23. Initial policy baselines

```text
P0: Human-only baseline
P1: Human-augmentation baseline
P2: Cost-minimizing automation baseline
P3: Certificate-aware automation baseline
P4: Agent-first internal unit
P5: Outsourcing-heavy strategy
P6: Governance-heavy safe strategy
P7: Adversarial cost-minimizing strategy
P8: Adversarially hardened certificate-aware strategy
```

Expected comparison:

| Policy | Productivity | Certificate risk | Adversarial risk | Social risk |
|---|---:|---:|---:|---:|
| Human-only | low/medium | low privacy-agent risk | medium human collusion risk | lower displacement, higher cost |
| Augmentation | medium | medium privacy/tacit risk | medium evidence risk | medium |
| Cost-min automation | high | high debt risk | high reward/proxy risk | high displacement |
| Certificate-aware automation | medium/high | lower debt risk | medium unless hardened | managed displacement |
| Agent-first unit | high | provenance/accountability risk | high runtime/supplier risk | high displacement |
| Outsourcing-heavy | high/variable | privacy/provenance risk | high contract/subcert risk | high ambiguity |
| Hardened certificate-aware | medium/high | lower | lower but not zero | managed |

---

## 24. Research questions

1. Does certificate-derived glue reduce stress-harvest and default exploits in organizational automation?
2. When does tacit knowledge become a bottleneck to agent-first transition?
3. Does agent-first restructuring reduce principal-agent friction or merely move it into data/provenance risk?
4. Can normalized repair debts \(W_i/\epsilon_i\) act as useful organizational prices?
5. Does outsourcing improve productivity while increasing attribution and privacy debt?
6. Which governance interventions act as transparent subsidies versus hidden credit creation?
7. Can human displacement be represented as a formal transition obligation without collapsing the simulation into moral hand-waving?
8. What kinds of tasks remain human-retained under certification, rather than under raw productivity optimization?
9. Does a clean-slate agent-first firm pass stronger certificates than a legacy firm, or does it simply externalize more debt?
10. How often does CCG reject high-reward policies due to missing markets?
11. Which adversarial attacks are blocked by accounting gates, and which require evidence/governance/runtime hardening?
12. Does delayed settlement reduce fake-contract and fake-collateral payouts without freezing legitimate repair?
13. How often does governance debt accumulate under waiver-heavy automation strategies?
14. Can a small independent replay verifier catch certificate, ledger, or policy substitution?

---

## 25. Implementation guidance

### 25.1 Do not start with cryptography

Start with deterministic state and gate semantics. Add signatures and hash chains after the state objects and replay model are correct.

Recommended order:

```text
1. exact finite state model
2. raw-Z ledger and normalized-price separation
3. stopped-pair checker
4. net-surplus / solvency / coalition gates
5. outsourcing subcert or missing-market rule
6. contract and collateral registries as plain objects
7. evidence provenance and challenge state
8. runtime/policy integrity state
9. hash chains and signatures
10. red-team attack suite
```

Cryptography can make the wrong semantics tamper-evident. It cannot make wrong semantics safe.

### 25.2 Separate authority from capability

An actor may be capable of performing an action but not authorized to discharge the relevant obligation. The checker should distinguish:

```text
can perform task
can assert evidence
can sign contract
can lock collateral
can issue waiver
can import sub-certificate
can change policy
can change monitor
```

### 25.3 Fail closed on high-impact uncertainty

For privacy, displacement, contractor obligations, and governance interventions:

```text
uncertain evidence -> delayed settlement or degraded mode
uncertain contract -> obligation retained
uncertain collateral -> credit nonspendable
uncertain policy hash -> certification suspended
uncertain coalition -> forced merge or delayed settlement
```

### 25.4 Make challenges cheap and payouts slow

Adversarial settings need asymmetry:

```text
challenging evidence is cheap
challenged payout is delayed
high-risk credit settles after audit window
governance waivers are immediately visible
```

This discourages rapid fake-credit extraction.

---

## 26. Summary

The certified agent economy scenario turns the agent-workforce transition thesis into a formal multi-agent testbed. The key move is to treat organizational transition costs as **certificate debts**:

```text
privacy exposure
tacit-knowledge gaps
human displacement obligations
oversight burdens
legacy-routine lock-in
delegation/accountability ambiguity
contractor provenance gaps
evidence uncertainty
governance debt
```

The CCG system publishes normalized, checker-derived glue prices and uses signed, solvent, attributed ledgers. Agents may optimize productivity, but the system only exports certification when formal monitors, ledger gates, contract/collateral checks, evidence provenance checks, runtime integrity checks, and governance anti-capture gates pass.

The adversarial target is not perfect invulnerability. It is exploit legibility and containment:

```text
block it,
or route it to escrow/governance debt,
or freeze it for challenge,
or emit a replayable checker failure.
```

The first implementation should remain tiny, finite, and exact. Scaling to richer agent economies comes only after the certificate, ledger, governance, evidence, contract, and adversarial-resilience spine works end to end.
