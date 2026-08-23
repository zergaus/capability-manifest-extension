# CME 2.0 — Responsibility & Interop Extension v0.2

**Document ID:** `CME-2.0-RESPONSIBILITY-INTEROP-EXT-v0.2`  
**Status:** `E2E REVIEW CANDIDATE / UNLOCKED / NOT RATIFIED`  
**Applies to:** CME 2.0 candidate architecture only  
**Current ratified authority:** CME 1.0  
**Base candidate:** `CME_2.0_BLUEPRINT_E2E_DRAFT.md`  
**Predecessor:** `CME_2.0_RESPONSIBILITY_INTEROP_EXTENSION_v0.1.md`  
**Source requirement:** PM validation + TM REV7 / synchronized-replica responsibility review  

---

# 0. Revision purpose

This v0.2 candidate refines the Responsibility & Interop extension after cross-checking it against real Thread Manager ownership, fencing, Human-presence, and recovery semantics.

The v0.1 direction remains valid, but three clarifications are now required:

```text
1. responsibility role `authoritative`
   → rename candidate role to `semantic_owner`

2. semantic responsibility ownership
   != runtime mutation ownership / active Host / leader / lease / fencing authority

3. Human-presence evidence
   → may be declared as a required invocation-context reference
   → CME never validates the Human or grants authority
```

This document supersedes v0.1 for overlapping Responsibility-role and Invocation-context terminology. It does not modify ratified CME 1.0.

---

# 1. Hard boundary — strengthened

CME MUST preserve all of the following distinctions:

```text
Capability != Authority
Responsibility declaration != trust proof
Semantic ownership != runtime mutation ownership
Semantic ownership != leader election
Semantic ownership != ownership lease
Semantic ownership != fencing authority
Required authority context != authority validation
Required Human-presence context != Human authentication
Delivery declaration != runtime transaction ownership
Dependency declaration != service discovery
Gateway capability != downstream capability ownership
Provider identity != authenticated principal
```

A provider may declare that it owns the canonical **machine semantics** for a domain.

That declaration does not prove that the provider instance is currently allowed to mutate the domain, is the active runtime owner, holds a valid fence, has won leadership, or has received organizational authority.

Those runtime and authority decisions remain external to CME.

---

# 2. Responsibility Domain role terminology

## 2.1 Candidate roles

The candidate Responsibility Domain roles are now:

```text
semantic_owner
gateway
observer
derived
```

### `semantic_owner`

The provider declares that its public semantic contract is the canonical source for the domain's machine meaning/state model.

This is intentionally **not** named `authoritative` because adopters such as Thread Manager separately use authority/ownership terms for live runtime mutation control, Host ownership, leases, fencing, and recovery.

### `gateway`

The provider transports/routes an operation across a responsibility boundary without becoming semantic owner of the downstream domain.

### `observer`

The provider observes the domain without claiming canonical state ownership or mutation ownership.

### `derived`

The provider publishes a derived/projection view whose source of truth remains elsewhere.

## 2.2 Conceptual shape

```yaml
responsibility_domains:
  - domain_id: chatgpt.conversation
    role: semantic_owner
  - domain_id: interop.transport
    role: gateway
```

## 2.3 Runtime-ownership separation

The following are explicitly outside Responsibility Domain role semantics:

```text
active writer election
single-writer lease
Host runtime ownership
project runtime ownership
ownership epoch
generation/fence token
resource arbitration
leader/follower state
crash takeover permission
recovery authorization
```

Example:

```text
TM declares semantic_owner for codex.work
+
Host B does not hold current TM runtime ownership/fence

→ CME declaration remains true
→ Host B still has no mutation authority
```

Likewise:

```text
PM declares semantic_owner for chatgpt.conversation
+
PM provider writer ownership is DRAINING / STANDBY / stale generation

→ CME semantic ownership remains unchanged
→ provider mutation remains blocked by PM runtime ownership rules
```

---

# 3. Human-presence evidence as Invocation Context

## 3.1 Candidate context kind

CME 2.0 Invocation Context Requirements MAY use the reusable context kind:

```text
human_presence_evidence_ref
```

It means only:

> this invocation contract requires a reference to external Human-presence evidence.

CME MUST NOT interpret this as proof that a Human is present or that the requested operation is authorized.

## 3.2 Ownership of validation

Example separation:

```text
CME
→ declares `human_presence_evidence_ref` is required

Gateway / transport service
→ preserves the opaque reference without widening its meaning

Owning Authority / Host trust boundary
→ validates source, action binding, expiry, revision binding, one-shot semantics, replay state, and Human identity
```

CME MUST NOT define:

```text
which Human is the Human CEO
how Human identity is authenticated
what credential proves Human presence
how one-shot evidence is issued
whether the evidence is still valid
whether the evidence grants the requested authority
```

## 3.3 Structural rejection

If an invocation contract requires `human_presence_evidence_ref` and the reference is absent, the invocation MAY fail structurally before authority evaluation.

This is contract incompatibility, not an authority denial.

---

# 4. Invocation Context candidate set — revised

The current reusable candidate context kinds are:

```text
principal_ref
authority_context_ref
human_presence_evidence_ref
delegation_id
request_id
source_binding_ref
target_binding_ref
correlation_ref
sequence_ref
operation_digest_ref
```

Namespaced context kinds remain allowed when a real adopter requirement does not fit a reusable core kind.

The presence of any reference does not transfer ownership of the referenced system into CME.

---

# 5. PM / TM reference mapping — revised

## 5.1 PM

Candidate PM Responsibility Domains:

```yaml
provider: executeoffice.pm
responsibility_domains:
  - { domain_id: chatgpt.project, role: semantic_owner }
  - { domain_id: chatgpt.conversation, role: semantic_owner }
  - { domain_id: chatgpt.logical_session, role: semantic_owner }
  - { domain_id: interop.binding, role: semantic_owner }
  - { domain_id: interop.transport, role: gateway }
```

PM runtime provider ownership, FIFO operation admission, writer generation, and provider fencing/recovery remain PM runtime contracts and are not implied by these declarations.

## 5.2 TM

Candidate TM Responsibility Domains:

```yaml
provider: executeoffice.tm
responsibility_domains:
  - { domain_id: codex.thread, role: semantic_owner }
  - { domain_id: codex.work, role: semantic_owner }
  - { domain_id: codex.execution, role: semantic_owner }
  - { domain_id: codex.runtime, role: semantic_owner }
```

TM may separately enforce:

```text
InvocationPrincipal
Authority Lease
Project Runtime Ownership
Host ownership generation
Resource Claims / Arbitration
operation_digest
fencing tokens
Human-presence evidence
recovery/quarantine state
```

Those are not CME Responsibility Domain semantics.

## 5.3 Human-sensitive TM operations

A TM capability requiring trusted Human confirmation MAY declare:

```yaml
required_context_refs:
  - principal_ref
  - authority_context_ref
  - human_presence_evidence_ref
```

The TM Host/Authority boundary, not CME, decides whether the referenced evidence satisfies the actual one-shot Human-presence contract.

---

# 6. Semantic fingerprint implications

A material change in Responsibility role or invocation-context requirement MUST participate in CME 2.0 compatibility/fingerprint evaluation when it changes safe invocation meaning.

Examples of material changes:

```text
semantic_owner → gateway
gateway → semantic_owner
required human_presence_evidence_ref → no longer required
no Human-presence requirement → newly required
```

A wording-only change that preserves the same machine semantics does not need to imply a capability identity change, but migration from the v0.1 candidate role token `authoritative` to v0.2 `semantic_owner` MUST be explicitly normalized in 2.0 candidate migration/conformance material.

---

# 7. Conformance additions

The CME 2.0 candidate conformance matrix SHOULD add at least:

```text
CME2-RI-01 semantic_owner does not imply runtime mutation ownership
CME2-RI-02 semantic_owner does not imply leader/lease/fence authority
CME2-RI-03 gateway cannot republish downstream responsibility as its own
CME2-RI-04 missing required human_presence_evidence_ref is structural failure
CME2-RI-05 presence of human_presence_evidence_ref does not authorize execution
CME2-RI-06 consumer/Host remains responsible for Human-evidence validation
CME2-RI-07 v0.1 authoritative-role token normalizes to v0.2 semantic_owner during candidate migration
```

---

# 8. Explicit non-scope reaffirmed

The following remain outside CME:

```text
Human CEO / company hierarchy
ChatGPT Headquarters / Codex organizational rank
Authority Ledger and delegation decisions
Human authentication / credential verification
Human-presence evidence issuance/consumption state machine
runtime ownership / leader election / fencing
unclean synchronized-replica recovery
Host liveness determination
split-brain recovery
PM/TM durable transaction state machines
AI semantic judgment
Service Hub trust administration / discovery
```

---

# 9. Candidate status

```text
CME 1.0
= RATIFIED AUTHORITY

CME 2.0 base Blueprint
= E2E REVIEW DRAFT / UNLOCKED

Responsibility & Interop Extension v0.2
= CURRENT PRE-INCORPORATION CANDIDATE
```

The next cumulative CME 2.0 Blueprint revision should incorporate v0.2 terminology and invariants before ratification review.
