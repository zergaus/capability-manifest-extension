# CME 1.0 — MCP Provider Adoption Directive
## Generic Handoff / Work Order for Existing and In-Development MCPs

**Document ID:** CME-1.0-ADOPTION-DIRECTIVE  
**Applies To:** Any MCP intended to participate in the Thread Manager capability/authority ecosystem  
**Standard:** CME 1.0  
**Native MCP Target:** MCP 2026-07-28  
**Input Authority:** `CME_1.0_BLUEPRINT.md`

---

# 0. How to Use This Document

Give this document and `CME_1.0_BLUEPRINT.md` to the Main Director or owner of each MCP project.

Replace:

```text
<MCP_NAME>
<PROJECT_ROOT>
```

with the actual project.

This is a **provider-adoption task**. It is not permission to redesign the MCP, change product scope, or embed Thread Manager into the MCP.

---

# 1. WORK ORDER

Apply **CME 1.0 — Capability Manifest Extension** to:

```text
MCP: <MCP_NAME>
Project root: <PROJECT_ROOT>
```

The goal is to make the MCP declare its actual functional capabilities in a machine-readable standard form so that Thread Manager can later:

- discover what this MCP can do;
- show those capabilities in the Human CEO delegation UI;
- persist delegation through the Authority Plane;
- avoid redundant organizational approval prompts;
- route work to an appropriate available provider.

CME itself does not grant permissions.

---

# 2. GOAL LOCK

The final MCP must satisfy:

```text
Existing MCP behavior
        +
CME 1.0 capability declaration
        =
CME-enabled MCP
```

It must **not** become:

```text
MCP
+
embedded Thread Manager
+
embedded delegation system
```

Do not couple the MCP product to Thread Manager runtime availability.

The MCP MUST still function for ordinary non-CME MCP clients.

---

# 3. AUTHORITATIVE INPUTS

Read before implementation:

1. `CME_1.0_BLUEPRINT.md`
2. current MCP architecture
3. current tool catalog
4. current test suite
5. current security/authorization constraints
6. current runtime/platform requirements

Do not invent capabilities from Blueprint examples. Inventory the actual MCP.

---

# 4. BASELINE FIRST

Before changing code:

1. verify current build/tests;
2. record current tool inventory;
3. record current Git status;
4. create or identify a clean baseline commit/tag;
5. preserve existing behavior.

If the project is not under version control, establish an appropriate reversible baseline before implementation unless project policy explicitly prohibits it.

CME adoption must be separately reversible.

---

# 5. REQUIRED DISCOVERY PHASE

Create an actual capability inventory.

For every current MCP tool, determine:

```text
Tool
Purpose
Read/write/delete behavior
Can execute code?
Can execute processes?
Can elevate?
Can control UI?
Can use network?
Can use credentials?
Can change system/security settings?
Can communicate/publish/transact externally?
Does required authority change based on arguments?
Current platform requirements
Current availability conditions
```

Do not start by inventing profile names. Start from real tools and real Effects.

---

# 6. CAPABILITY DESIGN RULE

Group tools into stable provider-scoped capabilities.

Bad:

```text
tool_001_allowed
tool_002_allowed
tool_003_allowed
```

Also bad:

```text
everything = true
```

Good examples:

```text
workbook.read
workbook.write
vba.execute
```

or:

```text
report.create
report.validate
report.compare
```

A capability should represent a meaningful, stable provider ability.

---

# 7. EFFECT MAPPING

Every capability MUST map to one or more CME Effects as appropriate.

Standard CME 1.0 Effects:

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

Do not under-declare an Effect to reduce apparent risk.

---

# 8. PROVIDER RISK HINT

Capabilities MAY declare:

```text
low
medium
high
critical
```

This is only `risk_hint`.

It does not control Thread Manager authority.

Do not write code that assumes:

```text
risk_hint = low
→ automatic approval
```

That decision belongs to Thread Manager.

---

# 9. EXTENSION ID

For current private/internal CME deployments use the centralized constant:

```text
local.cme/capability-manifest
```

Requirements:

- define it once;
- reference the constant everywhere;
- do not scatter the literal;
- allow later configuration/alias migration;
- do not claim it as a globally owned public namespace.

Do not independently choose a different CME extension ID for this MCP.

---

# 10. MCP EXTENSION ADVERTISEMENT

Advertise CME 1.0 support through the MCP extension-capability mechanism supported by the project's SDK/protocol target.

The MCP's ordinary Tool surface MUST remain available to clients that do not negotiate/understand CME.

Do not make CME the only way to call existing tools.

---

# 11. CME REQUEST SURFACE

Implement the CME vendor request method using the configured extension identifier.

Support operations applicable to the provider:

```text
manifest
status
resolve
```

`manifest` is REQUIRED.

`status` is REQUIRED for standard CME provider adoption.

`resolve` is REQUIRED when at least one Tool has dynamic capability requirements.

---

# 12. MANIFEST

The manifest MUST include:

```text
schema_version = 1.0
provider identity/version
manifest_revision
manifest_hash
capabilities
profiles, if any
```

Each capability MUST include sufficient semantic definition.

At minimum:

```text
id
effects
scope_kinds
target_kinds
platform_requirements
```

Human-readable metadata SHOULD include:

```text
title
description
group
risk_hint
```

---

# 13. MANIFEST HASH

Use:

```text
RFC 8785 JSON Canonicalization
→ UTF-8
→ SHA-256
→ sha256:<lowercase hex>
```

Exclude `manifest_hash` itself.

Exclude runtime availability/timestamps.

Add deterministic tests.

---

# 14. SEMANTIC FINGERPRINT

Every capability MUST expose or internally verify a deterministic semantic fingerprint based on:

```text
id
effects
scope_kinds
target_kinds
platform_requirements
normative execution constraints
```

Exclude:

```text
title
description
group
icon
localization
display order
risk_hint
```

Required tests:

```text
description-only edit
→ fingerprint unchanged

effect edit
→ fingerprint changed
```

---

# 15. TOOL BINDINGS

Every governed Tool MUST be linked to its required capability set.

Choose:

```text
static
dynamic
```

per Tool.

A Tool must not be left unbound merely because its behavior is complex. Complexity that changes authority is a reason for dynamic resolution.

---

# 16. STATIC TOOL RULE

Use static binding if arguments do not materially change required capability/Effect set.

Representative form:

```json
{
  "_meta": {
    "local.cme/capability-manifest": {
      "requirement_mode": "static",
      "required_capabilities": [
        "example.capability"
      ]
    }
  }
}
```

Use the centralized CME extension constant in real code.

---

# 17. DYNAMIC TOOL RULE

Use dynamic binding when arguments can change impact/authority.

Examples:

```text
elevated=false vs elevated=true
read-only vs write mode
local-only vs external publish
preview vs commit
dry-run vs destructive execution
```

Dynamic tools MUST support CME `resolve`.

---

# 18. DYNAMIC RESOLVE INTEGRITY

`resolve` MUST return enough information to prevent stale authority decisions.

Required:

```text
tool
required_capabilities
effects
platform_requirements
manifest_hash
arguments_hash
resolution_fingerprint
```

If arguments change between resolution and execution, the consumer must be able to detect it.

Write tests that deliberately mutate arguments after resolution.

---

# 19. AVAILABILITY STATUS

Do not remove a capability from the manifest merely because it is temporarily unavailable.

```text
Manifest = supported by provider
Status   = available on current host
```

Support:

```text
available
unavailable
degraded
unknown
```

Provide stable machine-readable reason codes where practical.

Examples:

```text
DEPENDENCY_MISSING
HOST_APPLICATION_MISSING
AUTH_CONTEXT_MISSING
INTERACTIVE_SESSION_REQUIRED
VBIDE_ACCESS_UNAVAILABLE
FEATURE_NOT_INSTALLED
OS_ELEVATION_UNAVAILABLE
```

Provider-specific reason codes MAY be added.

---

# 20. PLATFORM REQUIREMENTS

Declare platform requirements when applicable:

```text
interactive_session
gui_session
network
credential_context
os_elevation
host_confirmation_may_be_required
```

Do not imply that CME authority can bypass those requirements.

---

# 21. PROFILES

Profiles are OPTIONAL but recommended for providers with many capabilities.

Examples:

```text
General
Development Full
Read Only
```

A profile MUST expand to explicit capability IDs.

Do not create future-dynamic meaning such as:

```text
all_current_and_future_capabilities
```

New future capabilities remain ungranted until separately approved by the authority consumer.

---

# 22. NO SILENT CAPABILITY EXPANSION

After a capability ID ships, do not materially broaden it.

Forbidden:

```text
v1 capability Effects:
data.modify

v2 same capability ID:
data.modify + process.elevated
```

Use a new capability or breaking contract revision.

Add a test for this rule.

---

# 23. PROVIDER TRUST BOUNDARY

Do not implement provider self-approval.

CME manifest says what the provider claims to do. Thread Manager decides effective policy.

Do not add fields such as:

```text
auto_approve = true
ceo_approved = true
delegated_to = ...
```

These violate the standard boundary.

---

# 24. THREAD MANAGER INDEPENDENCE

The MCP MUST NOT require:

- Thread Manager installed;
- Thread Manager Registry;
- Thread Manager Authority Ledger;
- MAIN_DIRECTOR role IDs;
- a specific project organization.

CME support is a provider declaration surface. The MCP must remain a normal MCP server.

---

# 25. LEGACY / NON-CME CLIENT COMPATIBILITY

Test the MCP without CME negotiation.

Expected:

```text
existing Tool listing/calls continue to function
```

CME-aware behavior is additive. Do not regress existing integrations.

---

# 26. SECURITY REVIEW

Review every capability for:

- understated Effects;
- hidden process execution;
- hidden code execution;
- credential usage;
- external communication;
- external transaction;
- platform elevation;
- security/system configuration;
- arguments that change Effect severity.

A provider with broad internal automation MUST expose the relevant Effect even if the user does not see the internal mechanism.

Example:

```text
Tool internally invokes elevated PowerShell
→ process.elevated MUST be represented
```

---

# 27. DO NOT OVER-SPLIT INTERNAL IMPLEMENTATION

CME represents authority-relevant capabilities.

Do not expose every helper function.

Bad:

```text
excel._com_get_dispatch
excel._marshal_variant
excel._retry_rpc
```

Good:

```text
workbook.read
workbook.write
vba.execute
```

Capabilities should be meaningful to policy and delegation.

---

# 28. DO NOT OVER-COLLAPSE HIGH-RISK FUNCTIONS

Do not hide materially different authority behind one broad capability.

Bad:

```text
excel.all
```

if it includes:

```text
read workbook
run VBA
elevated PowerShell
change security settings
```

Separate functions when Effects/policy differ materially.

---

# 29. REFERENCE GUIDANCE — EXCEL-LIKE MCP

If this MCP is Excel/Office automation, inspect whether it supports:

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
native_application.control
```

This is an inventory checklist, not a mandated capability list. Only declare actually supported functionality.

---

# 30. REFERENCE GUIDANCE — REPORTING-LIKE MCP

If this MCP is a reporting engine, inspect:

```text
report.plan
report.create
report.validate
report.compare
report.history.read
report.artifact.read
```

Derive the final capability IDs from the real MCP. Do not redesign the engine solely to match this example.

---

# 31. REQUIRED TESTS

At minimum add tests for:

1. CME advertisement;
2. manifest retrieval;
3. manifest schema/shape;
4. capability ID uniqueness;
5. standard Effect validity;
6. static Tool bindings;
7. unknown capability-reference rejection;
8. status operation;
9. unavailable-capability behavior;
10. manifest-hash determinism;
11. semantic-fingerprint determinism;
12. display-only metadata not affecting semantic fingerprint;
13. Effect change affecting semantic fingerprint;
14. profile expansion, if profiles exist;
15. baseline MCP behavior without CME;
16. no Thread Manager runtime dependency.

If dynamic tools exist, also:

17. resolve operation;
18. arguments hash;
19. resolution fingerprint;
20. argument mutation detection;
21. manifest change detection.

---

# 32. KNOWN-BAD TESTS

Explicitly reject/detect:

- duplicate capability IDs;
- invalid/unknown unqualified standard Effect;
- static binding to unknown capability;
- dynamic Tool without resolver;
- dynamic resolution referencing unknown capability;
- manifest-hash mismatch;
- semantic-fingerprint mismatch;
- unavailable capability reported as available without required dependency;
- privilege-affecting argument omitted from dynamic resolution;
- profile referencing unknown capability;
- Thread Manager grant data embedded inside manifest;
- newly added capability silently treated as part of an old snapshot;
- existing capability acquiring a new critical Effect without fingerprint/breaking-change handling.

---

# 33. CONFORMANCE CLASS

Declare one:

```text
CME-PROVIDER-STATIC
```

or:

```text
CME-PROVIDER-DYNAMIC
```

Dynamic providers include all static requirements plus resolver requirements.

Document the class in the CME adoption report.

---

# 34. IMPLEMENTATION ARTIFACTS

Recommended local structure:

```text
<project>/
  cme/
    manifest.*
    effects.*
    hashing.*
    status.*
    resolve.*          # if dynamic
    schemas/
    tests/
```

Adapt to the project's established architecture. Do not force this structure if it creates architectural regression.

---

# 35. DELIVERABLE — PROVIDER CAPABILITY CATALOG

Create a human-readable document such as:

```text
CME_CAPABILITY_CATALOG.md
```

For every capability document:

```text
ID
Title
Purpose
Mapped Tools
Effects
Risk hint
Scope kinds
Target kinds
Platform requirements
Static/Dynamic
Availability conditions
```

The machine-readable manifest is runtime authority; this catalog is review evidence.

---

# 36. DELIVERABLE — TOOL BINDING MATRIX

Create:

```text
CME_TOOL_BINDING_MATRIX.md
```

Suggested columns:

```text
Tool
Requirement mode
Capabilities
Effects
Dynamic triggers
Platform requirements
```

This makes hidden authority gaps visible during review.

---

# 37. DELIVERABLE — ADOPTION REPORT

Create:

```text
CME_ADOPTION_REPORT.md
```

Required sections:

```text
RESULT

PROJECT / BASELINE

MCP VERSION / MCP PROTOCOL TARGET

CME CONFORMANCE CLASS

PROVIDER ID

EXTENSION ADVERTISEMENT

CAPABILITY INVENTORY

EFFECT MAPPING

STATIC TOOL BINDINGS

DYNAMIC RESOLUTION

AVAILABILITY MODEL

PLATFORM REQUIREMENTS

PROFILES

HASH / SEMANTIC FINGERPRINT

SECURITY REVIEW

LEGACY CLIENT REGRESSION

CME TEST RESULTS

EXISTING MCP REGRESSION

KNOWN LIMITATIONS

THREAD MANAGER READINESS

GIT / FINAL STATE
```

---

# 38. ACCEPTANCE CRITERIA

CME adoption is PASS only when:

- existing MCP regression passes;
- CME extension is advertised;
- manifest is machine-readable;
- manifest hash is deterministic;
- capability IDs are unique;
- Effects are complete enough for authority reasoning;
- relevant Tools have static/dynamic bindings;
- dynamic Tools resolve authority-changing arguments;
- status distinguishes support from current availability;
- platform requirements remain explicit;
- no Thread Manager authority/grant logic is embedded;
- no existing Tool requires Thread Manager merely to operate;
- semantic-expansion protections exist;
- profile expansion is explicit/snapshot-safe;
- known-bad tests pass;
- independent review finds no authority-bypass path;
- final Git tree is clean or deviations are documented.

---

# 39. SPECIAL ACCEPTANCE FOR HIGH-POWER MCPs

For MCPs that can invoke:

- arbitrary code;
- PowerShell;
- elevated PowerShell;
- VBA/macros;
- external communication;
- security/system configuration;
- credentials;
- financial/external transactions;

an independent reviewer MUST verify those Effects cannot be hidden behind a lower-impact capability.

Example failure:

```text
Capability says:
data.read

Tool can invoke:
elevated PowerShell

→ FAIL
```

---

# 40. THREAD MANAGER INTEGRATION READINESS

Provider-side CME completion does not require Thread Manager 1.2 to be finished.

At completion the provider should be ready for Thread Manager to perform:

```text
discover
→ manifest
→ status
→ resolve if needed
→ Dynamic Capability Registry
→ Authority Broker
```

Do not implement that consumer behavior inside this MCP.

---

# 41. WORK STOP CONDITION

After CME provider adoption:

- do not begin Thread Manager 1.2 implementation;
- do not redesign the Human CEO delegation UI;
- do not modify unrelated MCP features;
- do not introduce new provider functionality merely to make the CME catalog larger.

Stop and report.

The purpose is to **declare the MCP's real capability surface correctly**, not expand product scope.

---

# 42. COPY-PASTE START COMMAND FOR A MAIN DIRECTOR

> Apply CME 1.0 to this MCP as a provider-adoption task. Treat `CME_1.0_BLUEPRINT.md` as the authoritative standard and `CME_1.0_MCP_ADOPTION_DIRECTIVE.md` as the work order. Preserve the current MCP product behavior and clean baseline. Inventory the actual Tools first, derive stable provider-scoped capabilities, map all authority-relevant CME Effects, implement static/dynamic Tool bindings as required, implement manifest/status and resolve where required, add deterministic JCS+SHA-256 manifest/fingerprint handling, add CME conformance/known-bad tests, and verify legacy non-CME operation. Do not implement Thread Manager authority/delegation logic inside this MCP. Do not broaden product scope merely for CME. Complete the adoption report and stop for Human review.

---

# 43. Intended Result

Before CME:

```text
Agent:
"I need to use this MCP/Excel/PowerShell.
May I proceed?"
```

After CME + future Thread Manager Authority integration:

```text
Provider:
"Tool X requires Capability A with Effects B/C."

Thread Manager:
"Capability A is available.
The Human CEO already delegated A for this scope."

Result:
continue without redundant organizational approval
```

If authority is absent:

```text
Thread Manager
→ Main Director
→ Human CEO, only when truly required
```

If platform permission is required:

```text
organizationally authorized
+
platform/security prompt remains
```

That distinction is the goal.

**End of CME 1.0 MCP Provider Adoption Directive**
