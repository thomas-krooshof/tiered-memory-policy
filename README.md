# Tiered Memory Discipline for ChatGPT

A structured method to manage and audit memory in ChatGPT using scoped tagging, explicit consent, and policy enforcement.

---

## Overview

ChatGPT’s memory can remember user preferences, tone, goals, and more. However, memory behavior can feel opaque and difficult to control. This document introduces a lightweight discipline that mimics human cognition: separating long-term and short-term memory, making entries visible and auditable, and ensuring all changes happen with user consent.

---

## Benefits

- Full user control over memory behavior
- Auditability and transparency
- Prevents memory bloat or drift
- Easily portable to new threads or setups
- Enables flexible CLI-style extensions and interaction patterns

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

[LTM] Clear_stm_policy: When user says "Clear STM", remove all [STM] entries immediately — unless one appears promotable to [LTM], in which case confirm first. Otherwise act without delay or confirmation.
```

---

## How to Use

### For Users

Use `[STM]` tags for transient goals, modes, or thread-specific context. Example:\
`"Add [STM] Reviewing onboarding flow."`

Use `[LTM]` tags for persistent identity, tone, preferences, or constraints. Example:\
`"Add [LTM] Prefers clarity over convention, even if tone is direct."`

Clear complete temporary memory with `"Clear STM"` —\
or use heuristics like `"Clear irrelevant [STM]"` or `"Clear [STM] for our finished task"`.

Actively manage memory with:

- `"List all [STM] entries."` — View all temporary context.
- `"Summarize active [STM] context."` — Get a concise overview.
- `"Which [STM] entries are likely outdated or redundant?"` — Cleanup suggestions.
- `"Is any [STM] entry a good candidate for promotion to [LTM]?"` — Preserve what matters.

### For Assistants

- Never store memory without explicit tagging and consent.
- Suggest promotion if STM entries recur or seem central.
- Decline to store entries with no tag or ambiguous scope.

---

## Command Interface and Mode Control (Optional)

Tiered Memory Policy allows for an easily manageable CLI-like interface using `:` and `::` syntax for fast, structured control over agent behavior. using `:` and `::` syntax for fast, structured control over agent behavior.

- `:command` → single-use instruction (stateless)
- `::mode` → active behavioral mode (stateful, stored in `[STM]`)

### Enable via:

```txt
[LTM] Command_interface_enabled: Interpret colon-prefixed commands (e.g. :memory:list, ::review) as user triggers for memory or behavioral control. Commands prefixed with "::" refer to named user-defined modes. Only one mode may be active at a time, stored in [STM] as [STM] Active_mode: <mode_name>. Each mode must be defined in [LTM] as [LTM] Mode_<mode_name>: <description>. If a mode is invoked without a definition, propose to store it. Activating a new mode clears any prior mode state. 

[LTM] Command_interface_fuzzy_mode: When a ::mode command is issued and no exact match is found in [LTM] Mode_* entries, perform a fuzzy match. If a close match is found, ask user to confirm. If no match is acceptable, propose to create a new [LTM] Mode_<name> entry. Avoid storing undefined or ambiguous modes.
```

### Example Commands and Fuzzy Extension Logic

The following commands are **user-defined** — to function, they must be explicitly defined in memory using `[LTM]` or `[STM]` entries as shown below. This includes stateless `:commands` and stateful `::modes`.

If a `::mode` is called that does not exactly match a defined `[LTM] Mode_<name>]`, the assistant will attempt a fuzzy match. If a close match is found, you will be asked to confirm. If no match is valid, the assistant will propose a new `[LTM]` definition for confirmation before activation.

The following are **examples**, not built-in features. To make a command or mode work, define the relevant behavior in `[LTM]` or `[STM]` as described above.

| Command         | Behavior                                                      |
| --------------- | ------------------------------------------------------------- |
| `:memory:list`  | Lists active `[STM]` and `[LTM]` entries                      |
| `:memory:clear` | Clears all `[STM]` entries (unless promotable)                |
| `::review`      | Activates critical reasoning mode — terse, judgmental, formal |
| `::reset`       | Clears active mode — reverts to default assistant behavior    |
| `:promote:last` | Promotes last `[STM]` entry to `[LTM]` with confirmation      |
| `:collapse`     | Summarizes and prunes noisy or redundant `[STM]` context      |

To enable a command like `::review`, you must define it explicitly:

```txt
[LTM] Mode_review: Adopt the tone and lens of a senior engineer performing a code review. Be precise, critical, and unsentimental. Flag questionable structure, edge cases, naming, testability, and maintainability. Do not explain obvious basics. No flattery or hedging.
```

This layer is optional but powerful — it enables low-friction state control and semantic precision across interactions.
