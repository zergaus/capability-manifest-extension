# CME — Capability Manifest Extension

**An experimental MCP extension for machine-readable provider capabilities, execution effects, tool requirements, and runtime availability.**

> **Status:** CME 1.0 design contract ratified; public experimental repository.  
> **Native MCP target:** MCP 2026-07-28  
> **License:** Apache-2.0  
> **Official MCP status:** Third-party experimental extension; not an MCP Core standard.

## What CME does

CME gives an MCP provider a machine-readable way to declare:

- what capabilities the provider supports;
- what real execution **Effects** those capabilities can cause;
- which capabilities each Tool requires;
- when Tool arguments change the required capability set;
- what is actually available on the current host;
- what platform requirements remain outside organizational authority.

CME is designed so that a consumer can reason about provider capabilities without hard-coding a particular MCP implementation.

## What CME does not do

CME does **not** grant permission or delegation. It does not replace MCP authorization, OAuth, credentials, sandboxing, host approvals, Windows UAC, or other platform security boundaries.

The key separation is:

```text
Capability  = what a provider can do
Availability = whether it can do it on this host now
Authority    = whether the action has been delegated/approved
Platform permission = whether the host/OS/security layer permits execution
```

These states are intentionally independent.

## Core model

```text
MCP Provider
    │
    │ CME manifest / status / resolve
    ▼
Capability-aware Consumer
    ├─ capability inventory
    ├─ Effect normalization
    ├─ availability tracking
    ├─ authority policy (external to CME)
    └─ provider routing (external to CME)
```

CME itself only standardizes the provider declaration surface.

## CME 1.0 highlights

CME 1.0 defines:

- provider-scoped capability IDs;
- a common execution Effect vocabulary;
- static and dynamic Tool → Capability bindings;
- `manifest`, `status`, and dynamic `resolve` operations;
- separation of provider support from host availability;
- platform-requirement declarations;
- deterministic JSON Canonicalization Scheme (RFC 8785) + SHA-256 hashing;
- capability semantic fingerprints;
- a **No Silent Semantic Expansion** rule;
- snapshot-safe capability profiles;
- conservative handling of unknown Effects and legacy/non-CME providers;
- provider and consumer conformance classes plus CTK requirements.

## Current extension identifier

Private/development deployments currently use:

```text
local.cme/capability-manifest
```

This identifier is **development/private only** and must not be represented as a globally owned public namespace.

Before a stable external release, CME 1.0 requires migration to a reverse-domain extension identifier controlled by the project owner. Implementations should therefore centralize the extension identifier and support migration aliases where appropriate.

## Repository documents

| Path | Purpose | Status |
|---|---|---|
| [`specification/1.0/CME_1.0_BLUEPRINT.md`](specification/1.0/CME_1.0_BLUEPRINT.md) | Ratified design history and normative 1.0 contract | Ratified |
| [`docs/adoption/CME_1.0_MCP_ADOPTION_DIRECTIVE.md`](docs/adoption/CME_1.0_MCP_ADOPTION_DIRECTIVE.md) | Generic provider-adoption work order | Ratified companion directive |
| [`docs/PROJECT_STATUS.md`](docs/PROJECT_STATUS.md) | Public repository maturity, boundaries, and next package work | Living document |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Contribution rules for normative and implementation changes | Active |

## Planned 1.0 package

The ratified Blueprint authorizes the following implementation package, which will be derived from the contract rather than invented ad hoc by individual providers:

```text
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

The intended reference validation pair is deliberately heterogeneous:

- **Excel MCP** — complex provider exercising dynamic resolution, automation/code execution, and host-dependent availability.
- **Reporting Engine** — simpler provider exercising mostly static capabilities and artifact/report operations.

## Design invariants

A conforming CME ecosystem preserves these boundaries:

1. capability declaration is not permission;
2. provider risk hints cannot lower consumer policy;
3. future capabilities are not automatically included in old grants;
4. existing capability IDs cannot silently gain materially broader Effects;
5. argument-sensitive authority requirements must be resolved before execution;
6. availability cannot be confused with authorization;
7. organizational authority cannot bypass platform security;
8. legacy/non-CME providers remain usable, but their inferred capabilities are unverified.

## Using CME in an MCP provider

Start with the provider's actual Tool catalog. Inventory real effects and platform requirements first, then derive stable provider-scoped capabilities. Do not design capabilities merely to match examples in the standard.

The provider adoption procedure is documented in [`CME_1.0_MCP_ADOPTION_DIRECTIVE.md`](docs/adoption/CME_1.0_MCP_ADOPTION_DIRECTIVE.md).

## Project origin and independence

CME originated in the Thread Manager ecosystem to separate provider capability discovery from persistent organizational authority. The extension is intentionally designed to remain independent of Thread Manager runtime: a CME-enabled MCP must continue to operate as a normal MCP server for non-CME clients.

## License

Licensed under the [Apache License 2.0](LICENSE).
