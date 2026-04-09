# Requirements: SNow-Claude User Story Skill

## v1 Requirements (Active)

### Core Analysis & Orchestration
- [ ] **CORE-01**: User story parsing — Extract actor, goal, and motivation from raw agile format ("As a..., I want..., so that...").
- [ ] **CORE-02**: SYSTEM.md integration — Enforce core principles (scoping, no hardcoding, security, performance) in all generated output.
- [ ] **CORE-03**: Artifact identification — Automatically determine the necessary ServiceNow artifacts (e.g., Business Rule, Script Include) based on the requirement.
- [ ] **CORE-04**: Context auto-selection — Identify and recommend/load relevant context modules from `contexts/*.md`.
- [ ] **CORE-05**: Quality gate verification — Run the 25-point mental checklist against generated artifacts.

### Templates & Generation
- [ ] **GEN-01**: Spec auto-filling — Generate filled specification templates for each identified artifact type.
- [ ] **GEN-02**: Code generation — Produce production-ready, spec-driven ServiceNow code following the prototype pattern and JSDoc standards.
- [ ] **GEN-03**: Dual input mode — Support user stories provided via inline chat text or by referencing a `.md` file.

## v2 Requirements (Deferred)

- **CORE-06**: Gap detection — Proactively identify and flag missing technical details (e.g., missing table names or specific triggers) before generation.
- **CORE-07**: Multi-artifact orchestration — Handle complex stories that require multiple interconnected artifacts with cross-references.
- **GEN-04**: Dependency mapping — Provide a visual or structured map of how the generated artifacts relate and their deployment order.
- **GEN-05**: ATF test generation — Automatically provide specific ATF test steps and assertions for the generated code.
- **GEN-06**: Acceptance criteria to ATF — Directly map story ACs to verifiable ATF test cases.

## Out of Scope
- Direct deployment to a live ServiceNow instance.
- Project management features (story points, backlog ranking).
- Processing multiple stories or epics in a single execution.

## Traceability
*Traceability matrix between requirements and roadmap phases will be populated in the next step.*

---
*Generated: 2026-04-09*
