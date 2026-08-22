# Operational-Fit-First

**Status:** Active project rationale  
**Applies to:** CME evolution after the ratified 1.0 baseline

## Principle

CME exists because concrete systems needed a reliable way to describe machine capabilities separately from authority.

The project therefore follows this rule:

> **Make CME work well for real adopters first. Generalize second. Publish the useful result.**

Public availability does not invert that order.

## Why

A capability standard can easily become over-generalized.

Premature abstraction tends to produce:

- capability taxonomies no real provider needs;
- transport requirements that complicate otherwise simple local systems;
- fields added only for hypothetical interoperability;
- semantic indirection that makes authority review harder;
- a standard that looks broad but is unpleasant to implement.

CME should avoid that failure mode.

## Preferred evolution loop

```text
1. Observe a real adopter requirement.
2. Model the smallest capability/effect/availability semantics that solve it.
3. Implement and operate it.
4. Compare with other concrete adopters.
5. Generalize only when the generalization reduces real complexity or captures a repeated pattern.
6. Promote the proven result into a versioned CME contract.
```

## Adopter priority

Primary adopters may exercise experimental semantics before those semantics become stable CME requirements.

A public contributor may propose broader semantics, but breadth alone is not sufficient justification.

A proposed abstraction should answer:

- Which real adopter needs it?
- What complexity does it remove?
- What failure or ambiguity does it prevent?
- Does it preserve capability/authority separation?
- Does it make existing adopters harder to implement?
- Does it require a major-version boundary?

## Ratified versions remain stable

Operational-fit-first does not permit rewriting history.

CME 1.0 remains ratified according to its published semantics.

If real-world use demonstrates that CME 1.0's MCP-specific shape is too narrow, that is evidence for a versioned successor such as CME 2.0—not permission to silently redefine 1.0.

## Public standard, internal utility

CME is public because the contract may be useful beyond its originating ecosystem.

But the project does not optimize for external adoption metrics, maximal theoretical coverage, or standards prestige.

The standard should be:

```text
useful
small enough to reason about
strict where safety requires it
extensible where real use proves it
```

If those qualities also make CME useful to others, that is the intended public benefit.
