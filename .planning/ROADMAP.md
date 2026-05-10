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

### Phase 3: Validation & Refinement
**Goal:** Stress-test the `/story` skill with complex scenarios and refine the handoff logic.

- [ ] Execute V-01 through V-04 validation tests from Phase 2.
- [ ] Refine `story-analyzer.md` based on test results (e.g., better Playbook detection).
- [ ] Add "Gap Detection" (CORE-06) foundations.
- [ ] Update `SYSTEM.md` with any learned patterns.

**Success Criteria:**
- All v1 validation scenarios pass with 100% accuracy.
- System correctly identifies at least one "ambiguous" story and asks for clarification instead of generating a map.
- Playbook detection is verified with a cross-team handoff scenario.

### Phase 4: XML Context Integration
**Goal:** Enable the `/story` skill to ingest and analyze ServiceNow XML exports for "As-Is" context.

- [ ] Implement CORE-08 (XML Metadata Extraction).
- [ ] Implement CORE-09 (As-Is vs. To-Be Gap Analysis).
- [ ] Add `prompts/xml-analyzer.md` (or integrate into `story-analyzer.md`).
- [ ] Update `SYSTEM.md` with XML handling protocols.

**Success Criteria:**
- System can parse a Business Rule XML and identify the script logic.
- System correctly identifies if a story requirement is already satisfied by the provided XML.
- Solution Map includes "Existing Artifacts" section when XML context is provided.

---

## Milestone 2: v2 - Advanced Orchestration
**Goal:** Handle complex, multi-artifact processes and automated testing.

### Phase 4: Multi-Artifact Orchestration
**Goal:** Implement logic to manage dependencies between generated artifacts.

- [ ] Implement CORE-07 (Multi-artifact orchestration).
- [ ] Implement GEN-04 (Dependency mapping).

---

## Future Milestones
- **v3 (Interactive Tuning)**: Conversation-driven story refinement.

---
*Roadmap updated: 2026-05-10*

