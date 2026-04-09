# Phase 1: Foundations & Parsing - Context

**Gathered:** 2026-04-09
**Status:** Ready for planning

<domain>
## Phase Boundary

Define the interaction model for user stories, create the formal input specification, and implement the initial parsing logic in the `story-analyzer` skill. This handles the transition from raw user input to a structured internal representation of the requirement.

</domain>

<decisions>
## Implementation Decisions

### Input Specification
- **D-01: Lightweight Markdown** — The `user-story.spec.md` will favor a clean, markdown-first structure over heavy YAML headers. This keeps the barrier to entry low for users drafting stories.
- **D-02: Flexible Parsing** — The parsing engine will be semantic/flexible. It will look for intent (Actor, Goal, Value) rather than enforcing a strict regex-based format.

### Analysis Behavior
- **D-03: Interactive Clarification** — If a story is ambiguous or missing critical parts (e.g., "I want to sync data" without specifying source/target), the skill must stop and ask clarifying questions rather than guessing.
- **D-04: AC Checklist** — Acceptance Criteria will be extracted and presented as a checklist. This sets the stage for verification in Phase 2.

### the agent's Discretion
- The specific regex/logic for finding keywords (As a, I want to, So that) is left to the analyzer's discretion, provided it remains flexible.
- The formatting of the "Clarification Request" UI.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Core System
- `SYSTEM.md` — Performance, Security, and Code standards.
- `prompts/developer.md` — established output patterns for code artifacts.

### Phase Specs
- `.planning/REQUIREMENTS.md` — REQ-IDs CORE-01, GEN-03.
</canonical_refs>

<deferred>
## Deferred Ideas
- Automatic generation of ATF assertions from the AC checklist (to be handled in v2/M2).
</deferred>
