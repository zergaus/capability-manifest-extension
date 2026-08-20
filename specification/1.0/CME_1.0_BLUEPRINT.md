# CME 1.0 — Capability Manifest Extension
## Blueprint, History and Normative Standard

**Document ID:** CME-1.0-BLUEPRINT  
**Status:** RATIFIED — INTERNAL STANDARD / EXTERNAL-QUALITY EXPERIMENTAL EXTENSION  
**Standard Version:** 1.0  
**Native MCP Target:** MCP 2026-07-28  
**Owner System:** Thread Manager ecosystem  
**Purpose:** MCP capability declaration and discovery  
**Not Purpose:** authorization, permission granting, delegation, credential bypass, platform-security bypass  
**Ratification Basis:** Human CEO decision, 2026-08-15

---

# 0. Executive Definition

CME, the **Capability Manifest Extension**, is the standard language used by MCP providers to tell Thread Manager:

> “These are the operations I can perform, these are their real effects, these tools require those capabilities, and this is what is actually available on the current host.”

CME itself does **not** grant permission, delegate authority, replace MCP authorization, replace OAuth scopes, bypass Windows UAC, bypass Codex approvals, bypass sandboxing, or supply credentials.

The relationship is:

```text
MCP Provider
    │
    │ CME 1.0
    │ declares capabilities
    ▼
Thread Manager
    │
    ├─ Dynamic Capability Registry
    │    "What can this host do?"
    │
    ├─ Authority Ledger
    │    "What has the Human CEO delegated?"
    │
    ├─ Authority Broker
    │    "Is this action inside the delegation?"
    │
    └─ Capability Router
         "Which provider should execute it?"
```

The primary operational objective is:

> **Already-delegated organizational authority must not repeatedly become a new Human CEO approval request merely because a subordinate agent chooses a different MCP/tool or because the MCP internally uses Excel, VBA, PowerShell, Computer Use, DAX, Power Pivot, report rendering, or another declared mechanism.**

A real platform/OS/security permission may still be required.

---

# 1. Why CME Exists — Design History

## 1.1 Thread Manager began as an organization/thread system

The initial Thread Manager model focused on persistent organizational roles:

- MAIN_DIRECTOR
- MEETING_FACILITATOR
- PRODUCT_EXPERIENCE
- ARCHITECTURE_PLATFORM
- IMPLEMENTATION_FACTORY
- QA_RELEASE

Operational use showed that the Human CEO normally needs direct visibility into only `MAIN_DIRECTOR` and `MEETING_FACILITATOR`. The remaining permanent functional leads are better modeled as persistent backstage workers.

This produced the principle:

> **Persistence is not visibility.**

## 1.2 Hiding backstage roles exposed a more important problem

Backstage directors frequently encounter actions they believe require permission, especially:

- Computer Use
- Microsoft Excel
- PowerShell
- administrator/elevated PowerShell
- code/file changes
- tests/builds
- specialized MCP use

Even after the Human CEO explicitly told MAIN_DIRECTOR to proceed with broad or full project authority, subordinate directors could still ask the Human CEO for permission.

The root problem was not visibility. Delegation was conversational and local to a thread rather than machine-readable, persistent, inheritable authority.

This produced the need for a separate **Authority Plane** in Thread Manager.

## 1.3 Authority Plane alone was not enough

A stable authority system needs two independent facts:

```text
AUTHORITY
= Is this action allowed?

CAPABILITY
= Can the current host/provider perform this action?
```

These are not the same.

A project can contain a standing CEO grant for Excel automation while the current computer has no Excel MCP installed. Conversely, a powerful MCP can be installed while the Main Director has no authority to use a high-impact function.

Therefore:

> **AUTHORIZED ≠ AVAILABLE**

Both states must be represented independently.

## 1.4 Excel MCP changed the scale of the problem

The Excel MCP under development is not merely a workbook read/write adapter. Its intended surface can include workbook operations, formulas, formatting, VBA, PowerShell, Power Query, Power Pivot/Data Model, DAX, Pivot, charts, external data, native Excel automation, and supporting automation around the Excel ecosystem.

That makes a single permission such as:

```text
excel_mcp = allowed
```

unsafe as a general authority model. A single provider may reach many different execution effects.

The correct model is:

```text
Provider capability
        +
real execution Effects
        +
Tool → Capability binding
```

## 1.5 Reporting Engine confirmed that providers are optional and heterogeneous

The Reporting Engine MCP has a very different capability surface: plan, create, validate, compare, history/artifact access. A computer is not guaranteed to have the Reporting Engine, Excel MCP, or any future MCP installed.

Therefore Thread Manager must not hard-code provider names as its authority model.

Instead:

> **Each provider declares its own capabilities. Thread Manager dynamically discovers and normalizes them.**

This realization produced CME 1.0.

---

# 2. Scope

## 2.1 CME 1.0 MUST do

CME MUST provide a machine-readable way for an MCP provider to declare:

1. provider identity;
2. provider-supported capabilities;
3. common execution Effects for each capability;
4. stable capability semantics;
5. optional UI metadata;
6. optional capability profiles/presets;
7. static Tool → Capability requirements;
8. dynamic Tool → Capability resolution when arguments change required authority;
9. current-host capability availability;
10. platform requirements beyond organizational authority;
11. manifest revision/hash;
12. capability semantic fingerprints.

## 2.2 CME 1.0 MUST NOT do

CME MUST NOT:

- grant authority;
- record CEO delegation;
- decide whether a user approved an operation;
- issue organization-level approval;
- bypass Codex approval controls;
- bypass Windows UAC;
- bypass sandbox restrictions;
- supply credentials;
- authorize OAuth scopes;
- determine final provider trust;
- automatically approve a capability because the provider labels it safe;
- implement Thread Manager organizational hierarchy.

---

# 3. System Boundaries

```text
CME
= capability declaration standard

Thread Manager Dynamic Capability Registry
= discovered provider/capability inventory

Thread Manager Authority Ledger
= persisted Human CEO and delegated grants

Thread Manager Authority Broker
= effective authority decision

Thread Manager Capability Router
= provider selection

MCP Core / OAuth / Host / OS
= protocol and platform authorization/security
```

A conforming implementation MUST preserve these boundaries.

---

# 4. Normative Language and Status

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are normative requirements in this document.

CME 1.0 is a private/internal standard designed at external-publication quality. It MAY later be published or proposed as a third-party MCP extension, but it is not an official MCP Core standard.

---

# 5. MCP Compatibility

## 5.1 Native target

CME 1.0 targets **MCP 2026-07-28** as its native protocol generation and uses the formal optional extension mechanism.

The provider's normal MCP tools MUST remain usable by non-CME clients unless that provider independently requires another authorization mechanism.

CME is additive.

## 5.2 Extension identity

Logical extension name:

```text
capability-manifest
```

### Development/private wire identifier

Until a Human CEO-controlled public vendor namespace is selected, private deployments SHALL use:

```text
local.cme/capability-manifest
```

This is a **development/private identifier only**. It MUST NOT be represented as a globally owned public namespace.

### Future public identifier

Before external publication/distribution, replace the vendor prefix with a reverse-domain prefix controlled by the owner:

```text
<owned.reverse.domain>/capability-manifest
```

Implementations MUST centralize the extension identifier in one constant/configuration point and SHOULD support configured aliases for a one-release migration.

---

# 6. Versioning

CME versioning is independent from the MCP protocol version.

CME 1.0 uses semantic-version intent. Compatible additions MAY occur within 1.x if they do not change existing semantics.

The following require a major revision or explicitly breaking contract revision:

- removing/renaming required fields;
- incompatible field-type changes;
- broadening existing capability meaning;
- changing existing Effect meaning;
- adding new required fields;
- incompatible changes to grant-sensitive fingerprint rules.

---

# 7. Core Concept Model

```text
Provider
  └─ Manifest
       ├─ Capabilities
       │    ├─ Effects
       │    ├─ Scope kinds
       │    ├─ Target kinds
       │    └─ Platform requirements
       │
       ├─ Profiles
       │
       └─ Tool bindings
            ├─ static
            └─ dynamic

Host runtime
  └─ Availability Status

Consumer
  └─ Manifest + Status + Tool Resolution
```

---

# 8. Provider Identity

Every manifest MUST contain provider identity:

```json
{
  "provider": {
    "id": "excel-mcp",
    "title": "Excel MCP",
    "version": "1.4.0"
  }
}
```

Rules:

- `provider.id` MUST be stable for the logical provider.
- `provider.version` SHOULD identify the running provider build/version.
- Provider identity does not establish trust by itself.
- Thread Manager trust policy is external to CME.

---

# 9. Capability Identity

## 9.1 Provider-scoped capability IDs

Capability IDs are scoped to the provider manifest.

Examples:

```text
workbook.read
workbook.write
vba.execute
powerpivot.modify
report.create
report.validate
```

A capability ID MUST be stable once released and MUST NOT silently acquire materially broader Effects.

## 9.2 Capability versus Effect

Capability answers:

> “What provider function is this?”

Effect answers:

> “What kind of real-world/system effect can this function cause?”

Example:

```text
Capability:
vba.execute

Effects:
code.execute
data.modify
```

Thread Manager SHOULD reason from Effects for cross-provider policy while preserving provider-specific capability identity.

---

# 10. CME 1.0 Standard Effect Vocabulary

The following Effect IDs are normative in CME 1.0.

## Data

```text
data.read
data.create
data.modify
data.delete
```

## Code

```text
code.read
code.modify
code.execute
```

## Process

```text
process.execute
process.elevated
```

## User Interface

```text
ui.observe
ui.control
```

## Network / Credentials / System

```text
network.access
credential.use
system.configure
security.change
device.control
```

## External impact

```text
external.communicate
external.publish
external.transact
```

---

# 11. Effect Semantics

- `data.read` — reads data without intentionally mutating the authoritative target.
- `data.create` — creates new persistent/reportable data or artifacts.
- `data.modify` — changes existing data or persistent content.
- `data.delete` — deletes or irreversibly removes data/content.
- `code.read` — reads source/script/formula/macro/executable logic.
- `code.modify` — changes executable logic.
- `code.execute` — executes code or user/provider-supplied logic.
- `process.execute` — starts or controls a process/script runtime.
- `process.elevated` — executes with elevated/administrator-equivalent OS privileges.
- `ui.observe` — observes rendered UI/screen/application state.
- `ui.control` — controls UI by pointer, keyboard, automation, or equivalent interaction.
- `network.access` — communicates beyond a closed local target/environment.
- `credential.use` — uses a credential, token, key, authenticated session, or equivalent identity-bearing authority.
- `system.configure` — changes host/OS/application configuration beyond ordinary target-document data.
- `security.change` — changes security controls, authorization policy, permission state, trust configuration, or protection boundaries.
- `device.control` — controls hardware or a physical interface.
- `external.communicate` — sends communication to an external person/system.
- `external.publish` — publishes/exposes content externally.
- `external.transact` — creates a financial, contractual, purchase, sale, or materially binding external transaction.

---

# 12. Unknown Effects

Providers MUST NOT invent an unnamespaced Effect ID and present it as a CME-standard Effect.

If a provider requires a non-standard Effect before CME vocabulary revision, it MUST use a provider/vendor-qualified custom Effect namespace and mark it non-standard.

Thread Manager SHOULD treat unknown Effects conservatively. Unknown high-impact Effects MUST NOT become automatically delegated merely because a parent capability is granted.

---

# 13. Risk Hints

A capability MAY declare:

```text
risk_hint:
  low
  medium
  high
  critical
```

`risk_hint` is advisory only and MUST NOT be treated as authoritative security policy.

Recommended Thread Manager behavior:

```text
Effective risk
=
max(
  provider risk hint,
  Thread Manager effect-policy floor,
  local trust policy
)
```

A provider claiming `low` MUST NOT downgrade a stronger consumer classification.

---

# 14. Recommended Thread Manager Effect Risk Floors

This section is a **Thread Manager Consumer Profile**, not CME authority.

Suggested floors:

### LOW

```text
data.read
code.read
ui.observe
```

### MEDIUM

```text
data.create
data.modify
ui.control
network.access
```

### HIGH

```text
data.delete
code.modify
code.execute
process.execute
credential.use
external.communicate
external.publish
```

### CRITICAL

```text
process.elevated
system.configure
security.change
device.control
external.transact
```

Thread Manager MAY apply stricter policy.

---

# 15. Capability Object

Representative shape:

```json
{
  "id": "vba.execute",
  "title": "VBA 실행",
  "description": "Executes VBA within the governed Excel automation path.",
  "group": "Excel / Automation",
  "effects": [
    "code.execute",
    "data.modify"
  ],
  "scope_kinds": [
    "project",
    "workbook"
  ],
  "target_kinds": [
    "excel.workbook"
  ],
  "risk_hint": "high",
  "platform_requirements": []
}
```

Normative semantic fields include:

- `id`
- `effects`
- `scope_kinds`
- `target_kinds`
- `platform_requirements`
- explicit execution constraints, when present

Display-only fields include:

- `title`
- `description`
- `group`
- icon
- display order
- localization metadata

Display-only fields MUST NOT affect authorization semantics.

---

# 16. Scope Kinds

CME declares what kinds of scoping a capability can meaningfully support. CME does not create the actual grant.

Standard scope kinds:

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

Providers MAY declare a subset.

Actual project/phase/time delegation belongs to the Thread Manager Authority Ledger.

---

# 17. Target Kinds

`target_kinds` describe what the capability acts upon.

Examples:

```text
filesystem.path
excel.workbook
excel.worksheet
report.artifact
report.index
process
host.configuration
external.service
```

Provider-specific target kinds MAY be used. Target kinds aid routing and scope validation; they do not create authority.

---

# 18. Platform Requirements

CME 1.0 standard platform-requirement IDs:

```text
interactive_session
gui_session
network
credential_context
os_elevation
host_confirmation_may_be_required
```

Example:

```json
{
  "id": "powershell.elevated",
  "platform_requirements": [
    "os_elevation",
    "host_confirmation_may_be_required"
  ]
}
```

Organizational authority can be delegated while a host/OS confirmation is still required. CME/Thread Manager MUST NOT interpret delegation as permission to bypass that platform requirement.

---

# 19. Availability Is Separate from Support

Manifest means:

> “The provider supports this capability.”

Status means:

> “This capability is usable on this host now.”

Standard availability states:

```text
available
unavailable
degraded
unknown
```

Example:

```json
{
  "capability_id": "vba.modify",
  "state": "unavailable",
  "reason_code": "VBIDE_ACCESS_UNAVAILABLE",
  "message": "VBIDE project access is unavailable in the current Excel environment."
}
```

Rules:

- Availability MUST NOT mutate the manifest definition.
- Host-specific availability MUST NOT be included in `manifest_hash`.
- A capability grant MAY remain persisted while availability is `unavailable`.
- Thread Manager MUST distinguish `NOT_AUTHORIZED` from `UNAVAILABLE`.

---

# 20. Four-State Effective Model

```text
AUTHORIZED + AVAILABLE
→ executable from organizational-authority perspective

AUTHORIZED + UNAVAILABLE
→ authority exists, provider/runtime unavailable

NOT_AUTHORIZED + AVAILABLE
→ capability exists, delegation/approval required

NOT_AUTHORIZED + UNAVAILABLE
→ unavailable and not granted
```

Platform permissions may add a separate execution gate.

---

# 21. Tool Bindings

A conforming provider MUST bind relevant tools to required capabilities.

CME supports:

```text
static
dynamic
```

A manifest without Tool → Capability linkage is incomplete for governed automatic execution.

---

# 22. Static Tool Binding

Static means the required capability set does not materially depend on arguments.

Representative tool metadata:

```json
{
  "name": "write_cells",
  "_meta": {
    "local.cme/capability-manifest": {
      "requirement_mode": "static",
      "required_capabilities": [
        "workbook.write"
      ]
    }
  }
}
```

Real implementations MUST use the centralized extension-ID constant.

---

# 23. Dynamic Tool Binding

Dynamic binding is REQUIRED when arguments can change required authority or Effects.

Example:

```text
run_powershell(command, elevated=false)
```

Resolution:

```text
elevated=false
→ powershell.standard

elevated=true
→ powershell.elevated
```

Tool metadata:

```json
{
  "name": "run_powershell",
  "_meta": {
    "local.cme/capability-manifest": {
      "requirement_mode": "dynamic"
    }
  }
}
```

A CME-aware enforcing consumer MUST resolve requirements before relying on automatic delegation.

---

# 24. CME Vendor Request Method

CME 1.0 uses the extension identifier itself as the vendor request method.

Development/private method:

```text
local.cme/capability-manifest
```

Request parameter `operation` selects:

```text
manifest
status
resolve
```

This deliberately keeps the extension's custom request surface small.

---

# 25. Operation: manifest

Request:

```json
{
  "method": "local.cme/capability-manifest",
  "params": {
    "operation": "manifest"
  }
}
```

Representative result:

```json
{
  "schema_version": "1.0",
  "provider": {
    "id": "excel-mcp",
    "title": "Excel MCP",
    "version": "1.4.0"
  },
  "manifest_revision": 7,
  "manifest_hash": "sha256:...",
  "capabilities": [],
  "profiles": []
}
```

---

# 26. Operation: status

Request:

```json
{
  "method": "local.cme/capability-manifest",
  "params": {
    "operation": "status"
  }
}
```

Representative result:

```json
{
  "manifest_hash": "sha256:...",
  "observed_at": "2026-08-15T03:00:00+09:00",
  "capabilities": [
    {
      "capability_id": "workbook.read",
      "state": "available"
    },
    {
      "capability_id": "vba.modify",
      "state": "unavailable",
      "reason_code": "VBIDE_ACCESS_UNAVAILABLE"
    }
  ]
}
```

---

# 27. Operation: resolve

Request:

```json
{
  "method": "local.cme/capability-manifest",
  "params": {
    "operation": "resolve",
    "tool": "run_powershell",
    "arguments": {
      "command": "Get-Process",
      "elevated": true
    },
    "manifest_hash": "sha256:..."
  }
}
```

Representative result:

```json
{
  "tool": "run_powershell",
  "required_capabilities": [
    "powershell.elevated"
  ],
  "effects": [
    "process.execute",
    "process.elevated"
  ],
  "platform_requirements": [
    "os_elevation",
    "host_confirmation_may_be_required"
  ],
  "manifest_hash": "sha256:...",
  "arguments_hash": "sha256:...",
  "resolution_fingerprint": "sha256:..."
}
```

---

# 28. Dynamic Resolution Integrity

Dynamic resolution creates a time-of-check/time-of-use risk if arguments change.

Therefore:

1. provider MUST compute `arguments_hash`;
2. provider MUST return `manifest_hash`;
3. provider MUST return `resolution_fingerprint`;
4. consumer MUST ensure executed arguments match resolved arguments;
5. changed arguments MUST trigger re-resolution;
6. changed manifest MUST trigger re-resolution.

---

# 29. Canonicalization and Hashing

CME 1.0 standardizes deterministic hashing as:

```text
JSON Canonicalization Scheme (RFC 8785)
        ↓
UTF-8 bytes
        ↓
SHA-256
        ↓
lowercase hexadecimal
```

Hash representation:

```text
sha256:<64 hex chars>
```

---

# 30. Manifest Hash

`manifest_hash` MUST be calculated over the canonical manifest semantic payload with the `manifest_hash` field itself excluded.

Availability status, runtime timestamps, and purely local diagnostics are excluded.

The manifest hash identifies the capability contract presented by that provider build.

---

# 31. Capability Semantic Fingerprint

Each capability MUST have a semantic fingerprint.

Fingerprint input includes:

- capability `id`;
- standard/custom `effects`;
- `scope_kinds`;
- `target_kinds`;
- `platform_requirements`;
- normative execution constraints.

Fingerprint input excludes:

- title;
- description;
- icon;
- localization;
- display order;
- `risk_hint`.

Result:

```text
wording/icon change
→ grant semantics unchanged

effect/scope/platform meaning change
→ semantic fingerprint changed
```

---

# 32. No Silent Semantic Expansion

An existing capability ID MUST NOT silently gain materially broader Effects.

Violation:

```text
v1:
workbook.write
→ data.modify

v2:
workbook.write
→ data.modify + code.execute
```

The provider MUST introduce a new capability or a breaking contract revision that forces authority re-evaluation.

Thread Manager SHOULD treat changed semantic fingerprints as requiring grant re-evaluation.

---

# 33. Capability Profiles

Providers MAY declare convenience profiles.

Example:

```json
{
  "id": "excel-development-full",
  "title": "Excel 개발 전권",
  "capabilities": [
    "workbook.read",
    "workbook.write",
    "vba.modify",
    "vba.execute",
    "powerquery.modify",
    "powerpivot.modify",
    "dax.execute",
    "powershell.standard"
  ]
}
```

Profiles exist for UI convenience. Profiles are NOT authority.

---

# 34. Profile Snapshot Rule

When the Human CEO approves a profile, Thread Manager MUST store the expanded capability set as a snapshot.

It MUST NOT store only a future-dynamic `profile = all` meaning.

Authority snapshot concept:

```text
profile selected:
excel-development-full

approved capabilities:
- workbook.read
- workbook.write
- vba.modify
- vba.execute
- ...

approved manifest hash:
sha256:...
```

New capabilities in later provider releases default to:

```text
UNGRANTED
```

This prevents “full access” from becoming a blank cheque for unknown future privileges.

---

# 35. Provider Trust

CME declarations are provider assertions. CME does not prove that a provider is honest.

The consumer MUST apply its own trust model. Thread Manager SHOULD consider installation origin, MCP registration, package/signature/hash provenance when available, provider identity continuity, local policy, and known Effect policy.

Provider `risk_hint` alone is insufficient.

---

# 36. Legacy / Non-CME Provider Fallback

Thread Manager MUST remain useful when a provider does not support CME.

Recommended fallback:

```text
Provider supports CME
→ VERIFIED DECLARATION

Provider lacks CME
→ tools/list
→ standard ToolAnnotations if present
→ conservative inference
→ UNVERIFIED CAPABILITY
```

Existing MCP ToolAnnotations are hints and MUST NOT be converted into trusted authority automatically.

For unverified high-impact operations, Thread Manager SHOULD require stronger confirmation/policy instead of broad automatic delegation.

---

# 37. Routing Is Separate from Authority

The same logical action may be provided by multiple providers.

Example:

```text
Workbook inspection:
- Excel MCP
- Office MCP
- Computer Use
```

Thread Manager separates:

```text
WHAT MAY BE DONE
= Authority

WHAT CAN DO IT
= Capability Inventory

WHAT SHOULD DO IT
= Routing
```

A grant SHOULD be effect/capability-based unless the Human CEO explicitly scopes it to a provider.

---

# 38. Capability Lease to Backstage Roles

CME itself does not create leases. The Thread Manager Authority Plane SHOULD use CME data to issue task-scoped effective capability leases.

Example:

```text
Human CEO
  → grants MAIN_DIRECTOR workbook.write + vba.execute

MAIN_DIRECTOR
  → dispatches IMPLEMENTATION_FACTORY task

Authority Broker
  → calculates minimum capability subset

Task lease
  → workbook.write
  → vba.execute
```

The subordinate receives the effective capability context and SHOULD NOT ask the Human CEO again for authority already present in that lease.

---

# 39. Escalation Principle

Expected path:

```text
Backstage worker/director
        ↓
Authority Broker
        ├─ already delegated → continue
        ├─ Director can decide → Main Director decision
        └─ outside authority → Main Director → Human CEO
```

Backstage permanent roles SHOULD NOT directly surface ordinary organizational approval requests to the Human CEO.

Platform-level prompts may still appear separately.

---

# 40. Organizational Authority vs Platform Permission

Organizational authority example:

```text
May this project use elevated PowerShell?
```

Thread Manager can govern this.

Platform permission examples:

```text
Windows UAC
Codex host approval
OAuth consent
credential prompt
OS security boundary
```

CME/Thread Manager MUST NOT bypass these.

A capability may therefore be:

```text
organizationally authorized
+
platform confirmation required
```

---

# 41. Computer Use as a Capability Provider

Computer Use may be represented like any provider:

```text
Provider:
computer-use

Capabilities:
ui.observe
ui.control
application.launch
file_dialog.control
```

Capabilities map to CME Effects such as `ui.observe`, `ui.control`, and `process.execute` where appropriate.

---

# 42. Excel MCP Reference Model

Illustrative capability checklist:

```text
workbook.read
workbook.write
workbook.structure.modify
workbook.save

vba.read
vba.modify
vba.execute

powerquery.read
powerquery.modify
powerquery.refresh

powerpivot.read
powerpivot.modify

dax.read
dax.modify
dax.execute

pivot.modify
chart.modify

external_data.refresh

powershell.standard
powershell.elevated

native_excel.control
```

These are examples, not a mandated list. The Excel MCP team MUST inventory its real implementation.

---

# 43. Reporting Engine Reference Model

Illustrative checklist:

```text
report.plan
report.create
report.validate
report.compare
report.history.read
report.artifact.read
```

Likely Effects:

```text
report.plan
→ data.read

report.create
→ data.read + data.create

report.validate
→ data.read

report.compare
→ data.read

report.history.read
→ data.read
```

Final manifest MUST be derived from actual tools.

---

# 44. Human CEO Delegation UI Model

The future Thread Manager UI SHOULD group capabilities by provider/domain.

Example:

```text
[ System / Computer ]
☑ Computer Use
☑ Standard PowerShell
☐ Elevated PowerShell

[ Excel MCP ]
☑ Workbook read
☑ Workbook modify/save
☑ VBA modify
☑ VBA execute
☑ Power Query
☑ Power Pivot / DAX

[ Reporting Engine ]
☑ Report plan
☑ Report create
☑ Report validate
☑ Report compare
```

The UI is not the authority. Confirmed selection becomes an Authority Ledger transaction.

Unavailable capability MUST be distinguishable from ungranted capability.

---

# 45. Security Invariants

CME 1.0 conforming ecosystems MUST preserve:

1. capability declaration is not permission;
2. provider risk hint cannot override consumer policy;
3. provider capability cannot create authority it did not receive;
4. subdelegation cannot exceed parent effective authority;
5. future capabilities are not automatically included in old snapshots;
6. semantic broadening cannot hide behind a stable capability ID;
7. dynamic calls cannot rely on materially changed unresolved arguments;
8. organizational grants cannot bypass platform security;
9. availability cannot be confused with authorization;
10. untrusted annotations alone cannot create automatic high-risk authorization.

---

# 46. Privacy and Logging

CME diagnostics SHOULD log identifiers rather than secrets.

Safe examples:

- provider ID/version;
- capability IDs;
- manifest hash;
- availability reason codes;
- resolution fingerprint;
- policy decision ID.

Logs SHOULD NOT contain complete secrets, tokens, credentials, full environment-variable dumps, or sensitive argument bodies unless explicitly required and protected.

Argument hashing SHOULD be preferred when raw retention is unnecessary.

---

# 47. Conformance Classes

## CME-PROVIDER-STATIC

Supports:

- manifest;
- status;
- static tool bindings;
- manifest hash;
- semantic fingerprints.

## CME-PROVIDER-DYNAMIC

Includes STATIC plus:

- dynamic binding;
- resolve operation;
- argument hash;
- resolution fingerprint.

## CME-CONSUMER

Supports:

- extension discovery;
- manifest/status ingestion;
- static/dynamic requirement interpretation;
- hash/fingerprint validation;
- unknown capability/effect handling;
- non-CME provider fallback inventory.

Thread Manager 1.2 is intended to implement `CME-CONSUMER`.

---

# 48. Conformance Test Kit — Required Coverage

The CME 1.0 CTK MUST test at minimum:

1. manifest schema validity;
2. provider identity presence;
3. capability ID uniqueness;
4. Effect vocabulary validity;
5. custom Effect namespacing;
6. semantic fingerprint stability;
7. display metadata changes do not change semantic fingerprint;
8. Effect changes do change semantic fingerprint;
9. manifest hash determinism;
10. manifest revision handling;
11. static tool binding resolution;
12. unknown capability reference rejection;
13. dynamic resolve;
14. argument hash change detection;
15. manifest hash change detection;
16. profile expansion;
17. future capability not in prior snapshot;
18. unavailable/degraded behavior;
19. platform-requirement preservation;
20. provider risk downgrade cannot lower consumer floor;
21. legacy/non-CME graceful fallback;
22. unknown Effect conservative handling;
23. no permission/grant data embedded in provider manifest;
24. extension ID centralized/configurable;
25. non-CME client retains baseline MCP usability.

---

# 49. Reference Validation Strategy

CME 1.0 SHOULD be validated against at least two structurally different providers.

## Complex reference provider: Excel MCP

Expected to exercise:

- many capabilities;
- dynamic resolution;
- process execution;
- elevated execution;
- VBA;
- multiple availability states.

## Simple reference provider: Reporting Engine MCP

Expected to exercise:

- mostly static capabilities;
- artifact creation;
- validation/comparison;
- read-only operations.

If both fit naturally, CME is less likely to be overfit to one MCP.

---

# 50. Migration Strategy for Existing MCPs

Recommended order:

```text
1. CME 1.0 standard lock
2. CME CTK
3. Excel MCP adoption
4. Reporting Engine adoption
5. other MCP adoption
6. Thread Manager 1.2 CME consumer
7. Dynamic Delegation UI
8. Authority Ledger / Broker
9. backstage permanent-role migration
```

Provider adoption MAY proceed before Thread Manager 1.2 is complete.

CME-enabled providers MUST continue normal MCP operation without Thread Manager.

---

# 51. Thread Manager 1.2 Relationship

Target architecture:

```text
Thread Manager 1.2
— Organization, Authority & Capability

Organization Plane
├─ frontstage
├─ backstage
└─ ephemeral

Authority Plane
├─ CEO grants
├─ inheritance
├─ bounded subdelegation
├─ scope
├─ expiry
└─ escalation

Capability Plane
├─ CME discovery
├─ Dynamic Capability Registry
├─ availability
├─ Effect normalization
└─ routing
```

CME is the Capability Plane's provider-declaration standard.

---

# 52. Acceptance Scenarios

## Scenario A — Existing delegation, Excel MCP

GIVEN:

- Human CEO granted MAIN_DIRECTOR workbook write + VBA execution;
- subdelegation is allowed;
- Excel MCP is available and CME-enabled.

WHEN:

- IMPLEMENTATION_FACTORY needs to edit VBA.

THEN:

- Thread Manager resolves required capabilities;
- effective authority exists;
- task-scoped capability lease is issued;
- implementation continues;
- no redundant Human CEO organizational approval is surfaced.

## Scenario B — Elevated PowerShell outside grant

GIVEN:

- MAIN_DIRECTOR has Excel development authority;
- elevated PowerShell is not granted.

WHEN:

- dynamic resolution returns `powershell.elevated`.

THEN:

- general Excel authority is not treated as elevated authority;
- request escalates through MAIN_DIRECTOR;
- Human CEO is asked only because the Effect lies outside existing authority.

## Scenario C — Platform prompt remains

GIVEN:

- `powershell.elevated` is organizationally granted.

WHEN:

- Windows requires UAC.

THEN:

- no duplicate organizational approval is requested;
- Windows/Codex may still show the platform permission;
- CME does not bypass it.

## Scenario D — MCP absent on another computer

GIVEN:

- project grant includes `workbook.write`;
- current host lacks a suitable provider.

THEN:

```text
authority = granted
availability = unavailable
```

The grant remains. Thread Manager must not misreport this as permission denied.

## Scenario E — MCP upgrade adds new capability

GIVEN:

- Human CEO approved a profile against manifest hash A.

WHEN:

- manifest hash B introduces `security.change`.

THEN:

- prior grant snapshot does not include it;
- new capability is `UNGRANTED`;
- it is surfaced separately if required.

---

# 53. Ratified Decisions

The following are LOCKED for CME 1.0:

1. CME declares capabilities; it does not grant permission.
2. CME is part of the Thread Manager ecosystem design, but providers remain independently usable.
3. CME is designed at public-extension quality but initially operates as an internal experimental standard.
4. MCP 2026-07-28 is the native target.
5. Development extension ID is `local.cme/capability-manifest`.
6. Public namespace requires a Human CEO-controlled owned domain.
7. Provider-scoped capabilities + common Effects are used.
8. Provider risk is advisory; consumer policy is authoritative.
9. Static and dynamic Tool requirements are both supported.
10. Profiles are UI conveniences and are expanded to grant snapshots.
11. Future capabilities are not auto-granted.
12. Support, availability, authority, and platform permission remain separate.
13. Manifest and semantic fingerprints use JCS + SHA-256.
14. Unknown Effects are treated conservatively.
15. Legacy/non-CME providers remain usable and are marked unverified by the consumer.
16. Excel MCP is the complex reference provider.
17. Reporting Engine is the simple reference provider.
18. Thread Manager 1.2 is the reference CME consumer.

---

# 54. Intentionally Deferred Beyond CME 1.0

- final public vendor namespace;
- signed manifests / PKI;
- official MCP SEP submission;
- universal cross-vendor ontology beyond CME 1.0 Effects;
- automatic external trust federation;
- cross-enterprise authority exchange;
- credentials/secret delegation protocol;
- platform-level approval replacement;
- arbitrary policy language embedded in CME.

These may become CME 1.1/2.0 or separate standards.

---

# 55. Expected Standard Package

This Blueprint authorizes creation of:

```text
CME_1.0_BLUEPRINT.md
CME_1.0_SPEC.md
cme-manifest.schema.json
cme-status.schema.json
cme-resolve.schema.json
CME_EFFECT_VOCABULARY.md
CME_CONFORMANCE_TEST_KIT/
examples/
  excel-mcp/
  reporting-engine/
```

The machine-readable schemas and CTK should be derived from this ratified contract, not from ad hoc provider implementations.

---

# 56. External Standards Basis

CME intentionally builds on existing MCP mechanisms rather than inventing a separate transport.

External basis:

- MCP 2026-07-28 formalizes a stateless core, `server/discover`, and a first-class extensions framework.
- MCP extensions are optional/additive and use `{vendor-prefix}/{extension-name}` identifiers; third-party extensions should use a reverse-domain vendor prefix they control.
- Current MCP SDK extension support allows extension advertisement and custom vendor request methods.
- MCP ToolAnnotations are hints and should not be blindly trusted for security decisions.
- RFC 8785 provides deterministic JSON canonicalization suitable for hashing.
- SHA-256 is used for deterministic manifest/fingerprint digests.

References:

- https://blog.modelcontextprotocol.io/posts/2026-07-28/
- https://modelcontextprotocol.io/extensions/overview
- https://py.sdk.modelcontextprotocol.io/advanced/extensions/
- https://modelcontextprotocol.io/specification/draft/server/tools
- https://www.rfc-editor.org/rfc/rfc8785.html
- https://www.rfc-editor.org/info/rfc6234/

---

# 57. Final Standard Statement

> **CME 1.0 is the Thread Manager ecosystem's standard MCP Capability Declaration Extension.**
>
> It enables MCP providers to describe stable functions, execution Effects, Tool requirements, and current availability in a machine-readable form.
>
> Thread Manager consumes that declaration to make Human CEO delegation persistent, inheritable, minimal, and non-repetitive.
>
> CME never grants authority itself.
>
> The expected outcome is not “zero security prompts.” The expected outcome is **zero redundant organizational approval prompts for authority that the Human CEO has already delegated, while preserving real host/OS/security boundaries.**

**CME 1.0 — RATIFIED**
