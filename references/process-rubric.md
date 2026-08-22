# Process Review Rubric

## Evidence labels

- `confirmed`: supported by an authoritative policy, direct observation, reliable system record, or named accountable owner.
- `inferred`: supported by circumstantial or incomplete evidence and safe only as a provisional model.
- `to_verify`: unsupported, conflicting, stale, demo-generated, or consequential enough to require direct confirmation.

Interviews describe experience; logs describe recorded behavior; policies describe intended rules. None alone proves the complete process. Preserve conflicts instead of averaging them away.

## Node contract

Each critical node requires: `actor`, `trigger`, `input`, `rule`, `action`, `output`, `receiver`, `evidence`, `authorization`, `failure`, `recovery`, `timeout`, `escalation`, and `audit_record`.

The process is incomplete if a field is materially relevant but has no owner or explicit `not_applicable` rationale.

## Decision boundaries

- **Deterministic rule:** use when inputs, outcomes, and exceptions are explicit and testable.
- **Bounded Agent/model work:** use for extraction, classification, comparison, summarization, or recommendation when uncertainty is visible and reversible.
- **Human judgment:** require for policy exceptions, consequential approvals, ambiguous business truth, sensitive access, or irreversible action.

An Agent may recommend but must not silently approve, certify truth, grant access, or suppress an exception. Human approval must identify the approver, decision object, evidence seen, allowed outcomes, and audit record.

## Review tests

- authorization and least privilege;
- segregation of request, approval, and audit duties;
- provenance and sufficiency of evidence;
- sensitive-data minimization and retention;
- reversibility and recovery;
- duplicates, timeouts, retries, stale state, and concurrency;
- exception ownership and escalation deadline;
- receiver readiness and handoff acceptance;
- observable completion and auditability.

## Severity

- `blocker`: unsafe authority, missing consequential decision owner, fabricated truth, or unrecoverable critical failure.
- `high`: critical node cannot complete, hand off, or recover reliably.
- `medium`: process works only through undocumented manual knowledge or creates significant delay/error risk.
- `low`: local clarity or efficiency issue with bounded impact.

## Readiness gate

A process is implementation-ready only when critical nodes have accountable actors, evidence and authorization rules, receiver acceptance, exception paths, and audit records. Any unresolved blocker, sensitive-data authority, or human decision gate prevents readiness.
