# CME Ecosystem Requirement Matrix v0.1

**Status:** E2E REVIEW INPUT / UNLOCKED  
**Source adopters:** PM, RT, TM, SB  
**Purpose:** derive the smallest useful CME successor from real ecosystem needs without rewriting ratified CME 1.0.

---

# 1. Executive conclusion

The current ecosystem does **not** reveal a fundamental problem with CME 1.0's capability/effect model.

The material shared gap is **transport coupling**.

CME 1.0 was designed as an MCP extension and remains a strong fit for MCP providers. PM, RT, TM, and SB now expose or will expose local semantic control surfaces that need the same machine-readable capability semantics without being forced to pretend that every local service is an MCP server.

Therefore the leading successor requirement is:

> preserve CME capability semantics, separate them from the transport binding, and add a small Local Semantic Binding alongside the existing MCP Binding.

---

# 2. Constraints inherited from CME 1.0

A successor must preserve:

```text
capability != authority
SUPPORTED != AVAILABLE != AUTHORIZED != PLATFORM-APPROVED
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

# 3. PM requirements

PM exposes ChatGPT product operations over a local semantic control contract.

Candidate capabilities:

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

Requirements:

- PM must be able to publish capability metadata without running an MCP server solely for CME.
- capability identity must not depend on WebView2 vs Chrome provider mechanism;
- availability may change with authentication, UI drift, provider state, or supported product operations;
- mutation arguments may change required scope/effects and must support dynamic resolution where material;
- browser/UI implementation details must not enter CME core semantics.

Not CME:

```text
Project/conversation registry schema
logical-session state machine
browser/WebView ownership
Service Hub PM UI
Slack routing
```

---

# 4. RT requirements

Standalone machine-callable RT may expose:

```text
resource.targets.list
resource.target.inspect
resource.current.read
resource.history.read
resource.baseline.compare
resource.diagnostic.capture
```

Requirements:

- local capability declaration without MCP dependency;
- externally callable observation capabilities only;
- diagnostic capture changes RT telemetry state, not observed-target lifecycle;
- Embedded RT does not automatically become a separate provider; a hosting service may expose RT-derived capabilities in its own provider manifest.

Not CME:

```text
RT metric schema
sampling intervals
process identity
retention/database model
anomaly algorithms
sampler ownership
```

---

# 5. TM requirements

TM remains a CME 1.0 MCP consumer and must preserve existing behavior.

TM also needs a machine-readable declaration for its own local semantic control plane.

Candidate capability groups:

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

Requirements:

- existing MCP Binding semantics must remain representable;
- local semantic provider declaration must be transport-neutral;
- CME declaration must remain distinct from TM Authority and Resource Claims;
- invocation arguments may materially change effects/authority requirements;
- a local binding must not create a weaker route around TM's existing governed effect path.

Not CME:

```text
Authority Ledger
Invocation Principal
operation_digest
WorkState
claims/arbitration
job/recovery state machines
Codex App Server protocol
```

---

# 6. SB requirements

After PM/TM separation, SB owns only Slack capabilities.

Candidate capabilities:

```text
slack.connection.inspect
slack.message.publish
slack.thread.reply
```

Requirements:

- SB may declare its own Slack-facing capabilities;
- SB must not redeclare PM `chatgpt.*` or TM `codex.*` capabilities as if SB owns them;
- gateway/routing does not imply transitive capability ownership;
- transport/client dependency status belongs to SB domain status, not capability inheritance.

Not CME:

```text
Slack channel/user allowlists
Slack thread correlation schema
Slack message formatting
PM/TM routing policy
Service Hub bridge status presentation
```

---

# 7. Shared requirements proven across adopters

## 7.1 Transport-neutral semantic core

Capability identity/effects/scope/availability must be expressible independently of MCP or local RPC transport.

## 7.2 Binding-specific discovery/invocation metadata

MCP-specific extension negotiation and Tool binding should remain in an MCP binding document/layer.

Local semantic services need a local binding that identifies how a client obtains:

```text
Manifest
Status
Resolve
```

The local binding should not standardize the entire service RPC protocol.

## 7.3 Core version vs binding version

A provider should be able to declare:

```text
CME core version
binding kind
binding version
```

so a future binding update does not pretend core capability semantics changed.

## 7.4 Semantic invocation identity

CME 1.0 Tool→Capability binding generalizes to:

```text
invocation operation identity
→ static requirements
or
→ dynamic Resolve(arguments)
```

The MCP Binding maps Tool names to this model. A Local Semantic Binding maps provider semantic operation IDs to the same core model.

## 7.5 Non-transitive gateway ownership

If provider A calls provider B, A does not automatically own B's capabilities.

A gateway may expose a distinct high-level capability if it truly provides and owns that semantic operation, but must not copy downstream capability identity merely because it routes the call.

## 7.6 Provider support and current availability

All four adopters need CME 1.0's distinction between stable supported semantics and current availability.

## 7.7 Existing Effect vocabulary remains adequate

No new Effect is currently proven necessary by PM/RT/TM/SB.

The existing categories already cover:

```text
data read/create/modify/delete
code read/modify/execute
process execute/elevated
ui observe/control
network
credential
system/security/device
external communicate/publish/transact
```

Do not add `chatgpt.*`, `slack.*`, `codex.*`, or `resource.*` as Effects. Those are provider capabilities/domains, not cross-provider effects.

---

# 8. Explicitly rejected CME scope expansion

The following belong elsewhere and must not enter CME 2 merely because the ecosystem uses them:

```text
Service discovery          → ESHIC/product integration
service health schema      → ESHIC/domain adapter
settings/UI schema         → ESHIC
operator action rendering  → ESHIC
organization authority     → TM/authority plane
approval/delegation ledger → authority plane
RT telemetry schema        → RT
PM Project registry        → PM
TM work/job state          → TM
SB Slack correlation       → SB
```

---

# 9. Migration requirement

CME 2 must not claim that 1.0 and 2.0 hashes/fingerprints are byte-identical merely because semantics are compatible.

A migration/conformance record should prove semantic equivalence for inherited fields/rules.

CME 1.0 provider implementations remain valid 1.0 providers. Consumers may support both versions during migration.

---

# 10. Requirement verdict

Recommended successor shape:

```text
CME 2.0 Core
├─ provider identity
├─ capability model
├─ Effect vocabulary
├─ scope/target/platform requirements
├─ Manifest / Status / Resolve semantics
├─ semantic fingerprints / hashing
└─ conformance

Bindings
├─ MCP Binding
└─ Local Semantic Binding
```

No authority plane. No Hub/UI semantics. No telemetry schema. No speculative Effect expansion.

This is the smallest shared change currently justified by real adopters.
