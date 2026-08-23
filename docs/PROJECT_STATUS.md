# CME Project Status

## Current state

**CME 1.0 remains the ratified design contract and current normative authority.**

**CME 2.0 is an unlocked, not-ratified evolution candidate.**

The current CME 2.0 candidate package is:

```text
specification/2.0/CME_2.0_BLUEPRINT_E2E_DRAFT.md
+
specification/2.0/CME_2.0_RESPONSIBILITY_INTEROP_EXTENSION_v0.1.md
+
docs/requirements/CME_ECOSYSTEM_REQUIREMENT_MATRIX_v0.2.md
```

The Responsibility & Interop extension is a pre-incorporation companion for the next cumulative CME 2.0 Blueprint revision. It does not modify ratified CME 1.0 and is not independently ratified authority.

This distinction matters:

```text
CME 1.0 ratified authority
!=
CME 2.0 review candidate
!=
completed public implementation package
```

## Current 2.0 direction

CME 2.0 continues the transport-neutral direction discovered from PM/RT/TM/SB adopter requirements:

```text
CME 2.0 Core
├─ MCP Binding
└─ Local Semantic Binding
```

The current PM ↔ TM direct-interoperation review adds a second candidate requirement layer:

```text
Responsibility Domain
Cross-Service Operation Contract
Invocation Context Requirements
Delivery / Correlation / Idempotency Declaration
Service-to-Service Dependency / Compatibility Declaration
Semantic-fingerprint coverage for material interop semantics
```

The intent is to make machine responsibility and safe cross-service invocation requirements explicit without moving Authority, trust, service discovery, workflow orchestration, AI judgment, or runtime recovery state machines into CME.

## Hard responsibility boundary

CME remains a declaration standard.

The following invariants are preserved:

```text
Capability != Authority
Availability != Authorization
Responsibility declaration != trust proof
Required authority context != authority validation
Delivery declaration != runtime transaction ownership
Dependency declaration != service discovery
Gateway routing != downstream capability ownership
Provider identity != authenticated principal
```

CME may declare that an invocation requires `principal_ref`, `authority_context_ref`, correlation identity, or idempotency semantics.

CME does not decide whether an authority reference is valid, whether a Human CEO granted it, whether an AI should invoke another AI, or whether a runtime should retry/reconcile a failed operation.

## PM / TM responsibility direction

Current adopter mapping under review:

```text
PM
→ authoritative: chatgpt.project / chatgpt.conversation / chatgpt.logical_session / interop.binding
→ gateway: interop.transport
→ does not own codex.thread / codex.work

TM
→ authoritative: codex.thread / codex.work / codex.execution / codex.runtime
→ validates its own Authority/Capability/Ownership/Claim/Effect path outside CME
```

PM may expose gateway capabilities such as:

```text
interop.delegation.forward
interop.result.deliver
```

while TM may expose downstream semantics such as:

```text
codex.delegation.accept
codex.result.publish
```

The names are adopter candidates, not frozen CME Core vocabulary.

## Evolution principle

CME is **operational-fit first**.

The standard evolves from concrete requirements in systems that actually use it. Generality is introduced when it reduces real integration complexity or captures a pattern already proven across adopters.

Expected loop:

```text
real adopter requirement
→ requirement matrix
→ candidate CME semantics
→ reverse adoption
→ implementation evidence
→ cross-adopter comparison
→ consolidate only what survives
→ versioned ratification
```

Public reuse is a benefit of a good working contract, not a requirement that primary adopters distort their architecture to satisfy hypothetical users.

See [`docs/rationale/OPERATIONAL_FIT_FIRST.md`](rationale/OPERATIONAL_FIT_FIRST.md).

## Locked 1.0 boundaries

The following remain stable CME 1.0 design decisions unless a documented future version supersedes them:

- CME declares capabilities; it does not grant authority.
- Provider-scoped capabilities are paired with common execution Effects.
- Provider support, host availability, organizational authority, and platform permission are separate states.
- Static and dynamic Tool requirements are both supported.
- Dynamic resolution is bound to arguments and manifest state through deterministic hashes/fingerprints.
- Manifest and semantic fingerprint hashing use RFC 8785 canonicalization + SHA-256.
- Capability profiles are conveniences and must expand to explicit snapshots when used by an authority consumer.
- Future capabilities are not automatically granted by older snapshots.
- Existing capability IDs cannot silently acquire materially broader semantics.
- Unknown Effects and non-CME inference are handled conservatively.
- Providers remain usable without Thread Manager or any particular CME consumer.

## Publication status

This repository is public, but CME is **not an official MCP Core standard**.

The current development/private 1.0 extension identifier is:

```text
local.cme/capability-manifest
```

A stable public release requires an owner-controlled reverse-domain namespace. Until that decision is made, the development identifier must not be presented as a globally owned public namespace.

## Work remaining for CME 2.0 convergence

1. Consolidate the Responsibility & Interop extension into the next cumulative CME 2.0 Blueprint revision.
2. Reverse-apply Responsibility Domains and cross-service invocation requirements to PM and TM.
3. Verify that a simple non-gateway provider is not distorted by optional interop fields.
4. Verify that an existing dynamic MCP provider preserves natural CME semantics.
5. Define the exact 2.0 machine-readable shape and canonical hashing inputs for Responsibility/Interop fields.
6. Specify Local Semantic Binding discovery/metadata representation without creating a universal RPC framework.
7. Produce 1.0→2.0 migration/equivalence rules.
8. Add conformance cases for non-transitive responsibility, context requirements, ambiguous delivery, idempotency, reconciliation, and dependency compatibility.
9. Independently review the ESHIC/Trust/Authority boundaries before ratification.

## Work remaining for a complete CME 1.0 public package

1. Extract a standalone normative `CME_1.0_SPEC.md` from the ratified Blueprint without changing locked semantics.
2. Publish the normative Effect vocabulary as a dedicated document.
3. Define and validate machine-readable schemas for manifest, status, and resolve.
4. Build the CME Conformance Test Kit covering the Blueprint's required and known-bad cases.
5. Validate against the two reference provider shapes: Excel-like dynamic provider and Reporting-like static provider.
6. Select the stable public reverse-domain extension identifier and document migration from `local.cme/capability-manifest`.
7. Perform cross-document consistency review before a stable `v1.0.0` release/tag.

## Change discipline

Changes to titles, descriptions, examples, and other display-only material may be editorial when they do not alter semantics.

Changes to Effects, responsibility roles, required invocation context, delivery/idempotency/reconciliation guarantees, dependency semantics, scope kinds, target kinds, platform requirements, execution constraints, dynamic-resolution integrity rules, fingerprint inputs, or other authority/safety-relevant semantics are normative candidate changes and require compatibility review before ratification.

A change that materially broadens an existing capability's meaning cannot be shipped silently under the same capability ID/fingerprint.

A generalization that is not motivated by real adopter needs should remain a proposal rather than becoming normative merely for theoretical completeness.
