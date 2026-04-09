# Phase 2 Context: Analysis & Generation

## Objective
Implement the intelligence layer of the `snow-story` skill. This layer maps parsed requirements to ServiceNow artifacts, automatically selects relevant context modules, and guides the user through technical design before generating the final code.

## Key Decisions (from discussion)

1.  **Staged Execution (Design -> Generate):**
    -   **Decision:** The skill will NOT immediately generate code. It will first present a Technical Solution Map (Technical Design Specification).
    -   **Rationale:** Provides an interactive Q&A step. This builds shared context between the user and the model, allowing for course correction and design refinement before committing to code generation.
2.  **Automated Context Loading:**
    -   **Decision:** The system will automatically select and load the necessary context modules (`contexts/*.md` or `SYSTEM.md` sub-sections) based on the identified artifacts.
    -   **Rationale:** Reduces friction for the user and ensures the code generator always has the correct rules loaded without manual prompting.
3.  **Naming Conventions & Scoping:**
    -   **Decision:** The system will assume standard default naming conventions (e.g., `x_vendor_app`, standard prefixes). However, the output *must* include a persistent disclaimer instructing the user to align the names with their organization's specific requirements.
    -   **Rationale:** Provides a working baseline while acknowledging that every ServiceNow instance has unique governance rules.

## Core Capabilities to Implement
-   **Artifact Identification Logic:** Mapping requirement types (e.g., client action vs. server logic) to the correct ServiceNow components.
-   **Handoff Protocol:** Establishing the handoff from the `/story` output (Design) to the `/developer` (or a sub-role) for the actual code implementation.
-   **Quality Gate Automation:** Translating the 25-point mental checklist in `SYSTEM.md` into an explicit compliance output table for generated code.

---
*Created: 2026-04-09*
