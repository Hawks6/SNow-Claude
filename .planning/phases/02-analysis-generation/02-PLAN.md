---
phase: 02
padded_phase: "02"
name: "Analysis & Generation"
requirements_addressed: [CORE-02, CORE-03, CORE-04, CORE-05, GEN-01, GEN-02]
wave: 1
autonomous: true
---

# Plan 02: Analysis & Generation Logic

## Objective
Upgrade the `story-analyzer.md` behavioral role to generate a Technical Solution Map, automate context selection, enforce scoping disclaimers, and set up a clean handoff to the code generation role (`/developer`).

## Tasks

<task>
<read_first>
- [prompts/story-analyzer.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/prompts/story-analyzer.md)
- [02-CONTEXT.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/.planning/phases/02-analysis-generation/02-CONTEXT.md)
</read_first>
<action>
Update `prompts/story-analyzer.md` to enhance the `Artifact Identification` logic and `Output Format`.
1.  **Artifact Matrix:** Add instructions to explicitly map capabilities to ServiceNow artifacts (e.g., Client-side UI changes -> Client Script/UI Policy; Server-side side-effects -> Business Rule).
2.  **Naming & Scoping Disclaimer:** Add instructions to assume a default naming convention (like `x_vendor_app`) but **Require a bolded disclaimer** mapped to the user to align this with their org's standards.
3.  **Context Recommendation:** Instruct the analyzer to output explicit references to load context (e.g., if it's a Business Rule, load `@contexts/business-rules.md`).
4.  **Quality Gates:** Add a "Quality Gate Focus" bullet for each proposed artifact (Security, Performance, Maintainability, Upgrade Safety, Testability).
5.  **Handoff Protocol:** Update the output format to guide the user to ask questions, review, and then use `/developer [story-id]` to generate code based on the Technical Design Specification.
</action>
<acceptance_criteria>
- [ ] `prompts/story-analyzer.md` is updated.
- [ ] Output format includes `Technical Solution Map`.
- [ ] Output format includes a bold disclaimer about naming conventions.
- [ ] Output format includes specific context recommendations.
- [ ] Output format includes a Handoff Protocol instruction block.
</acceptance_criteria>
</task>

## Verification
- Verify the analyzer correctly identifies a Business Rule for a server-side data change (V-01).
- Verify the analyzer suggests loading `contexts/business-rules.md` when a Business Rule is identified (V-02).
- Verify the output block includes the Naming Disclaimer and Handoff Protocol (V-04).

---
*Created: 2026-04-09*
