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
**Goal:** Transition from single-artifact analysis to full-stack lifecycle management with integrated self-validation.

### Phase 5: Multi-Artifact Orchestration & Self-Validation
**Goal:** Implement logic to manage dependencies and a "Self-Validation Protocol" to ensure solution correctness.

- [ ] Implement **Dependency Ordering**: Order artifacts by installation/build priority (e.g., Tables -> Properties -> Script Includes -> UI).
- [ ] Implement **Cross-Artifact Consistency**: Ensure shared variables/properties are consistent across all proposed artifacts.
- [ ] Implement **Internal Validation Protocol**: A mental "Simulation Step" where the model tests its own proposed logic against edge cases before final output.
- [ ] Implement **Transaction Impact Assessment**: Detect and flag potential recursion or performance loops.

**Success Criteria:**
- Solution Map includes a "Implementation Order" numbered list and "🔗 Dependency Map".
- Every Solution Map includes a "✅ Internal Validation" section confirming Claude's self-test.
- System flags potential recursion or performance loops in the "Transaction Impact Assessment" step.

### Phase 6: Advanced XML Context & Consistency
**Goal:** Deepen the integration with existing instance state and conflict detection.

- [ ] Implement **Conflict Detection**: Flag contradictions between story requirements and provided XML logic.
- [ ] Implement **Version History Awareness**: Detect if a provided XML is an older version than a previously analyzed one.

**Success Criteria:**
- System identifies at least one "Direct Contradiction" in a stress-test scenario.
- Gap Analysis distinguishes between "New Feature" and "Existing Logic Refinement".

---

## Future Milestones
- **v3 (Interactive Tuning)**: Conversation-driven story refinement.

---
*Roadmap updated: 2026-05-10*

