# Kang Enterprise Process Reviewer

English | [简体中文](README.zh-CN.md)

[![Release](https://img.shields.io/github/v/release/KanG-ciyuan/kang-enterprise-process-reviewer?display_name=tag&sort=semver&style=flat-square)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Last commit](https://img.shields.io/github/last-commit/KanG-ciyuan/kang-enterprise-process-reviewer?style=flat-square)](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer/commits/main)

**Review whether enterprise workflows are actually executable, accountable, recoverable, and safe for AI participation.**

A read-only review Skill for business and operational process design across SaaS, internal tools, services, and AI-assisted processes. Give it a proposed process and it returns a review of responsibility, evidence, authorization, handoff, exceptions, escalation, and human decision points — without changing the product it reviews.

The behavioural content is one Markdown prompt contract, [`SKILL.md`](SKILL.md) (45 lines), plus a 46-line rubric at [`references/process-rubric.md`](references/process-rubric.md). There is no runtime code and there are no scripts.

**Status: public release candidate.** `manifest.json` declares `"status":"public-release-candidate"`, and `reports/creation-handoff.md` describes v0.2.0 as "a public release candidate pending release evidence". Tag and GitHub release `v0.2.0` both exist and point at the current HEAD. `manifest.json` additionally carries `"maturity_tier":"production"`, which conflicts with the candidate wording and with the missing output-evidence file; this README uses the candidate wording. See [Validation Evidence](#validation-evidence).

## Why Flowcharts Are Not Enough

> A flowchart shows where work goes. A governed process explains who is responsible, what evidence authorizes it, how exceptions recover, and when AI must stop.

A diagram is a map of intended movement. It does not say who owns a decision, what has to be true before an action is legitimate, who accepts the handoff, or what happens when the happy path fails. Those are exactly the gaps that surface later as self-approval, silent failure, orphaned exceptions, and automation nobody can reverse.

The review therefore refuses to treat any single source as the whole truth. From [`references/process-rubric.md`](references/process-rubric.md):

> Interviews describe experience; logs describe recorded behavior; policies describe intended rules. None alone proves the complete process. Preserve conflicts instead of averaging them away.

| What a flowchart answers | What it usually leaves open | Where this review looks |
| --- | --- | --- |
| Order of steps | Who is accountable for each step | actor and accountable owner per critical node |
| Branching on a decision | What authorizes the decision, and who may not approve it | authorization rules and segregation of duties |
| The happy path | Failure, timeout, retry, and recovery behavior | failure, recovery, timeout, escalation per node |
| End state | Whether the receiver accepted the work, and whether it is auditable | receiver acceptance and audit records |

## Simple Flow vs Governed Process

| Concern | Simple flow | Governed process |
| --- | --- | --- |
| Node definition | A box with a label | A stated actor, trigger, input, rule, action, output, receiver, evidence, authorization, failure, recovery, timeout, escalation, and audit record |
| Approval | "Manager approves" | Approver, decision object, evidence seen, allowed outcomes, and audit record are all identified |
| Evidence | Assumed | Labeled `confirmed`, `inferred`, or `to_verify`, with its source named |
| Failure | Not drawn | Failure mode, recovery path, timeout, and escalation deadline per critical node |
| Completion | The process ends | Receiver readiness, handoff acceptance, and observable completion |
| AI participation | Unspecified | Split into deterministic rule, bounded Agent work, and human judgment — with a hard stop |

## What Gets Reviewed

Seven dimensions are genuinely defined by this repo. Each is backed by explicit text in [`SKILL.md`](SKILL.md) and [`references/process-rubric.md`](references/process-rubric.md):

| Dimension | What the repo defines | Source |
| --- | --- | --- |
| Responsibility | Known actors, an accountable owner per critical node, and an actor responsibility map in the output | `SKILL.md:15`, `:31`, `:35`; `references/process-rubric.md:46` |
| Evidence | Evidence labels, provenance and sufficiency, an evidence register, and an `evidence` field per node | `references/process-rubric.md:5-7`, `:29`; `SKILL.md:21`, `:31` |
| Authorization | An `authorization` field per node, authorization and least privilege as a review test, and segregation of request, approval, and audit duties | `references/process-rubric.md:26-27`; `SKILL.md:31` |
| Handoff | A `receiver` field per node, receiver readiness and handoff acceptance as a review test, and a downstream handoff in the output | `references/process-rubric.md:34`; `SKILL.md:31`, `:35` |
| Exception | Exception ownership as a review test, and exception and escalation paths as an output element | `references/process-rubric.md:33`; `SKILL.md:35` |
| Escalation | An `escalation` field per node, an escalation deadline, and the stop-and-escalate rule | `SKILL.md:31`, `:39-41`; `references/process-rubric.md:33` |
| Human Decision Gate | Human judgment as a boundary category, the human-approval record requirement, and readiness blocked by an unresolved human decision gate | `references/process-rubric.md:21`, `:23`, `:46` |

**Not a dimension: State.** The repo contains no state section, no state-ownership rule, and no state-transition definition. The only state-related text is `stale state` inside one review-test bullet (`references/process-rubric.md:32`) and `stale` inside the `to_verify` evidence label (`references/process-rubric.md:7`). Stale state, concurrency, and duplicated work can be raised as review concerns; this is not state-machine or lifecycle coverage, and the repo does not claim to be one.

<details>
<summary>The nine review tests and the seven-step method</summary>

Review tests (`references/process-rubric.md:27-35`): authorization and least privilege; segregation of request, approval, and audit duties; provenance and sufficiency of evidence; sensitive-data minimization and retention; reversibility and recovery; duplicates, timeouts, retries, stale state, and concurrency; exception ownership and escalation deadline; receiver readiness and handoff acceptance; observable completion and auditability.

Method (`SKILL.md:21-27`):

1. Freeze scope and build an evidence register.
2. Model every material node as `actor -> trigger -> input -> rule -> action -> output -> receiver`.
3. Add failure, recovery, timeout, authorization, escalation, and audit behavior to each critical node.
4. Separate deterministic rules, bounded Agent or model work, and human judgment.
5. Test contradictions, missing owners, self-approval, fabricated evidence, silent failure, and irreversible automation.
6. Apply the process rubric, then propose the smallest credible workflow.
7. Return blockers and human decisions before any implementation handoff.

Step 2 is a modelling shorthand for the node chain, not the full node contract described below. The contract is the 14-field list at `SKILL.md:31` and `references/process-rubric.md:13`.

</details>

## Node Contract

The repo's strongest concept. Each critical node must identify **14 fields** (`SKILL.md:29-31`, restated at `references/process-rubric.md:11-13`):

`actor` · `trigger` · `input` · `rule` · `action` · `output` · `receiver` · `evidence` · `authorization` · `failure` · `recovery` · `timeout` · `escalation` · `audit_record`

The rubric adds the completeness rule:

> The process is incomplete if a field is materially relevant but has no owner or explicit `not_applicable` rationale.

| Group | Fields | Question it forces |
| --- | --- | --- |
| Flow | `actor`, `trigger`, `input`, `rule`, `action`, `output`, `receiver` | Who acts, on what signal, under which rule, and who receives the result? |
| Legitimacy | `evidence`, `authorization` | What supports the action, and what permits it? |
| Durability | `failure`, `recovery`, `timeout`, `escalation` | What happens when it breaks, and who is called? |
| Accountability | `audit_record` | What is left behind that a later reviewer can check? |

**Limits of this claim — read before relying on it.** The node contract is a **prose field list only**. The repository ships no schema file, no validator, no example node-contract instance, and no test that inspects any produced node. Nothing enforces the 14 fields; they are an instruction to a model. The repo is also internally inconsistent about them: the output fixture at [`evals/output_cases.json`](evals/output_cases.json) states that a node contains only **8** fields, silently dropping `action`, `evidence`, `authorization`, `recovery`, `timeout`, and `audit_record` (`evals/output_cases.json:8`). Treat the 14-field list in `SKILL.md` and the rubric as the contract, and the fixture as a weaker restatement.

## Evidence & Authorization

Evidence is labeled, not asserted (`references/process-rubric.md:5-7`):

| Label | Definition |
| --- | --- |
| `confirmed` | Supported by an authoritative policy, direct observation, reliable system record, or named accountable owner. |
| `inferred` | Supported by circumstantial or incomplete evidence and safe only as a provisional model. |
| `to_verify` | Unsupported, conflicting, stale, demo-generated, or consequential enough to require direct confirmation. |

Rules that shape the review: demo data, model output, and upstream reports are not confirmed business truth (`SKILL.md:11`); a bounded review is required when material is missing, with unsupported claims marked `to_verify` (`SKILL.md:17`); contradictions are preserved rather than averaged away (`references/process-rubric.md:9`); and the review tests explicitly cover provenance, sufficiency, sensitive-data minimization, and retention (`references/process-rubric.md:28-30`).

Authorization is checked as least privilege, as segregation of request / approval / audit duties, and as an explicit `authorization` field on every critical node (`references/process-rubric.md:26-27`; `SKILL.md:31`). The output must include authorization and data controls (`SKILL.md:35`).

Readiness — the repo's actual gate concept — is defined once, at `references/process-rubric.md:44-46`:

> A process is implementation-ready only when critical nodes have accountable actors, evidence and authorization rules, receiver acceptance, exception paths, and audit records. Any unresolved blocker, sensitive-data authority, or human decision gate prevents readiness.

**Naming caveat.** An earlier version of this README and `agents/interface.yaml` referred to an "Evidence Gate". That term is defined nowhere in [`SKILL.md`](SKILL.md) or [`references/process-rubric.md`](references/process-rubric.md). What does exist is the evidence labels above and the readiness gate quoted here. This README uses those names.

## Rule / Agent / Human Boundary

The decision split is defined once, as three categories (`references/process-rubric.md:19-21`):

| Category | Use when |
| --- | --- |
| Deterministic rule | Inputs, outcomes, and exceptions are explicit and testable. |
| Bounded Agent/model work | Extraction, classification, comparison, summarization, or recommendation where uncertainty is visible and reversible. |
| Human judgment | Policy exceptions, consequential approvals, ambiguous business truth, sensitive access, or irreversible action. |

The Agent-side boundary is explicit (`references/process-rubric.md:23`):

> An Agent may recommend but must not silently approve, certify truth, grant access, or suppress an exception. Human approval must identify the approver, decision object, evidence seen, allowed outcomes, and audit record.

## Exception & Escalation

Exceptions are reviewed as owned paths, not as surprises. Each critical node carries `failure`, `recovery`, `timeout`, and `escalation`, and the review tests target duplicates, timeouts, retries, stale state, concurrency, exception ownership, and escalation deadlines (`references/process-rubric.md:32-33`). Exception and escalation paths are a required output element (`SKILL.md:35`).

Escalation terminates in a human. The stop rule (`SKILL.md:39-41`):

> Stop when authority, sensitive-data use, approval ownership, or an irreversible decision is unresolved. Escalate policy choices and business truth to the named human owner. Never allow an Agent to make a consequential decision merely because the evidence is inconvenient to obtain.

Severity is a defined vocabulary of four levels (`references/process-rubric.md:39-42`): `blocker` (unsafe authority, missing consequential decision owner, fabricated truth, or unrecoverable critical failure), `high` (a critical node cannot complete, hand off, or recover reliably), `medium` (the process works only through undocumented manual knowledge, or creates significant delay/error risk), `low` (a local clarity or efficiency issue with bounded impact).

## Outputs

The review returns eleven elements (`SKILL.md:35`): scope and evidence register; actor responsibility map; node table; rule/Agent/human decision split; authorization and data controls; exception and escalation paths; contradictions; minimal viable workflow; findings; open decisions; downstream handoff.

Every finding must carry ten fields (`SKILL.md:37`): `id`, `severity`, `evidence_status`, `source`, `node`, `failure_mode`, `business_impact`, `required_change`, `owner`, `verification`. Every critical node must answer who acts, what authorizes or supports the action, what is handed off, and what happens when it fails.

What ships is the **declared** contract, not a toolchain: no template, no schema file, no example output, and no runner that parses a produced review. The output contract is prose. See [Validation Evidence](#validation-evidence).

## Validation Evidence

| Item | Status | Evidence |
| --- | --- | --- |
| Version `0.2.0` consistency | `VERIFIED` | `SKILL.md:6`, `manifest.json`, `reports/skill-ir.json:6`, [`tests/test_contract.py`](tests/test_contract.py), git tag `v0.2.0` = HEAD `39851b9`, GitHub release `v0.2.0`. No version mismatch. |
| Package contract tests | `VERIFIED` as run | `python3 -m unittest discover -s tests -v` → `Ran 3 tests in 0.001s` / `OK`. |
| Tests verify review behaviour | **No** | The 3 tests assert 2 file existences, 9 substrings in Markdown headings, 1 legacy-name negative guard, and 3 fixture count thresholds. They do not execute the skill, call a model, or parse any output. |
| Trigger fixtures | `VERIFIED` as recorded fixtures | 11 hand-authored Chinese cases (5 `should_trigger`, 3 `should_not_trigger`, 3 `near_neighbor`) in [`evals/trigger_cases.json`](evals/trigger_cases.json); [`reports/trigger-eval.json`](reports/trigger-eval.json) reproduces identically at 11/11 (`pass_rate` 1.0). |
| Trigger evaluation method | Keyword scoring | The scorer counts concept-keyword substrings drawn from the skill description against each fixture string, with a `0.3` threshold. **No model is invoked and no router is tested.** The runner lives in a different repository (`kang-meta-skill`) and is not shipped here, so the report cannot be regenerated from this repo alone. |
| Output contract evaluation | `TO_VERIFY` — missing evidence | [`evals/output_cases.json`](evals/output_cases.json) has one case with English prose assertions and **no runner anywhere**. `manifest.json` declares a release gate named "output contract evaluation"; no script or report for it exists. `reports/output-evidence.json` **does not exist**, and the author's own release check flags `provider_or_human_output_evidence: missing_evidence: true`. |
| Declared test runner | Not shipped | `pytest` is not installed for any `python3` on the audit machine and is not declared in any dependency file. The suite runs under the stdlib `unittest` runner only. |
| Continuous integration | **None** | No `.github/`, no workflow file, no `Makefile`, no `tox.ini`. The 3 tests run only when a human types the command. |
| Package validators | `HISTORICAL` — author environment | `quick_validate.py` and `validate_skill.py` pass when run against a local checkout, but both live in the separate `kang-meta-skill` repository and are not part of this package. |
| Secrets and client data | `VERIFIED` clean | No credentials, no `.env`, no email addresses. A whole-history scan (all 10 commits, all blobs) for company or client identifiers returned zero hits. `CRM` appears only inside a synthetic fixture string. The repo contains no client data and no interview transcripts. |
| GitHub release notes | Partly unsupported | The `v0.2.0` release body claims "output schemas" and "cross-domain evaluations". No file in this repo contains a schema, and the only evaluation with a report is the keyword trigger scorer. Do not rely on those two phrases; the release has no assets. |

<details>
<summary>Claims removed from the previous version of this README</summary>

| Removed | Why |
| --- | --- |
| `status: public release` badge | Contradicted by `manifest.json` (`public-release-candidate`) and `reports/creation-handoff.md` ("pending release evidence"). Status wording now follows the repo's own declared candidate status. |
| Static `contract tests-3` badge | A hand-written count that goes stale and, sitting beside release badges, implied assurance the tests do not provide. Replaced by the release, license, and last-commit badges. |
| "Evidence Gate" / 证据门 as a defined mechanism | The term appears in the previous README and `agents/interface.yaml`, but is defined nowhere in `SKILL.md` or the rubric. The real concepts are the evidence labels and the readiness gate. |
| "digital employee" / 数字员工 framing | Not present in `SKILL.md`, which defines a read-only workflow assurance role. That framing belongs to a different project (`kang-agent-workforce`), not to this one. |
| Author-local absolute paths in the verification commands | They published a private directory layout and do not work for any other reader. Replaced with a repo-relative command that actually runs. |

</details>

## Example

No example output ships in this repository — there is no `examples/`, `templates/`, or `schemas/` directory. The single output fixture ([`evals/output_cases.json`](evals/output_cases.json)) is a synthetic prompt about a procurement approval process with prose assertions, not a reference answer.

The table below illustrates the 14-field node contract and was written for this README. It is not a shipped artifact, and no tool in this repository produced or validated it.

| Field | Illustrative content for one node |
| --- | --- |
| `actor` | Procurement requester |
| `trigger` | Purchase request submitted above the standing threshold |
| `input` | Request record, budget line, vendor reference, prior quotes |
| `rule` | Threshold policy plus category eligibility rules |
| `action` | Route the request to a named budget owner for approval |
| `output` | Approval decision with conditions |
| `receiver` | Requester and the procurement operations queue |
| `evidence` | Policy version, request record, comparison of quotes |
| `authorization` | The budget owner holds the delegation for this cost center; the requester cannot approve their own request |
| `failure` | Approver unavailable, or the budget line is exhausted mid-review |
| `recovery` | Delegate approval with a recorded delegation, or return the request for revision |
| `timeout` | Decision window after which the request escalates automatically |
| `escalation` | Contested or policy-exception cases go to the finance owner named in the policy |
| `audit_record` | Request id, approver identity, decision, evidence seen, timestamp |

The fixture's own assertion for a case like this covers only 8 of these 14 fields, which is the inconsistency described under [Node Contract](#node-contract).

## Use / Not Use

**Use it for**

- Reviewing a proposed business or operational process before implementation.
- Checking whether actors, triggers, inputs, rules, evidence, handoff, exceptions, authorization, escalation, and human decision gates are actually defined.
- Locating self-approval, missing owners, fabricated evidence, silent failure, and irreversible automation.
- Deciding where AI may participate and where a human decision gate must block it.

**Do not use it for** (`SKILL.md:3`)

- Product navigation design or visual UX critique.
- Implementation of the process or the product.
- Raw employee interviewing.
- Treating demo data, model output, or an upstream report as confirmed business truth (`SKILL.md:11`).

If the objective or process boundary is absent, the review stops rather than proceeding on assumption (`SKILL.md:17`).

## Safety / Human Boundary

- The role is **read-only**: it writes only the assigned process artifact and does not modify the product (`SKILL.md:45`). `manifest.json` declares `"permissions":{"default":"read-only","implementation":"requires human approval"}`.
- The review **stops** on unresolved authority, sensitive-data use, approval ownership, or an irreversible decision, and escalates to the named human owner (`SKILL.md:41`).
- An Agent may recommend but must not silently approve, certify truth, grant access, or suppress an exception (`references/process-rubric.md:23`).
- An unresolved blocker, sensitive-data authority question, or human decision gate prevents readiness (`references/process-rubric.md:46`).

**These are instructions to a model, not enforced controls.** Nothing in this repository — no linter, no output validator, no schema, no test — can prevent a model from ignoring the stop-and-escalate rule. The declared read-only permission has no implementing or auditing mechanism here.

## Quick Start

Prerequisites (`SKILL.md:15`, `:17`): the process objective, scope, known actors, trigger and intended outcome, authoritative policies or constraints, and available evidence. Architecture, the current workflow, system logs, forms, interviews, and exception records are supporting inputs.

```bash
git clone https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer.git
cd kang-enterprise-process-reviewer
python3 -m unittest discover -s tests -v
```

The last command is the package contract check described in [Validation Evidence](#validation-evidence) — it verifies packaging identity and heading presence, not review behaviour.

The entry point is [`SKILL.md`](SKILL.md) (`manifest.json` `"entrypoint":"SKILL.md"`), and it is invoked as `$kang-enterprise-process-reviewer` (`SKILL.md:45`). Read it together with [`references/process-rubric.md`](references/process-rubric.md), which carries the evidence labels, the node contract, the decision boundaries, the review tests, severity, and the readiness gate; the rubric is linked from `SKILL.md:26` and is the only reference document in the package.

<details>
<summary>Installation and adapter status — partly unverified</summary>

- `npx skills add KanG-ciyuan/kang-enterprise-process-reviewer` appeared in the previous README. The npm package `skills` exists (published as `The open agent skills ecosystem`), so the CLI is real, but the end-to-end install of this repository was **not verified** — the audit environment had no network path to `github.com`. Treat this command as `TO_VERIFY`.
- `manifest.json` declares `"target_platforms":["codex","agent-skills-compatible"]`, while `agents/interface.yaml` declares `adapter_targets: [openai, claude, generic, agent-skills-compatible]`. The two lists disagree, and only [`agents/openai.yaml`](agents/openai.yaml) exists — `claude` and `generic` are declarations with no adapter artifact. Do not assume those adapters work.
- No installation layout is documented for non-Codex use.

</details>

---

## Part of the Kang Open-Source AI System

This project is one part of an evidence-driven system for enterprise AI transformation,
agent collaboration, and AI-native product delivery.

| Stage | Project | Role |
| --- | --- | --- |
| DISCOVER | [enterprise-ai-diagnostic-skills](https://github.com/KanG-ciyuan/enterprise-ai-diagnostic-skills) | Understand how the business actually works before automating it |
| DEFINE | [kang-product-architect](https://github.com/KanG-ciyuan/kang-product-architect) | Turn ambiguous requirements into an implementation-ready product contract |
| DEFINE | [kang-enterprise-process-reviewer](https://github.com/KanG-ciyuan/kang-enterprise-process-reviewer) | Review whether workflows are executable, accountable and recoverable |
| BUILD & COORDINATE | [kang-agent-workforce](https://github.com/KanG-ciyuan/kang-agent-workforce) | Role-based AI product workforce with explicit handoffs |
| BUILD & COORDINATE | [kang-agent-collab](https://github.com/KanG-ciyuan/kang-agent-collab) | Agent collaboration and handoff protocol |
| BUILD & COORDINATE | [kang-frontend-standard](https://github.com/KanG-ciyuan/kang-frontend-standard) | Frontend quality standard for AI-built interfaces |
| VERIFY | [kang-b2b-ux-auditor](https://github.com/KanG-ciyuan/kang-b2b-ux-auditor) | Can users actually finish the work? |
| VERIFY | [kang-product-acceptance-auditor](https://github.com/KanG-ciyuan/kang-product-acceptance-auditor) | Independent acceptance of AI-built products |
| DELIVER | [kang-github-readme](https://github.com/KanG-ciyuan/kang-github-readme) | Evidence-aware README engineering |
| DELIVER | [kang-ppt-skill](https://github.com/KanG-ciyuan/kang-ppt-skill) | Evidence-aware presentation design |

**Cross-cutting infrastructure:** [kang-meta-skill](https://github.com/KanG-ciyuan/kang-meta-skill) —
Skill engineering, evaluation and release governance.

**Earlier work:** [ai-agent-rules](https://github.com/KanG-ciyuan/ai-agent-rules),
[workflow-five-steps](https://github.com/KanG-ciyuan/workflow-five-steps),
[renovation-agent](https://github.com/KanG-ciyuan/renovation-agent).

```text
DISCOVER
Enterprise AI Diagnostic Skills
        ↓
DEFINE
Kang Product Architect
Kang Enterprise Process Reviewer
        ↓
BUILD & COORDINATE
Kang Agent Workforce
Kang Agent Collab
Kang Frontend Standard
        ↓
VERIFY
Kang B2B UX Auditor
Kang Product Acceptance Auditor
        ↓
DELIVER
Kang GitHub README
Kang PPT Skill
```

> This is an ecosystem map, not a strict runtime pipeline. The stages describe where
> each project sits in the work, not a mandatory execution order.

## License

Released under the [MIT License](LICENSE).

<!-- kang-author:start -->
## About Kang

Maintained by Kang. GitHub: https://github.com/KanG-ciyuan/
<!-- kang-author:end -->
