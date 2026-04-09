# Roadmap: SNow-Claude User Story Skill

## Milestone 1: v1 MVP - User Story Analyzer

### Phase 1: Foundations & Parsing
**Goal:** Define the interaction model for user stories and implement the parsing logic.

- [ ] Create `specs/user-story.spec.md` template.
- [ ] Create `prompts/story-analyzer.md` (Stage 1: Parsing logic).
- [ ] Implement dual input support (Chat vs File).

**Success Criteria:**
- Model extracts "As a [Actor], I want [Goal], so that [Value]" from raw text.
- Model identifies if a story is "Complete" or "Vague".

**Requirements addressed:** CORE-01, GEN-03

---

### Phase 2: Analysis & Generation
**Goal:** Implement the intelligence layer to map requirements to ServiceNow artifacts and generate code.

- [ ] Implement Artifact Identification logic in `story-analyzer.md`.
- [ ] Implement Context Auto-Selection logic.
- [ ] Implement Spec Auto-Filling and Code Generation logic.
- [ ] Integrate SYSTEM.md Quality Gates.
- [ ] Register `/story` command in `SYSTEM.md`.

**Success Criteria:**
- Entering a story results in a formatted response with code, config, and tests.
- Generated code passes a 25-point mental checklist audit.
- SNow-Claude operates normally when `/story` is not used.

**Requirements addressed:** CORE-02, CORE-03, CORE-04, CORE-05, GEN-01, GEN-02

---

## Future Milestones
- **v2 (Advanced Orchestration)**: Gap detection, multi-artifact ordering, ATF assertions.
- **v3 (Interactive Tuning)**: Conversation-driven story refinement.

---
*Roadmap created: 2026-04-09*
