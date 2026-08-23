# CME 2.0 — TM Validation Requirement Patch v0.1

**Status:** `E2E REVIEW INPUT / UNLOCKED`  
**Current ratified authority:** CME 1.0  
**Targets:** CME 2.0 Responsibility & Interop candidate  
**Source:** Thread Manager REV7 / synchronized-replica / PM responsibility-boundary review

---

# 1. Purpose

The Thread Manager REV7 validation exposed three concrete clarifications that must be harvested into the CME 2.0 candidate before PM/TM reverse adoption:

```text
1. semantic-domain ownership must not reuse runtime-authority terminology;
2. semantic-domain ownership must remain separate from runtime writer/leader/fence ownership;
3. Human-presence evidence may be structurally required by an invocation without CME authenticating the Human or granting authority.
```

These requirements do not alter CME 1.0.

---

# 2. Requirement TM-CME-R1 — responsibility terminology

The v0.1 candidate role token:

```text
authoritative
```

is ambiguous in systems that already use `authority`, `authoritative Host`, `runtime ownership`, `ownership epoch`, and `fencing token` for live mutation control.

CME 2.0 SHOULD use:

```text
semantic_owner
```

for canonical machine-semantic ownership.

Required invariant:

```text
semantic_owner
!= organizational authority
!= runtime mutation ownership
!= active Host
!= leader
!= lease
!= fence holder
```

---

# 3. Requirement TM-CME-R2 — runtime ownership independence

A provider may remain `semantic_owner` for a domain while a particular runtime instance is:

```text
STANDBY
DRAINING
RECOVERY_REQUIRED
stale generation
not current fence holder
not current active Host
```

CME MUST NOT infer live mutation permission from responsibility declaration.

Examples:

```text
PM semantic_owner(chatgpt.conversation)
+ stale provider-writer generation
→ PM remains semantic owner
→ mutation denied by PM runtime ownership

TM semantic_owner(codex.work)
+ Host B lacks current ownership/fence
→ TM remains semantic owner
→ Host B mutation denied by TM runtime ownership
```

---

# 4. Requirement TM-CME-R3 — Human-presence invocation context

Some high-risk operations require an external Human-presence evidence object.

CME 2.0 SHOULD support the reusable required-context kind:

```text
human_presence_evidence_ref
```

CME meaning:

```text
this invocation structurally requires a Human-presence evidence reference
```

CME non-meaning:

```text
the Human is authenticated
the reference is fresh
the action digest matches
the evidence is one-shot/unconsumed
the Human is the Human CEO
the operation is authorized
```

Validation remains owned by the service/Host/Authority boundary.

---

# 5. Fingerprint / compatibility implication

The following are material CME 2.0 candidate semantic changes:

```text
semantic_owner ↔ gateway
Human-presence context newly required
Human-presence context requirement removed
```

They SHOULD participate in compatibility/fingerprint evaluation when they affect safe invocation meaning.

The candidate migration package must explicitly normalize the pre-ratification v0.1 role token:

```text
authoritative → semantic_owner
```

without treating that token rename alone as a change to organizational/runtime authority.

---

# 6. Reverse-adoption targets

After CME 2.0 consolidation, PM and TM should consume these semantics as follows:

```text
PM
→ semantic_owner: chatgpt.project / conversation / logical_session / interop.binding
→ gateway: interop.transport
→ runtime provider-writer ownership remains PM-internal

TM
→ semantic_owner: codex.thread / work / execution / runtime
→ active Host/runtime ownership/fencing remains TM-internal
→ Human-sensitive operations may require human_presence_evidence_ref
→ actual evidence validation remains TM Host/Authority-owned
```

---

# 7. Explicit non-scope

This patch MUST NOT move the following into CME:

```text
Human authentication
Human-presence evidence issuance or consumption
Authority Ledger
runtime leader election
runtime ownership lease
fencing/generation rotation
unclean replica recovery
split-brain recovery
PM/TM transaction state machines
AI hierarchy or decision logic
```

---

# 8. Requirement verdict

```text
TM-CME-R1 responsibility terminology     ACCEPT FOR CME 2.0 CANDIDATE
TM-CME-R2 runtime-ownership separation   ACCEPT FOR CME 2.0 CANDIDATE
TM-CME-R3 Human-presence context kind    ACCEPT FOR CME 2.0 CANDIDATE
```

These are current requirement-harvest inputs, not ratified CME authority.
