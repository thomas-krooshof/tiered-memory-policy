Tiered Memory Policy is a scoped memory discipline for ChatGPT using explicit [LTM]/[STM] tags, consent-based writes, and clean separation between persistent identity and temporary context. Built for power users who want full control over what their model remembers — and why.

# Tiered Memory Discipline for ChatGPT

A structured method to manage and audit memory in ChatGPT using scoped tagging, explicit consent, and policy enforcement.

---

## Overview

ChatGPT’s memory can remember user preferences, tone, goals, and more. However, memory behavior can feel opaque and difficult to control. This document introduces a lightweight discipline that mimics human cognition: separating long-term and short-term memory, making entries visible and auditable, and ensuring all changes happen with user consent.

---

## Key Concepts

- **[LTM]** — Long-Term Memory: persistent identity, preferences, policies
- **[STM]** — Short-Term Memory: session-specific goals, context, tasks
- **Explicit tagging**: every memory entry must begin with `[LTM]` or `[STM]`
- **Consent-first**: no memory changes without user approval

---

## Core Policy (store each as a memory entry)

```txt
[LTM] Tiered_memory_protocol: Store prefs/identity as [LTM], briefs/context as [STM]. Require visible tag in content. No scope = reject. [LTM] persists unless explicitly removed. [STM] can be cleared by user or agent. This rule is tagged [LTM].

[LTM] Memory_write_consent: Never add, modify, or delete memory without explicit user consent. Ask first — only act if confirmed.

[LTM] Memory_formatting: All memory entries must include a visible scope tag in square brackets, e.g. [LTM] or [STM], placed at the start of the entry. Example: [LTM] Strict_adherence: ... or [STM] Active_goal: ....

[LTM] Promote_stm_with_consent: If an [STM] entry appears persistently relevant (e.g. reused, cross-session, central to reasoning), suggest promotion to [LTM]. Only act after explicit user consent.
```

---

## How to Use

### For Users
- Add these memory entries via prompt or memory editor.
- Use `[STM]` tags for temporary goals or context (e.g. `[STM] Active_goal: Reviewing onboarding strategy.`)
- Ask the assistant to promote to `[LTM]` if it proves useful.
- Clear `[STM]` entries when context changes.

### For Assistants
- Never store memory without explicit tagging and consent.
- Suggest promotion if STM entries recur or seem central.
- Decline to store entries with no tag or ambiguous scope.

---

## Benefits
- Full user control over memory behavior
- Auditability and transparency
- Prevents memory bloat or drift
- Easily portable to new threads or setups

---

## Status
This discipline is community-defined and experimental, but aligns with ChatGPT’s memory interface and emerging best practices.

Share, adapt, and iterate.

---
