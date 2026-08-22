# CME 2.0 — Capability Manifest Extension
## Transport-Neutral Core + Bindings Blueprint

**Document ID:** `CME-2.0-BLUEPRINT-E2E-DRAFT`  
**Status:** `E2E REVIEW DRAFT / UNLOCKED / NOT RATIFIED`  
**Current ratified authority:** CME 1.0  
**Design basis:** real PM/RT/TM/SB adopter requirements  
**Principle:** operational-fit first

---

# 0. Executive definition

CME 2.0 is a candidate evolution of CME 1.0 that separates **capability semantics** from a particular protocol transport.

CME 1.0 proved the core model in MCP:

```text
provider
→ capabilities
→ Effects
→ invocation requirements
→ availability
```

The ExecuteOffice ecosystem now has local semantic providers—PM, TM, SB, and optionally standalone RT—that need the same machine-readable declaration without being forced to run MCP solely to carry CME metadata.

The candidate 2.0 architecture is therefore:

```text
CME 2.0 Core
      │
      ├─ MCP Binding
      └─ Local Semantic Binding
```

The Core defines meaning. Bindings define how that meaning is discovered/resolved in a transport environment.

---

# 1. Compatibility posture

CME 1.0 remains ratified and historically stable.

This draft MUST NOT be read as permission to rewrite `specification/1.0/CME_1.0_BLUEPRINT.md`.

A future ratified 2.0 should preserve the semantic intent of mature 1.0 rules unless a concrete adopter requirement justifies a versioned breaking change.

CME 2.0 does not make 1.0 providers invalid. During migration consumers MAY support both.

---

# 2. Scope

CME 2.0 Core SHOULD define:

1. provider identity;
2. provider-scoped capability identity;
3. common Effects;
4. scope kinds;
5. target kinds;
6. platform requirements;
7. supported capability manifest;
8. current availability status;
9. static invocation requirements;
10. dynamic invocation resolution;
11. manifest revision/hash;
12. capability semantic fingerprints;
13. profile snapshot semantics;
14. conformance behavior;
15. version/binding negotiation semantics.

Bindings define transport-specific representation/discovery.

---

# 3. Non-scope

CME 2.0 MUST NOT define:

- user/organization authority;
- grants/delegation;
- operator approval workflow;
- provider credentials;
- OAuth/UAC/sandbox bypass;
- Service Hub discovery/UI/settings schema;
- service domain health;
- resource telemetry schemas;
- provider business-state schemas;
- one universal RPC protocol;
- process/service lifecycle management.

Capability declaration remains distinct from permission.

---

# 4. Core invariants

A conforming design preserves:

```text
Capability != Authority
Availability != Authorization
Provider declaration != trust proof
Platform requirement != organizational approval
Gateway routing != downstream capability ownership
Binding transport != capability semantics
```

States remain independently representable:

```text
SUPPORTED
AVAILABLE
AUTHORIZED       # external to CME
PLATFORM-APPROVED # external platform state
```

CME itself owns only the first two and metadata needed to reason about execution requirements.

---

# 5. Core object model

Conceptually:

```text
ProviderDeclaration
├─ cme
│  ├─ core_version
│  ├─ binding_kind
│  └─ binding_version
├─ provider
├─ manifest_revision
├─ manifest_hash
├─ capabilities[]
├─ profiles[] optional
└─ invocation_bindings[]

ProviderStatus
├─ provider identity/ref
├─ manifest hash/revision
└─ capability availability[]

Resolution
├─ invocation identity
├─ arguments_hash
├─ manifest_hash
├─ required capabilities
├─ effective Effects/scope/platform requirements
└─ resolution_fingerprint
```

Wire shapes remain binding/version-specific.

---

# 6. Provider identity

Core provider identity continues to require stable logical identity and running version/build evidence.

Conceptual fields:

```text
provider.id
provider.title
provider.version
provider.instance_id optional
```

`provider.id` identifies the logical provider.

`provider.instance_id` MAY distinguish concurrent local instances but MUST NOT replace stable provider identity.

Provider identity remains discovery identity, not trust proof.

---

# 7. Capability identity

Capability IDs remain provider-scoped.

Examples:

```text
chatgpt.conversation.send
codex.turn.interrupt
slack.message.publish
resource.current.read
workbook.write
```

These examples are capabilities, **not Effects**.

An existing capability ID MUST NOT silently gain materially broader Effects or authority-relevant execution semantics.

If semantics broaden materially, the provider must version/replace capability identity according to compatibility rules.

---

# 8. Effect vocabulary

CME 2.0 candidate retains the proven CME 1.0 vocabulary unless future real use demonstrates a gap:

```text
data.read
data.create
data.modify
data.delete

code.read
code.modify
code.execute

process.execute
process.elevated

ui.observe
ui.control

network.access
credential.use
system.configure
security.change
device.control

external.communicate
external.publish
external.transact
```

No new Effect is currently justified by PM/RT/TM/SB.

`ui.observe` and `ui.control` describe execution effects involving a user interface. They do not define Service Hub rendering semantics and do not overlap ESHIC UI schema ownership.

---

# 9. Scope and target kinds

Core retains scope/target semantics because authority consumers need to know what a capability can meaningfully affect.

Examples of reusable scope kinds:

```text
host
project
workspace
path
document
workbook
resource
account
domain
service
```

Providers MAY define namespaced target kinds such as:

```text
chatgpt.project
chatgpt.conversation
codex.thread
slack.channel
filesystem.path
excel.workbook
```

CME declares possible semantic targeting. It does not grant access to the target.

---

# 10. Platform requirements

Core retains platform/environment requirements such as:

```text
interactive_session
gui_session
network
credential_context
os_elevation
host_confirmation_may_be_required
```

A provider may declare additional namespaced requirements when necessary.

Consumer authorization MUST NOT be interpreted as bypassing them.

---

# 11. Manifest

Manifest describes what the provider **supports semantically**, regardless of whether each capability is currently usable.

Manifest contains:

- provider identity;
- CME core/binding version;
- capabilities and semantics;
- invocation binding metadata;
- optional profiles;
- revision/hash.

Temporarily unavailable capabilities remain in the manifest.

A provider MUST NOT delete a supported capability from manifest merely because authentication/network/host state temporarily makes it unavailable.

---

# 12. Status

Status describes **current availability**.

Core availability states remain conceptually:

```text
available
unavailable
degraded
unknown
```

Availability entry SHOULD have stable machine-readable reason information where practical.

Examples:

```text
AUTH_REQUIRED
PROVIDER_UI_DRIFT
HOST_UNAVAILABLE
DEPENDENCY_MISSING
PLATFORM_CONTEXT_MISSING
```

Reason vocabulary may be provider/binding namespaced; `unknown` is preferable to fabricated certainty.

---

# 13. Invocation binding

CME 1.0 binds MCP Tools to capabilities. CME 2 Core generalizes this into **invocation identities**.

Concept:

```text
InvocationBinding
- invocation_id
- requirement_mode = static | dynamic
- static_requirements[] when static
- resolver identity/method when dynamic
```

The binding layer maps concrete transport operations to `invocation_id`.

Examples:

```text
MCP Tool `send_message`
→ invocation_id = chatgpt.conversation.send

Local RPC operation `SendMessage`
→ invocation_id = chatgpt.conversation.send
```

The same provider capability semantics can therefore survive a transport change.

---

# 14. Static requirements

Static invocation requirements apply when arguments cannot materially change authority/effect semantics.

A static binding identifies required capability IDs and any fixed effect/scope constraints.

If an argument can switch read→write, local→publish, preview→commit, standard→elevated, harmless→destructive, the operation is not safely static for that distinction.

---

# 15. Dynamic Resolve

Dynamic Resolve determines the actual capability/effect/scope/platform requirements for concrete invocation arguments.

Resolution MUST be bound to:

```text
provider identity
manifest revision/hash
invocation identity
canonical arguments hash
resolved requirement set
resolution fingerprint
```

Changed material arguments require re-resolution.

Execution must not use stale resolution evidence when manifest semantics or arguments changed.

---

# 16. Hashing and semantic fingerprints

CME 1.0 uses RFC 8785 JSON canonicalization + SHA-256.

The 2.0 candidate SHOULD retain deterministic canonicalization and SHA-256 for equivalent artifact classes unless binding-independent normalization requires a documented versioned canonical form.

Important migration rule:

> Semantic compatibility does not imply 1.0 and 2.0 hashes are byte-identical.

A 2.0 migration must define its own canonical inputs and produce a semantic-equivalence/conversion record where 1.0 evidence is imported.

Capability semantic fingerprints continue to include authority-relevant semantics such as:

```text
capability id
Effects
scope kinds
target kinds
platform requirements
normative execution constraints
```

Display-only changes do not silently alter authority meaning.

---

# 17. No Silent Semantic Expansion

The CME 1.0 rule remains a hard invariant.

Example:

```text
old capability Effects = data.modify
new behavior = data.modify + code.execute
```

The provider cannot leave every old grant snapshot semantically unchanged just because the capability string stayed the same.

A versioned semantic/fingerprint change must force consumer re-evaluation according to its authority policy.

---

# 18. Profiles

Profiles remain UI/convenience groupings, not future-dynamic grants.

When a consumer persists an authorization decision based on a profile, it should snapshot the explicit capability set/fingerprints relevant at approval time.

New future capabilities are not automatically included in an old `all`-style grant snapshot.

CME itself still does not store the grant.

---

# 19. Core version vs binding version

Candidate declaration:

```text
core_version = 2.0
binding_kind = mcp | local-semantic | <future>
binding_version = binding-specific
```

Benefits:

- core Effect/capability semantics may remain stable while MCP negotiation changes;
- Local Semantic Binding may evolve without pretending provider Effects changed;
- a consumer can explicitly support one core version and selected bindings.

Binding kind/version are compatibility metadata, not provider capabilities.

---

# 20. MCP Binding

The MCP Binding is the successor representation for existing CME 1.0 MCP behavior.

It should preserve:

```text
optional MCP extension negotiation
manifest/status/resolve request surface
tool → invocation/capability binding
MCP-native provider identity/version context
legacy client usability
```

A CME-enabled MCP provider remains usable by ordinary non-CME MCP clients unless it independently adds an authorization requirement.

The binding must not import Thread Manager authority or organization concepts.

---

# 21. Local Semantic Binding

Local Semantic Binding exists for services such as PM/TM/SB/standalone RT.

It standardizes only how a trusted client discovers CME metadata operations for an already-discovered local provider.

Minimum logical metadata operations:

```text
GetCmeManifest
GetCmeStatus
ResolveCmeInvocation
```

Exact transport paths/method names MAY be implementation-specific or described by a small binding descriptor.

The binding does **not** define the provider's ordinary domain RPC methods.

For example ESHIC/service registration may already tell a client where PM is; the CME local binding then tells a capability-aware client how to obtain PM's CME declaration. CME itself need not discover PM's process/service installation.

---

# 22. Discovery boundary

CME answers:

> Once I have a provider/control endpoint, what machine capabilities does it declare and what is available?

ESHIC or product-specific registration answers:

> What services are installed/available to integrate and how do I bind to them?

These remain separate.

CME MUST NOT grow a Service Hub service registry merely to support local binding.

---

# 23. Gateway non-transitivity

Suppose:

```text
Slack → SB → PM → ChatGPT
```

SB owns Slack gateway capabilities. PM owns ChatGPT capabilities.

SB does not automatically publish:

```text
chatgpt.conversation.send
```

as an SB-owned capability simply because it can route a request to PM.

If SB someday owns a distinct high-level semantic operation such as `briefing.ask`, that may be an SB capability with its own Effects, but downstream PM capability identity remains provider-owned.

This prevents capability catalogs from becoming transitive copies of a service graph.

---

# 24. Hosted/embedded capability boundary

An embedded library/component does not automatically become a separate CME provider.

Example:

```text
PM includes Embedded RT
```

Options:

1. PM exposes PM-owned `diagnostics.resource.*` capabilities using RT internally; or
2. a separately addressable standalone RT collector declares its own provider identity.

Choose based on the actual external control boundary, not the implementation module graph.

---

# 25. Availability dependencies

A capability may be supported but unavailable because of a downstream dependency.

Example PM:

```text
chatgpt.conversation.send
SUPPORTED = yes
AVAILABLE = no
reason = AUTH_REQUIRED
```

CME status can expose that fact without declaring ChatGPT as a separate provider owned by PM or granting login credentials.

Availability reason does not become authority.

---

# 26. Provider trust

CME remains a provider assertion surface.

Consumers may combine declaration with:

```text
provider binary/source identity
signing/publisher evidence
local configuration trust
transport authentication
adoption/conformance evidence
```

but those trust systems remain external to CME semantics.

A provider cannot self-label `risk_hint=low` and lower consumer policy.

---

# 27. Conformance classes — candidate

Core candidates:

```text
CME-2-PROVIDER-STATIC
CME-2-PROVIDER-DYNAMIC
CME-2-CONSUMER
```

Binding qualifiers:

```text
MCP-BINDING
LOCAL-SEMANTIC-BINDING
```

Examples:

```text
PM = CME-2-PROVIDER-DYNAMIC + LOCAL-SEMANTIC-BINDING
TM = CME-2-CONSUMER + MCP-BINDING
   + optionally CME-2-PROVIDER-DYNAMIC + LOCAL-SEMANTIC-BINDING
```

Conformance should not require an adopter to implement unused bindings.

---

# 28. PM adoption candidate

PM declaration should cover only actual PM public semantic operations.

Example conceptual entry:

```yaml
capability:
  id: chatgpt.conversation.send
  effects:
    - external.communicate
    - ui.control
  platform_requirements:
    - interactive_session
    - gui_session
    - network
    - credential_context
```

Exact effects must reflect actual provider implementation. If a future official provider sends without UI actuation, the provider capability semantics/fingerprint may differ and must be represented honestly.

---

# 29. RT adoption candidate

Standalone RT read capabilities are primarily `data.read`.

A diagnostic-capture operation modifies RT telemetry state/history but MUST NOT claim a target process-control capability.

No `process.execute`/`system.configure` Effect is added merely because RT observes a process.

---

# 30. TM adoption candidate

TM keeps consuming CME from MCP providers exactly as its existing authority/capability architecture requires.

TM's own local provider declaration is separate from TM Authority.

Example:

```text
codex.turn.start capability exists
!= caller authorized to start privileged work
```

TM's semantic operation may dynamically resolve additional Effects/capabilities based on work/arguments, but the actual governed execution remains TM-owned.

---

# 31. SB adoption candidate

SB's manifest should remain deliberately small after PM/TM split:

```text
slack.connection.inspect
slack.message.publish
slack.thread.reply
```

Its status may say PM/TM route dependencies are unavailable, but it does not duplicate their catalogs.

---

# 32. Relationship to ESHIC

CME:

```text
machine capability
effect
availability
invocation requirement
```

ESHIC:

```text
service discovery/status/data/settings
human-facing actions
operator presentation semantics
```

A human-facing ESHIC action MAY carry a `capability_ref` to a CME capability.

That reference means the action corresponds to a machine-declared capability. It does not import CME rendering, does not make CME required for all ESHIC services, and does not grant authority.

---

# 33. Error and compatibility behavior

A consumer encountering:

```text
unsupported core version
unsupported binding
invalid manifest hash
unknown mandatory Effect
stale dynamic resolution
provider identity mismatch
```

must not silently downgrade into a falsely verified capability decision.

It may:

```text
reject governed invocation
fall back to UNVERIFIED legacy inference when explicitly supported/safe
surface capability unavailable/incompatible
```

according to consumer policy.

---

# 34. Migration from 1.0

A future 2.0 migration package should contain:

1. 1.0→2.0 field/semantic mapping;
2. Effect vocabulary equivalence table;
3. manifest/status/resolve mapping;
4. Tool binding→InvocationBinding mapping;
5. hashing/canonicalization migration rules;
6. consumer dual-version behavior;
7. provider migration examples;
8. negative cases preventing accidental authority widening.

Do not silently rewrite historical 1.0 artifacts.

---

# 35. Candidate acceptance matrix

```text
CME2-01 provider-scoped IDs preserved
CME2-02 Effect vocabulary preservation
CME2-03 capability != authority
CME2-04 support != availability
CME2-05 platform requirements preserved
CME2-06 static invocation binding
CME2-07 dynamic Resolve
CME2-08 argument hash freshness
CME2-09 manifest freshness
CME2-10 semantic fingerprint stability
CME2-11 no silent expansion
CME2-12 profile snapshot safety
CME2-13 unknown Effect conservative
CME2-14 core/binding version separation
CME2-15 MCP Binding legacy compatibility
CME2-16 Local Semantic Binding metadata access
CME2-17 no service discovery leakage
CME2-18 no ESHIC UI leakage
CME2-19 gateway non-transitivity
CME2-20 embedded component provider rule
CME2-21 PM adoption
CME2-22 RT adoption
CME2-23 TM 1.0 consumer compatibility
CME2-24 TM local-provider adoption
CME2-25 SB minimal adoption
CME2-26 authority systems remain external
```

---

# 36. Ratification gate

This draft MUST NOT become ratified CME authority until:

1. PM/RT/TM/SB detailed Blueprints are reviewed;
2. the ecosystem requirement matrix is accepted;
3. MCP 1.0 semantics are mapped losslessly enough for supported behavior;
4. Local Semantic Binding is proven useful without becoming a new generic RPC framework;
5. all four reference adopters can map without product distortion;
6. TM's existing CME 1.0 consumer behavior remains compatible;
7. ESHIC boundary is independently reviewed;
8. reverse adoption finds no material capability/authority ambiguity;
9. conformance/migration requirements are clear.

Until then:

```text
CME 1.0 = RATIFIED AUTHORITY
CME 2.0 = E2E REVIEW DRAFT / UNLOCKED
```

---

# 37. Definition of success

CME 2 succeeds if:

```text
Excel MCP can still declare capabilities naturally.
TM can still consume them safely.
PM can declare ChatGPT-control capabilities without becoming an MCP server just for metadata.
SB can declare Slack capabilities without inheriting PM/TM catalogs.
RT can expose observation capabilities without becoming a process manager.
A capability has the same semantic meaning regardless of the binding used to carry the declaration.
No provider declaration grants authority.
No Service Hub/UI semantics leak into CME.
```

That is the smallest versioned evolution currently justified by actual use.
