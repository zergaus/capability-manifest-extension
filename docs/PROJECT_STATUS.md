# CME Project Status

## Current state

**CME 1.0 is a ratified design contract published as an experimental third-party MCP extension.**

The repository currently publishes the authoritative 1.0 Blueprint and the generic MCP Provider Adoption Directive. The machine-readable schemas, extracted normative specification, conformance test kit, and reference examples listed in the Blueprint are the next implementation package and are not yet represented here as completed artifacts.

This distinction matters:

```text
Ratified design contract != completed public implementation package
```

## Locked 1.0 boundaries

The following are treated as stable 1.0 design decisions unless a documented breaking revision supersedes them:

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

The current development/private extension identifier is:

```text
local.cme/capability-manifest
```

A stable public release requires an owner-controlled reverse-domain namespace. Until that decision is made, the development identifier must not be presented as a globally owned public namespace.

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

Changes to Effects, scope kinds, target kinds, platform requirements, execution constraints, dynamic-resolution integrity rules, fingerprint inputs, or other authority-relevant semantics must be treated as normative changes and reviewed for compatibility.

A change that materially broadens an existing capability's meaning cannot be shipped silently under the same capability ID.
