# CME Ecosystem Requirement Matrix v0.2

**Status:** `E2E REVIEW INPUT / UNLOCKED`  
**Supersedes:** `CME_ECOSYSTEM_REQUIREMENT_MATRIX_v0.1.md` as the current requirement-harvest candidate  
**Current ratified authority:** CME 1.0  
**Source adopters:** PM, TM, RT, SB predecessor evidence  
**New source:** PM ↔ TM direct ChatGPT Headquarters / Codex responsibility review  

---

# 1. Executive conclusion

The ecosystem still does **not** reveal a fundamental problem with CME 1.0's capability/effect model.

The first CME 2.0 gap remains transport coupling:

> capability semantics must survive MCP vs local-semantic transport.

The PM/TM direct-interoperation review exposes a second shared gap:

> transport-neutral capability declaration is insufficient when multiple services participate in one operation unless responsibility ownership, invocation-context requirements, delivery/correlation guarantees, and downstream semantic dependencies are also machine-readable.

Therefore the current CME 2.0 candidate needs both:

```text
Transport-neutral Core + Bindings
+
Responsibility & Interop declaration semantics
```

CME still MUST NOT become the Authority Plane, service registry, trust system, workflow orchestrator, or runtime transaction manager.

---

# 2. Constraints inherited from CME 1.0

A successor must preserve:

```text
capability != authority
SUPPORTED != AVAILABLE != AUTHORIZED != PLATFORM-APPROVED
provider declaration != trust proof
provider-scoped capability IDs
common Effect vocabulary
platform requirements
static/dynamic invocation resolution
manifest/status/resolve separation
semantic fingerprints
No Silent Semantic Expansion
profile snapshot safety
unknown Effect conservative handling
legacy/non-CME compatibility
```

CME 1.0 remains ratified historical authority. This matrix does not edit it.

---

# 3. PM requirements — updated

PM exposes ChatGPT product/session/lifecycle operations and now also serves as the deterministic transport/binding gateway between the ChatGPT edge and TM.

PM-owned semantic domains include candidate responsibility domains:

```text
chatgpt.project
chatgpt.conversation
chatgpt.logical_session
interop.binding
```

PM may declare gateway responsibility for:

```text
interop.transport
```

Candidate PM capabilities continue to include ChatGPT operations such as:

```text
chatgpt.session.inspect
chatgpt.projects.list
chatgpt.project.inspect
chatgpt.project.create
chatgpt.project.rename
chatgpt.project.instructions.read
chatgpt.project.instructions.write
chatgpt.project.archive
chatgpt.conversations.list
chatgpt.conversation.inspect
chatgpt.conversation.create
chatgpt.conversation.send
chatgpt.conversation.rename
chatgpt.conversation.archive
chatgpt.logical_session.resolve
chatgpt.logical_session.send
chatgpt.lifecycle.rollover
```

New interop candidate capabilities should be PM-owned gateway semantics rather than Codex ownership claims:

```text
interop.delegation.forward
interop.result.deliver
```

Requirements:

- PM must publish capability metadata without becoming MCP solely for CME;
- capability identity must not depend on WebView2 vs Chrome provider mechanism;
- PM must be able to declare authoritative vs gateway responsibility domains;
- PM must not claim `codex.thread` / `codex.work` responsibility merely because it forwards to TM;
- PM interop invocation must be able to declare required opaque context references such as `principal_ref`, `authority_context_ref`, `delegation_id`, and binding/correlation references;
- PM may transport those references but must not validate or broaden their authority meaning;
- PM may declare the delivery/correlation/idempotency guarantees its gateway actually provides;
- PM may declare dependency on TM/Codex semantic capabilities without copying their ownership;
- browser/UI implementation details remain outside CME core semantics.

Not CME:

```text
Project/conversation registry schema
logical-session state machine
FIFO/provider-operation queue
DelegationTransaction runtime state machine
RESULT_UNKNOWN recovery implementation
browser/WebView ownership
ChatGPT rollover rollback
Service Hub PM UI
Human CEO / ChatGPT Headquarters hierarchy
```

---

# 4. TM requirements — updated

TM remains a CME 1.0 MCP consumer and also exposes its own local semantic control plane.

Candidate TM responsibility domains:

```text
codex.thread
codex.work
codex.execution
codex.runtime
```

Candidate capability groups continue to include:

```text
codex.runtime.inspect
codex.models.list
codex.capacity.read
codex.threads.list
codex.thread.inspect
codex.thread.ensure
codex.thread.resume
codex.thread.retire
codex.work.submit
codex.job.inspect
codex.job.cancel
codex.turn.start
codex.turn.steer
codex.turn.interrupt
codex.approval.inspect
codex.approval.respond
codex.recovery.reconcile
```

New direct-interoperation candidate semantics:

```text
codex.delegation.accept
codex.result.publish
```

Requirements:

- existing MCP Binding semantics remain representable;
- local provider declaration remains transport-neutral;
- TM must be able to declare canonical responsibility for Codex/work domains;
- CME declaration remains distinct from TM Authority, Invocation Principal validation, Resource Claims, scheduling, and effect enforcement;
- a TM invocation may declare that `principal_ref` / `authority_context_ref` are required without CME validating them;
- TM may declare idempotency/correlation/reconciliation guarantees exposed to a gateway consumer;
- a local binding must not create a weaker path around TM's governed effect boundary;
- material responsibility or delivery semantics must participate in compatibility/fingerprint evaluation.

Not CME:

```text
Authority Ledger
principal authentication/issuance
validity of authority_context_ref
operation_digest runtime enforcement
WorkState
claims/arbitration
job/recovery state machines
Codex App Server protocol
MAIN_DIRECTOR hierarchy
```

---

# 5. RT requirements

Standalone machine-callable RT may expose:

```text
resource.targets.list
resource.target.inspect
resource.current.read
resource.history.read
resource.baseline.compare
resource.diagnostic.capture
```

Candidate responsibility role is primarily:

```text
resource.telemetry → authoritative or observer according to the actual addressable product boundary
```

Requirements remain:

- local capability declaration without MCP dependency;
- externally callable observation capabilities only unless future evidence proves otherwise;
- diagnostic capture changes RT telemetry state, not target lifecycle;
- Embedded RT does not automatically become a separate provider.

Not CME:

```text
metric schema
sampling intervals
process identity
retention/database model
anomaly algorithms
sampler ownership
```

---

# 6. SB status and requirement impact

SB provided real evidence for transport-neutral CME, gateway non-transitivity, availability dependencies, and local semantic provider declaration.

The preferred ExecuteOffice architecture no longer requires SB for:

```text
Human CEO ↔ ChatGPT
ChatGPT ↔ Codex
```

Therefore SB remains valid predecessor/adopter evidence but MUST NOT force CME 2.0 to preserve a Slack-centered architecture.

If SB remains addressable during migration, its own capability responsibility stays limited to Slack semantics such as:

```text
slack.connection.inspect
slack.message.publish
slack.thread.reply
```

SB must not redeclare PM/TM capabilities as its own.

---

# 7. Shared requirements proven across adopters

## 7.1 Transport-neutral semantic core

Capability identity/effects/scope/availability must be expressible independently of MCP or local RPC transport.

## 7.2 Binding-specific metadata

CME Core defines meaning; bindings define representation/discovery of CME metadata.

Local Semantic Binding must not become one universal service RPC protocol.

## 7.3 Responsibility Domain

A provider needs a machine-readable way to declare whether it is:

```text
authoritative
gateway
observer
derived
```

for a semantic domain.

Responsibility declaration is provider assertion, not trust proof or organizational authority.

## 7.4 Responsibility Domain != Target Kind

```text
Responsibility Domain
→ who claims canonical semantic ownership

Target Kind
→ what object/resource a capability may affect
```

The two must remain independently representable.

## 7.5 Semantic invocation identity

CME 1.0 Tool→Capability binding generalizes to transport-neutral InvocationBinding.

Cross-service invocations may additionally need source/target responsibility-domain metadata.

## 7.6 Invocation Context Requirements

A capability must be able to declare required context kinds including, when applicable:

```text
principal_ref
authority_context_ref
delegation_id
request_id
source_binding_ref
target_binding_ref
correlation_ref
sequence_ref
```

CME declares required structure only.

CME does not validate organizational authority represented by those references.

## 7.7 Delivery / Correlation / Idempotency Declaration

A provider should be able to declare the actual safe-retry/recovery semantics it supports, including:

```text
request identity/idempotency support
result correlation requirement
ambiguous outcome possibility
reconciliation support
replay/duplicate-effect constraints
```

CME must not invent exactly-once guarantees or own consumer transaction state machines.

## 7.8 Service-to-Service semantic dependencies

A capability may depend on another provider capability/responsibility domain/binding compatibility without inheriting ownership of that dependency.

Dependency declaration must not become service discovery or trust pairing.

## 7.9 Non-transitive gateway ownership

If provider A calls provider B:

```text
A routes to B
!= A owns B capability
!= A owns B responsibility domain
```

A gateway declares its own gateway capability plus downstream dependency requirements.

## 7.10 Semantic fingerprint coverage

Responsibility role, required invocation-context kinds, material delivery guarantees, and material dependency requirements must participate in semantic compatibility/fingerprint evaluation when they change safe invocation meaning.

## 7.11 Existing Effect vocabulary remains adequate

No new Effect is currently justified by this responsibility/interoperation work.

The existing vocabulary already covers the real system impacts.

---

# 8. Explicitly rejected CME scope expansion

The following remain outside CME 2.0:

```text
Human CEO / company organization      → organization/authority standards
ChatGPT Headquarters hierarchy        → organization architecture
Authority Ledger / grants             → authority plane
principal authentication/issuance     → trust/authority/runtime owner
service discovery                     → ESHIC/product integration
pairing / trust administration        → trust architecture
Service Hub Introducer behavior       → ESHIC/trust architecture
service health/UI/settings            → ESHIC/domain adapter
AI result interpretation              → ChatGPT/Codex AI
PM/TM durable transaction states      → PM/TM
PM FIFO / RESULT_UNKNOWN handling     → PM
TM work/job/claim state               → TM
RT telemetry schema                   → RT
ChatGPT rollover/rollback              → PM
runtime wake/resume                    → owning runtime
```

CME may describe references/dependencies on these systems but does not own them.

---

# 9. Candidate CME 2.0 package update

Current recommended successor package:

```text
CME 2.0 Core
├─ provider identity
├─ responsibility domains
├─ capability model
├─ Effect vocabulary
├─ scope/target/platform requirements
├─ InvocationBinding
│  ├─ static/dynamic capability requirements
│  ├─ optional cross-service operation contract
│  ├─ invocation-context requirements
│  └─ delivery/correlation/idempotency declarations
├─ dependency/compatibility requirements
├─ Manifest / Status / Resolve semantics
├─ semantic fingerprints / hashing
└─ conformance

Bindings
├─ MCP Binding
└─ Local Semantic Binding
```

Still absent by design:

```text
Authority Plane
Service Registry
Trust/Pairing System
Universal RPC
Workflow Engine
Runtime Transaction Manager
```

---

# 10. Reverse-adoption requirement

Before CME 2.0 ratification, the Responsibility & Interop semantics must be reverse-applied to:

```text
PM
TM
one simple non-gateway provider
one dynamic MCP provider
```

Acceptance questions:

1. Can PM declare ChatGPT ownership and transport gateway behavior without claiming Codex state?
2. Can TM declare Codex ownership without importing ChatGPT state?
3. Can required authority/principal context be expressed without CME validating authority?
4. Can PM/TM describe replay/correlation guarantees without CME owning their runtime state machines?
5. Can a simple provider ignore irrelevant gateway fields cleanly?
6. Does an existing CME 1.0-style MCP provider retain natural capability semantics?

---

# 11. Requirement verdict

The second CME 2.0 requirement-harvest cycle is accepted as a candidate direction:

```text
Transport-neutral Core
+
Responsibility Domain
+
Cross-Service Invocation Contract
+
Invocation Context Requirements
+
Delivery/Correlation/Idempotency Declaration
+
Non-transitive Dependency/Compatibility Declaration
```

with the hard invariant:

> CME describes machine capability and invocation semantics. It does not become the system that grants authority, establishes trust, decides AI intent, or executes recovery state machines.

CME 1.0 remains ratified authority until a future CME 2.0 ratification gate explicitly closes.
