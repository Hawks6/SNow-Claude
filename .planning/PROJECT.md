# Project Context: SNow-Claude Skills Expansion

## What This Is
Adding a new `snow-story` skill to the SNow-Claude framework. This skill acts as a User Story Analyzer & Solutions Architect that takes raw agile user stories (via chat or file), applies ServiceNow platform guidelines (`SYSTEM.md`), retrieves relevant context, and breaks the story down into concrete, implementation-ready ServiceNow artifacts (Client Scripts, Business Rules, Script Includes, etc.). The skill integrates cleanly, ensuring SNow-Claude preserves its default behavior when the skill is not explicitly invoked.

## Requirements

### Validated

- ✓ Core persona and system architecture utilizing Composable Context Injection (`SYSTEM.md` as Brain).
- ✓ Existing behavioral roles (`architect.md`, `developer.md`, `reviewer.md`, `debugger.md`).
- ✓ Existing knowledge base (`contexts/`) and specification engine (`specs/`).
- ✓ Zero-dependency, Markdown-based implementation designed for Claude.

### Active

- [ ] Define the `snow-story` skill logic and prompt structure.
- [ ] Enable the skill to parse and accept user stories provided via inline chat or as a `.md` file reference.
- [ ] Define logic to analyze requirements specifically using `SYSTEM.md`.
- [ ] Ensure the skill generates concrete ServiceNow artifacts (e.g., Client Scripts, Business Rules) based on its analysis.
- [ ] Implement conditional bounds so the rest of SNow-Claude operates normally when the skill is not invoked.

### Out of Scope

- Direct deployment to or modification of a live ServiceNow instance (this framework remains an isolated, spec-driven development environment).

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| **Target Output Level:** Analysis + Code Artifacts | Generating both the strategic breakdown and the actual code satisfies the requirement for a complete solution in one pass without extra steps. | — Pending |
| **Interaction Model:** Chat & MD File Support | Supports multiple workflows (quick, informal requests vs documented agile stories) out of the box. | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-09 after initialization*
