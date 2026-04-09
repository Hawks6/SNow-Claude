---
phase: 01
padded_phase: "01"
name: "Foundations & Parsing"
requirements_addressed: [CORE-01, GEN-03]
wave: 1
autonomous: true
---

# Plan 01: User Story Interaction & Parsing

## Objective
Establish the `story-analyzer` behavioral role and the supporting `user-story.spec.md` template to enable structured story intake.

## Tasks

<task>
<read_first>
- [SYSTEM.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/SYSTEM.md)
- [prompts/architect.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/prompts/architect.md) (reference for role structure)
</read_first>
<action>
Create [specs/user-story.spec.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/specs/user-story.spec.md) as a lightweight markdown template. 
It must include sections for:
- Agile Story (As a... I want... so that...)
- Acceptance Criteria (Checklist format)
- Context / Supporting Docs
</action>
<acceptance_criteria>
- [ ] File exists at `specs/user-story.spec.md`.
- [ ] Contains placeholders for Actor, Goal, and Benefit.
- [ ] Uses simple markdown headers.
</acceptance_criteria>
</task>

<task>
<read_first>
- [SYSTEM.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/SYSTEM.md) (§5 Meta-Prompts & Context)
- [01-CONTEXT.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/.planning/phases/01-foundations-parsing/01-CONTEXT.md)
</read_first>
<action>
Create [prompts/story-analyzer.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/prompts/story-analyzer.md).
Implement the following core logic:
1. **Behavioral Role**: Define identity as a "ServiceNow Story Analyzer & Solutions Architect".
2. **Dual-Input Logic**: Instructions to parse both raw chat text and referenced story files.
3. **Semantic Parsing**: Instructions to extract Actor, Goal, and Value regardless of exact phrasing.
4. **Acceptance Criteria Extraction**: Instructions to convert ACs into a formatted checklist.
5. **Clarification Gate**: Logic to stop and ask questions if the story is incomplete or missing motivation.
</action>
<acceptance_criteria>
- [ ] File exists at `prompts/story-analyzer.md`.
- [ ] Identity matches SNow-Claude standards.
- [ ] Includes explicit instructions for the "Clarification Gate".
- [ ] Includes logic for flexible parsing of "As a/I want/So that".
</acceptance_criteria>
</task>

<task>
<read_first>
- [SYSTEM.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/SYSTEM.md)
</read_first>
<action>
Update [SYSTEM.md](file:///c:/Users/91823/.gemini/antigravity/scratch/snow-claude/SYSTEM.md) §8 (Persona-Specific Sub-Prompts) to include the new `/story` command mapped to `prompts/story-analyzer.md`.
</action>
<acceptance_criteria>
- [ ] `/story` command is registered in the sub-prompts table.
- [ ] Description accurately reflects "User Story Analysis & Solutioning".
</acceptance_criteria>
</task>

## Verification
- Verify that `/story` activates the analyzer.
- Verify that the analyzer stops for a vague story (V-02).
- Verify that the analyzer correctly parses a complete story into Actor/Goal/Value/ACs (V-01).

---
*Created: 2026-04-09*
