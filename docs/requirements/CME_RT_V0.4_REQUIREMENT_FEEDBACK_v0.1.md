# CME 2.0 — RT v0.4 Requirement Feedback v0.1

**Status:** `E2E REVIEW INPUT / UNLOCKED / NON-NORMATIVE`  
**Current ratified authority:** CME 1.0  
**Target:** CME 2.0 transport-neutral + responsibility/interop candidate  
**Source adopter:** Resource Thermometer (RT) v0.4  
**Source RT commit:** `68a4e34f02a3ba340dea208c6a9dc21fd7bfd5ed`  
**Source RT documents:**
- `docs/architecture/RESOURCE_THERMOMETER_BLUEPRINT_v0.4_E2E_DRAFT.md`
- `docs/architecture/RT_AUDIT_AUTHORITY_SECURITY_v0.4.md`

---

# 0. Purpose

RT v0.4 changed from a simple resource collector into an independent resource-audit tool with explicit audit-read authority, host-scoped acquisition, target-scoped attribution, evidence quality, and a separated audit-security boundary.

This feedback records the CME implications for a later CME 2.0 consolidation/revision. It does **not** modify CME 1.0 and does not advance CME 2.0 authority.

The central conclusion is:

> CME 2.0's current transport-neutral and Responsibility Domain direction remains correct. RT v0.4 adds bounded adopter requirements around semantic ownership, audit invocation context, degraded availability, and diagnostic-capture semantics without moving RT's authority model into CME.

---

# 1. Preserve the current CME boundaries

RT v0.4 reinforces the existing CME invariants:

```text
Capability != Authority
Availability != Authorization
Provider declaration != trust proof
Semantic ownership != runtime ownership
Semantic ownership != fencing authority
Required context != context validation
Binding transport != capability semantics
```

RT specifically adds another concrete reason to preserve these distinctions:

```text
RT AUDIT_AUTHORITY
!= AUDIT_REQUEST_AUTHORITY
!= EVIDENCE_ACCESS_AUTHORITY
!= MANAGEMENT_AUTHORITY
```

These RT authority planes remain RT/host/security-policy concerns and MUST NOT be absorbed into CME.

---

# 2. Requirement CME-RT-R1 — RT responsibility domain

The older ecosystem requirement candidate described RT roughly as:

```text
resource.telemetry → authoritative or observer
```

That wording is now stale for two reasons:

1. CME 2.0 candidate terminology has moved from `authoritative` to `semantic_owner`;
2. RT v0.4 owns more than a metric pipe: it owns resource-audit evidence semantics, quality, baseline comparison, and resource anomaly evidence.

The next CME 2.0 consolidation SHOULD evaluate a canonical RT responsibility domain such as:

```text
resource.audit
```

or, if narrower naming is preferred:

```text
resource.audit.evidence
```

with candidate role:

```text
semantic_owner
```

Meaning:

> RT owns the public machine semantics for RT resource-audit evidence.

Non-meaning:

```text
RT owns the audited service domain
RT owns target lifecycle
RT owns organizational authority
RT owns current Host acquisition leadership merely because it is semantic_owner
```

`resource.telemetry` may remain a valid alternate domain if the final CME review deliberately keeps telemetry as the canonical public term, but the final choice should account for RT v0.4's audit/evidence product identity.

---

# 3. Requirement CME-RT-R2 — semantic ownership vs Host acquisition ownership

RT v0.4 has internal runtime ownership for host acquisition:

```text
acquisition_owner_id
acquisition_generation
fencing / stale-owner rejection
```

CME Responsibility Domain semantics MUST NOT model or imply that ownership.

Required invariant:

```text
semantic_owner(resource.audit)
!= active RT Host Agent
!= acquisition owner
!= DB writer
!= generation/fence holder
```

An RT provider may remain the semantic owner of `resource.audit` while a particular RT runtime instance is standby, stale, unavailable, or not the current acquisition owner.

This is the same class of separation already harvested from PM/TM runtime ownership and should be retained as a cross-adopter conformance case.

---

# 4. Requirement CME-RT-R3 — Local Semantic Binding is RT's primary CME shape

RT Host Agent is a local semantic provider and should not be forced to run MCP merely to expose CME metadata.

The preferred RT binding is therefore:

```text
CME 2.0 Core
└─ Local Semantic Binding
   ├─ Manifest
   ├─ Status
   └─ Resolve where argument-sensitive semantics require it
```

This reinforces the existing transport-neutral CME 2.0 requirement.

Embedded RT does not automatically become a separate CME provider. A hosting application may expose RT-derived capabilities through its own provider declaration where that is the natural product boundary.

---

# 5. Requirement CME-RT-R4 — candidate RT capability surface

The current RT v0.4 machine-facing requirement harvest is:

```text
resource.targets.list
resource.target.inspect
resource.current.read
resource.history.read
resource.baseline.compare
resource.diagnostic.capture
resource.audit.quality.read
```

The final IDs remain adopter/provider decisions, but the capability classes are real.

Baseline intent:

```text
list/inspect/current/history/baseline/quality
→ observation/evidence capabilities

diagnostic.capture
→ changes RT telemetry/audit state only
→ does not mutate the audited target
```

CME MUST NOT invent target-control Effects merely because diagnostic capture increases sampling activity.

---

# 6. Requirement CME-RT-R5 — invocation context may be required, validation remains external

RT v0.4's privileged audit model requires caller identity and authorization outside CME.

Some RT invocations may structurally require typed context references such as:

```text
principal_ref
authority_context_ref
correlation_ref
request_id
```

Examples:

```text
resource.current.read
→ may require caller/principal context so RT can enforce EVIDENCE_ACCESS_AUTHORITY

resource.diagnostic.capture
→ may require caller/principal + audit-request authority context
```

CME meaning:

> this operation structurally requires these context references.

CME non-meaning:

```text
caller is authenticated
caller may read the requested target
caller may expand audit scope
authority reference is valid
diagnostic request is approved
```

Actual validation remains RT/Host/Authority-policy owned.

`human_presence_evidence_ref` should remain optional and used only if a future RT operation is genuinely Human-sensitive. Ordinary authorized audit reads do not justify requiring Human presence.

---

# 7. Requirement CME-RT-R6 — degraded/partial availability without conflating authorization

RT can often remain usable while individual audit paths are degraded.

Examples:

```text
resource.current.read = available
private_commit metric = EXACT
GPU metric = UNSUPPORTED
attribution = STRONG

resource.current.read = available/degraded
ETW lifecycle = unavailable
snapshot fallback = active

resource.history.read = unavailable
resource.current.read = available
history store quarantined
```

CME 2.0 should preserve enough status semantics for a provider to report capability availability independently while detailed metric quality stays in RT domain data.

Critical rule:

```text
capability unavailable/degraded because RT cannot provide the operation
!=
caller unauthorized by RT EVIDENCE_ACCESS_AUTHORITY
```

Authorization failure remains external to CME availability.

---

# 8. Requirement CME-RT-R7 — diagnostic capture semantics

`resource.diagnostic.capture` is a useful boundary test.

It may:

```text
increase RT sampling rate
create/update RT diagnostic-session state
increase bounded RT storage/I/O
```

It does not:

```text
kill/restart/suspend target
change target priority/affinity
throttle target
modify target configuration
```

CME may describe capability Effects, required contexts, argument-sensitive limits, correlation/idempotency semantics, and current availability.

CME MUST NOT absorb:

```text
RT OverheadBudget algorithm
sampling state machine
rate-limit enforcement
diagnostic expiry implementation
AUDIT_REQUEST_AUTHORITY validation
```

---

# 9. Requirement CME-RT-R8 — do not prematurely standardize privileged administration capabilities

RT v0.4 has administrative concepts such as:

```text
target registration
audit-scope expansion
evidence-access policy
provider-hint authorization
```

These exist because RT may be a privileged auditor.

The baseline CME 2.0 RT surface SHOULD NOT automatically add generic capabilities such as:

```text
resource.target.register
resource.audit.scope.expand
resource.evidence.access.grant
```

unless a real cross-service machine invocation requires those operations.

For now they remain RT deployment/security administration concerns.

This follows CME's operational-fit-first rule.

---

# 10. Explicit non-scope for CME

RT v0.4 reinforces that CME must not absorb:

```text
RT metric schema
raw counter schema
sampling intervals
process-instance identity
attribution algorithms/quality vocabulary
ETW implementation
Job Object policy
SQLite/retention model
baseline statistics
anomaly algorithms
Host acquisition ownership/fencing
AUDIT_AUTHORITY
AUDIT_REQUEST_AUTHORITY
EVIDENCE_ACCESS_AUTHORITY
RT IPC authentication implementation
```

CME only declares machine capability/responsibility/invocation semantics.

---

# 11. Reverse-adoption checks after CME 2.0 consolidation

When the next cumulative CME 2.0 candidate is ready, reverse-apply it to RT and verify:

```text
CME-RT-A1 RT can expose CME over Local Semantic Binding without becoming MCP
CME-RT-A2 resource-audit semantic ownership is representable without claiming target ownership
CME-RT-A3 semantic_owner cannot be confused with Host acquisition ownership/fencing
CME-RT-A4 RT read capabilities remain distinct from caller authorization
CME-RT-A5 degraded capability availability can coexist with per-metric RT quality
CME-RT-A6 diagnostic capture remains RT-state-changing but target-non-mutating
CME-RT-A7 required invocation context can be declared without CME validating RT authority
CME-RT-A8 no RT telemetry/security/runtime schema leaks into CME core
```

---

# 12. Feedback verdict

```text
CME-RT-R1 responsibility domain update                 HARVEST
CME-RT-R2 semantic/runtime ownership separation        HARVEST
CME-RT-R3 Local Semantic Binding primary shape         CONFIRM EXISTING DIRECTION
CME-RT-R4 RT audit capability surface                  HARVEST
CME-RT-R5 typed invocation context, external validation HARVEST / CONFIRM BOUNDARY
CME-RT-R6 degraded availability vs authorization       HARVEST / CONFIRM BOUNDARY
CME-RT-R7 diagnostic capture semantics                 HARVEST
CME-RT-R8 no speculative admin capability expansion    PRESERVE OPERATIONAL-FIT-FIRST
```

This document is future revision input only. It does not alter ratified CME 1.0 or independently ratify any CME 2.0 candidate semantics.
