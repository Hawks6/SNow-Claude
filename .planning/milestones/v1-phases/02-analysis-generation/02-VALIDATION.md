# Phase 2 Validation Strategy: Analysis & Generation

## Dimension 8: Verification & Proof

### V-01: Artifact Matrix Validation
**Goal:** Prove the analyzer correctly maps common requirements to the right ServiceNow artifact types.
- **Scenario A (Data change side-effect):** "When an incident state changes to Resolved, automatically update the parent problem."
    - **Expected Artifact:** Business Rule (After Update).
- **Scenario B (UI Interaction):** "Hide the assignment group field if category is Network."
    - **Expected Artifact:** UI Policy (or Client Script).
- **Scenario C (Reusable Logic):** "Create a script to calculate business hours for SLAs."
    - **Expected Artifact:** Script Include.

### V-02: Context Recommendation Check
**Goal:** Prove the system outputs the correct context references.
- **Test:** Run Scenario A (Business Rule).
- **Success:** The Proposed Artifacts section includes `@contexts/business-rules.md` (or equivalent context suggestion).

### V-03: Quality Gates Focus
**Goal:** Verify the system surfaces security/performance considerations *before* code generation.
- **Test:** Run a scenario requiring GlideRecord queries.
- **Success:** The Technical Solution Map includes a "Quality Gate Considerations" note that mentions Performance (indexing, GlideAggregate, etc.).

### V-04: Handoff Protocol
**Goal:** Prove the output includes instructions for next steps.
- **Success:** Output ends with clear instructions to review and then invoke `/developer`.

---
*Created: 2026-04-09*
