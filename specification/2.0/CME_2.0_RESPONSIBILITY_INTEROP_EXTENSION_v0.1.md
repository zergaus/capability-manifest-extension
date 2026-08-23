# CME 2.0 — Responsibility & Interop Extension v0.1

**Document ID:** `CME-2.0-RESPONSIBILITY-INTEROP-EXT-v0.1`  
**Status:** `E2E REVIEW CANDIDATE / UNLOCKED / NOT RATIFIED`  
**Applies to:** CME 2.0 candidate architecture only  
**Current ratified authority:** CME 1.0  
**Base candidate:** `CME_2.0_BLUEPRINT_E2E_DRAFT.md`  
**Source requirement:** PM ↔ TM direct ChatGPT Headquarters / Codex interoperation responsibility review  

---

# 0. Purpose

This document pre-incorporates machine-responsibility and cross-service interoperation requirements discovered while validating Project Manager for ChatGPT (PM) and Thread Manager (TM).

It does **not** amend or reinterpret ratified CME 1.0.

It extends the unlocked CME 2.0 candidate so that a consumer can answer not only:

```text
What capability exists?
What Effects can it cause?
Is it available now?
What invocation requirements apply?
```

but also, when a capability participates in a multi-service path:

```text
Which provider claims canonical machine responsibility for each domain?
Which cross-service operation is being performed?
Which context references must accompany that invocation?
What delivery/correlation/idempotency semantics does the provider declare?
Which downstream semantic dependencies are required without copying their ownership?
```

These additions remain declaration semantics. They do not create organizational authority, trust, discovery, workflow orchestration, or runtime state machines.

---

# 1. Hard boundary

CME MUST preserve:

```text
Capability != Authority
Responsibility declaration != trust proof
Required authority context != authority validation
Delivery declaration != runtime transaction ownership
Dependency declaration != service discovery
Gateway capability != downstream capability ownership
Provider identity != authenticated principal
```

CME may declare that an invocation **requires** `principal_ref` or `authority_context_ref`.

CME MUST NOT decide whether those references are valid, current, sufficient, delegated by the Human CEO, or permitted for the requested effect.

Those decisions remain external consumer/authority responsibilities.

---

# 2. Responsibility Domain

## 2.1 Definition

A **Responsibility Domain** is a machine-readable declaration of which semantic domain a provider claims to own, gateway, observe, or derive.

It answers:

> For this provider contract, what kind of machine truth does this provider claim to be responsible for?

It is not organizational authority and is not proof that the provider should be trusted.

Candidate conceptual shape:

```yaml
responsibility_domains:
  - domain_id: chatgpt.conversation
    role: authoritative
  - domain_id: interop.transport
    role: gateway
```

Candidate roles:

```text
authoritative
→ provider claims canonical machine-state ownership for this domain within its public semantic contract

gateway
→ provider transports/routes operations for this domain but does not become canonical owner of downstream state

observer
→ provider observes the domain without mutation ownership

derived
→ provider publishes derived/projection data whose source of truth is elsewhere
```

## 2.2 Declaration is not trust

`role = authoritative` means only:

> this provider declares itself the canonical semantic owner for this domain.

A consumer MAY reject that declaration based on pairing/trust policy, architecture policy, provider identity mismatch, conflicting declarations, or local configuration.

CME MUST NOT resolve competing authoritative claims by itself.

## 2.3 Domain identity

Responsibility domains SHOULD be stable semantic identifiers, not UI labels or implementation classes.

Examples:

```text
chatgpt.project
chatgpt.conversation
chatgpt.logical_session
codex.thread
codex.work
codex.execution
codex.runtime
resource.telemetry
interop.binding
interop.transport
```

A transport change or implementation rewrite SHOULD NOT change responsibility-domain identity when semantic ownership is unchanged.

## 2.4 Relationship to target kinds

Responsibility Domain and Target Kind are distinct:

```text
Responsibility Domain
→ who claims machine-semantic ownership

Target Kind
→ what kind of object/resource a capability can affect
```

A provider can affect a target kind without owning the target's canonical state.

---

# 3. Cross-Service Operation Contract

CME 2.0 InvocationBinding SHOULD support an optional cross-service operation contract when an invocation crosses provider/service responsibility boundaries.

Candidate conceptual shape:

```yaml
operation_contract:
  operation_class: request
  source_domain: chatgpt.logical_session
  target_domain: codex.work
  required_context_refs:
    - principal_ref
    - authority_context_ref
    - delegation_id
    - request_id
    - source_binding_ref
    - target_binding_ref
  correlation:
    required: true
  idempotency:
    key_required: true
  delivery:
    ambiguous_outcome_possible: true
    reconciliation_supported: true
```

The contract describes machine semantics required to invoke the capability safely and compatibly.

It does not standardize one universal RPC wire protocol.

Bindings remain free to represent the same semantic contract through MCP, Local Semantic Binding, or another future binding.

---

# 4. Invocation Context Requirements

## 4.1 Purpose

A capability may require contextual references whose meaning is owned elsewhere.

CME SHOULD be able to declare the required presence and semantic binding of those references without validating their external authority meaning.

Candidate reusable context kinds:

```text
principal_ref
authority_context_ref
delegation_id
request_id
source_binding_ref
target_binding_ref
correlation_ref
sequence_ref
operation_digest_ref
```

The list is extensible through namespaced context kinds.

## 4.2 Required vs interpreted

CME declares:

```text
this invocation requires authority_context_ref
```

CME does not declare:

```text
this authority_context_ref grants permission
```

Example ownership:

```text
CME
→ declares required context

PM/gateway
→ preserves and transports opaque context references

TM Authority / consumer enforcement
→ validates actual authority semantics
```

A gateway MUST NOT rewrite, broaden, or synthesize authority meaning merely to satisfy an invocation requirement.

## 4.3 Missing context

If a required context reference is absent, the provider/consumer MAY reject the invocation as structurally incompatible before any authority decision is attempted.

This is schema/contract enforcement, not an authority denial.

---

# 5. Delivery, Correlation, Idempotency, and Replay Declaration

## 5.1 Purpose

Cross-service consumers need to know what transport-level execution guarantees a provider claims before building safe retry/recovery logic.

Candidate declaration fields:

```yaml
delivery_semantics:
  request_identity: delegation_id
  idempotency:
    supported: true
    key_kind: consumer_supplied
  result_correlation:
    required: true
  ambiguous_outcome:
    possible: true
  reconciliation:
    supported: true
  replay:
    duplicate_effect_forbidden: true
```

## 5.2 Provider promise, not consumer state machine

CME may declare that a provider supports idempotent request identity or reconciliation.

CME MUST NOT define the consumer's durable transaction states such as:

```text
PREPARED
DISPATCHING
RESULT_UNKNOWN
RECONCILING
COMMITTED
```

Those state machines remain owned by PM, TM, or another consuming runtime.

## 5.3 No invented exactly-once guarantee

A provider MUST NOT claim `exactly_once` merely because it attempts deduplication.

If the provider cannot prove an outcome after response loss, it SHOULD declare ambiguous outcome as possible and expose the strongest actual reconciliation semantics available.

`unknown` is preferable to fabricated delivery certainty.

## 5.4 Material semantic field

A change from:

```text
idempotency supported
→ idempotency unsupported
```

or:

```text
reconciliation supported
→ reconciliation unsupported
```

is materially relevant to safe invocation and MUST participate in semantic compatibility/fingerprint evaluation where the declaration applies.

---

# 6. Service-to-Service Dependency and Compatibility Requirements

## 6.1 Non-transitive dependency declaration

A provider capability MAY declare that successful execution depends on another semantic capability/domain without inheriting ownership of that dependency.

Candidate conceptual shape:

```yaml
dependency_requirements:
  - kind: responsibility_domain
    id: codex.work
    required_role: authoritative
  - kind: capability
    id: codex.delegation.accept
  - kind: binding_compatibility
    core_version: "2.0"
    binding_kind: local-semantic
```

This means:

```text
my capability requires a compatible downstream provider
```

not:

```text
I own the downstream provider's capability/state
```

## 6.2 Discovery remains external

CME does not locate, pair, authenticate, or select the peer instance.

Service discovery, trust establishment, and peer selection remain external architecture concerns.

CME only describes the semantic compatibility requirement once a candidate peer/provider is known.

## 6.3 Availability dependency

A supported capability may become `unavailable` or `degraded` because a required dependency is unavailable/incompatible.

The provider MAY expose a stable reason such as:

```text
DEPENDENCY_UNAVAILABLE
DEPENDENCY_INCOMPATIBLE
DEPENDENCY_CONTRACT_MISMATCH
```

The provider MUST NOT copy the dependency's entire capability catalog into its own manifest.

---

# 7. Semantic Fingerprint Expansion Candidate

For CME 2.0, material interop declarations SHOULD participate in semantic fingerprint input when they affect invocation safety, authority requirements, replay safety, or meaning.

Candidate fingerprint-relevant fields include:

```text
responsibility-domain identity + role
cross-service operation source/target domains
required invocation-context kinds
idempotency key requirement/semantics
result-correlation requirement
ambiguous-outcome/reconciliation semantics
dependency requirements that materially constrain execution
```

Display labels, descriptions, icons, or human-readable names remain non-semantic.

A provider MUST NOT silently change a capability from a direct authoritative operation into a gateway operation, remove required authority context, or weaken replay/reconciliation semantics while preserving a fingerprint that claims semantic equivalence.

---

# 8. PM / TM reference mapping

This section records the adopter requirement that motivated the extension. It is illustrative and does not make PM/TM names part of CME Core.

## 8.1 PM

Candidate responsibility declaration:

```yaml
provider: executeoffice.pm
responsibility_domains:
  - { domain_id: chatgpt.project, role: authoritative }
  - { domain_id: chatgpt.conversation, role: authoritative }
  - { domain_id: chatgpt.logical_session, role: authoritative }
  - { domain_id: interop.binding, role: authoritative }
  - { domain_id: interop.transport, role: gateway }
```

Candidate PM interop capabilities:

```text
interop.delegation.forward
interop.result.deliver
```

These names intentionally do not make PM the owner of `codex.thread` or `codex.work`.

A PM delegation-forward invocation may require:

```text
principal_ref
authority_context_ref
delegation_id
request_id
source_binding_ref
target_binding_ref
```

PM preserves those references but does not decide whether the authority context is valid.

## 8.2 TM

Candidate responsibility declaration:

```yaml
provider: executeoffice.tm
responsibility_domains:
  - { domain_id: codex.thread, role: authoritative }
  - { domain_id: codex.work, role: authoritative }
  - { domain_id: codex.execution, role: authoritative }
  - { domain_id: codex.runtime, role: authoritative }
```

Candidate TM-side capabilities:

```text
codex.delegation.accept
codex.result.publish
```

TM remains responsible for Codex/work admission and its own Authority/Capability/Ownership/Claim/Effect enforcement.

CME only declares what the operation requires and what semantic/delivery guarantees TM exposes.

## 8.3 GPT Skill / Codex MCP edges

An edge adapter does not automatically become a distinct CME provider merely because it exists in the call path.

It becomes a provider only if it exposes an independently addressable semantic control boundary that benefits from its own capability declaration.

AI decisions such as whether to delegate again or whether a result is sufficient are never CME semantics.

---

# 9. Strengthened gateway non-transitivity

The CME 2.0 base rule remains and is strengthened:

```text
Provider A routes to Provider B
!=
Provider A owns Provider B's capability
!=
Provider A owns Provider B's responsibility domain
```

A gateway provider SHOULD declare its own gateway responsibility and its downstream dependency requirements rather than republishing downstream capability identity as its own.

Example:

```text
PM interop.delegation.forward
→ PM-owned gateway capability
→ depends on TM codex.delegation.accept
→ does not make PM owner of codex.work
```

---

# 10. Explicit non-scope

The following remain outside CME even when referenced by a CME invocation requirement:

```text
Human CEO / company hierarchy
ChatGPT Headquarters / Codex organizational rank
Authority Ledger and grant/delegation decisions
principal issuance/authentication
validity of authority_context_ref
service pairing / trust administration
Service Hub discovery or Introducer behavior
AI semantic judgment / result sufficiency
PM FIFO queue
PM/TM durable transaction state machines
RESULT_UNKNOWN recovery implementation
TM WorkState / claims / arbitration
ChatGPT conversation rollover/rollback
runtime wake/resume mechanism
provider lifecycle/process ownership
```

CME may describe that a required reference or dependency exists. It does not take ownership of the referenced system.

---

# 11. Compatibility posture

CME 1.0 remains unchanged and ratified.

The fields proposed here are CME 2.0 candidate semantics.

A simple CME 2.0 provider that does not participate in cross-service gateway/interoperation SHOULD NOT be forced to implement irrelevant delivery/dependency metadata.

However:

- a provider that claims gateway responsibility SHOULD declare downstream dependency semantics;
- a cross-service operation that requires correlation/idempotency SHOULD declare those requirements explicitly;
- a provider claiming canonical ownership of a machine domain SHOULD declare the corresponding Responsibility Domain;
- absence of optional declarations MUST NOT be interpreted as a stronger guarantee.

---

# 12. Candidate conformance additions

```text
CME2-RI-01 Responsibility Domain is distinct from authority and trust
CME2-RI-02 authoritative/gateway/observer/derived roles are represented without transitive ownership
CME2-RI-03 Target Kind remains distinct from Responsibility Domain
CME2-RI-04 cross-service operation can declare source/target domains
CME2-RI-05 required invocation context is machine-readable
CME2-RI-06 required authority context is not validated by CME
CME2-RI-07 correlation/idempotency/replay semantics are explicit when claimed
CME2-RI-08 ambiguous delivery does not silently become exactly-once
CME2-RI-09 downstream dependency declaration does not copy capability ownership
CME2-RI-10 dependency discovery/trust remain external
CME2-RI-11 material responsibility/delivery/requirement changes affect semantic compatibility/fingerprint
CME2-RI-12 PM can map ChatGPT ownership + gateway transport without claiming Codex ownership
CME2-RI-13 TM can map Codex ownership without importing ChatGPT state
CME2-RI-14 AI judgment and runtime transaction state remain outside CME
```

---

# 13. Consolidation gate

The next cumulative CME 2.0 Blueprint revision MUST either:

1. incorporate these semantics into the Core/InvocationBinding/Local Semantic Binding sections; or
2. explicitly reject a requirement with adopter-impact analysis.

Silent omission is not allowed once PM/TM reverse-adoption depends on these fields.

Before CME 2.0 ratification, reverse adoption MUST verify at least:

```text
PM
TM
one simple non-gateway provider
one dynamic MCP provider
```

so the new interop semantics do not distort ordinary CME providers.

Until ratification:

```text
CME 1.0 = RATIFIED AUTHORITY
CME 2.0 base draft + this extension = UNLOCKED CANDIDATE PACKAGE
```
