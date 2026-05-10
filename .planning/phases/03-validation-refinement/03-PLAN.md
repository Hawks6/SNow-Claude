# Plan: Phase 3 Validation & Refinement

**Goal:** Ensure v1 stability and prepare for v2 features.

## Wave 1: Validation Loop
**Objective:** Execute existing validation scenarios and document gaps.

- [ ] **Task 3.1: Scenario V-01 (Simple Script)**
  - Input: "Create a client script to hide the 'Priority' field on the Incident table for non-admins."
  - Output: Verify Artifact ID, Naming Disclaimer, and correct Context recommendation.
- [ ] **Task 3.2: Scenario V-02 (Multi-Step Logic)**
  - Input: Complex story involving a BR and a Script Include.
  - Output: Verify "Technical Solution Map" structure and `/developer` handoff.
- [ ] **Task 3.3: Scenario V-03 (Cross-Team Playbook)**
  - Input: Story with handoffs between Service Desk and Security.
  - Output: Verify PAD Playbook recommendation and trigger logic.
- [ ] **Task 3.4: Scenario V-04 (Ambiguity Detection)**
  - Input: "I want to automate the user process." (Vague).
  - Output: System MUST ask clarifying questions instead of generating code.

## Wave 2: Refinement & Hardening
**Objective:** Fix issues found in Wave 1 and update role logic.

- [ ] **Task 3.5: Refine `story-analyzer.md`**
  - Adjust trigger keywords for Playbook detection.
  - Standardize the "Gap Report" format.
  - Ensure 100% compliance with Naming Governance disclaimers.
- [ ] **Task 3.6: Update `SYSTEM.md`**
  - Integrate any new "Senior Architect" patterns discovered during validation.

## Wave 3: v2 Bridge (CORE-06)
**Objective:** Implement initial Gap Detection logic.

- [ ] **Task 3.7: Implement Gap Detection Prototypes**
  - Add logic to identify missing security roles or auditing requirements in stories.
  - Update `story-analyzer.md` to flag these gaps in a separate section of the Solution Map.

---
*Plan created: 2026-05-10*
