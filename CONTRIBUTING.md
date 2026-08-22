# Contributing to CME

CME is an experimental third-party MCP extension with a deliberately strict separation between **capability declaration** and **authority**. Contributions are welcome, but normative changes must preserve that boundary.

## Before proposing a change

Please read:

1. [`specification/1.0/CME_1.0_BLUEPRINT.md`](specification/1.0/CME_1.0_BLUEPRINT.md)
2. [`docs/PROJECT_STATUS.md`](docs/PROJECT_STATUS.md)
3. [`docs/rationale/OPERATIONAL_FIT_FIRST.md`](docs/rationale/OPERATIONAL_FIT_FIRST.md)
4. [`docs/adoption/CME_1.0_MCP_ADOPTION_DIRECTIVE.md`](docs/adoption/CME_1.0_MCP_ADOPTION_DIRECTIVE.md) when the change affects provider adoption.

## Operational-fit-first rule

CME is developed from real adopter requirements.

A proposal should preferably be justified by one or more of:

- a concrete problem in an existing adopter;
- a repeated pattern across existing adopters;
- a simplification that measurably reduces implementation or operational complexity;
- a compatibility/safety defect in the current contract.

A proposal should **not** be accepted merely because it makes CME look more universal.

If a generalization makes current adopters harder to implement, harder to reason about, or less safe, the proposal needs explicit evidence that the tradeoff is worthwhile.

The ratified CME 1.0 contract remains stable. New adopter-driven requirements that cannot fit without semantic breakage belong in an explicitly versioned successor.

## Contribution categories

### Editorial

Examples include wording, formatting, navigation, examples, localization, and other display-only changes that do not alter authorization-relevant semantics.

### Compatible normative addition

Examples may include optional fields or additional non-breaking guidance that preserves all existing semantics and compatibility guarantees.

### Breaking normative change

A change is breaking when it removes or renames required fields, changes field types incompatibly, changes an existing Effect meaning, broadens an existing capability meaning, adds incompatible grant-sensitive semantics, or otherwise invalidates prior authority interpretation.

Breaking changes require an explicitly versioned contract revision.

## Required invariants

Contributions must not:

- embed authority grants, approvals, delegation records, or provider self-approval into CME manifests;
- treat provider `risk_hint` as authoritative policy;
- collapse availability into authorization;
- imply that CME bypasses OAuth, credentials, sandboxing, host confirmations, UAC, or OS/platform security;
- silently broaden an existing capability ID;
- make a CME-enabled provider depend on Thread Manager merely to preserve its ordinary MCP behavior;
- under-declare execution Effects to make a capability appear safer than its real behavior.

## Tests

Normative implementation work should add deterministic tests for the affected contract. Dynamic-resolution changes must include stale-argument and manifest-change detection. Hash/fingerprint changes must demonstrate deterministic canonicalization and the required semantic/display-only stability properties.

The complete required CTK coverage is defined in the ratified Blueprint.

## Pull requests

Keep normative and editorial changes distinguishable. A pull request that changes authority-relevant semantics should explain:

- what contract field or rule changes;
- which real adopter requirement motivates it;
- whether the change is backward compatible;
- which fingerprints/hashes are expected to change;
- whether provider or consumer conformance behavior changes;
- which tests demonstrate the intended behavior.

## License

By contributing to this repository, you agree that your contributions are licensed under the repository's Apache License 2.0 terms.
