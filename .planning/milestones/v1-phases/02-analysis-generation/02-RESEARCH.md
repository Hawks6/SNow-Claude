# Phase 2 Research: Analysis & Generation

## Objective
Establish the logic and structures required for the `/story` role to map user requirements to ServiceNow artifacts, assemble a Technical Solution Map, and prepare the spec for code generation by `/developer`.

## Technical Strategy

1. **The Technical Solution Map (Output Format):**
    - The `/story` prompt needs to guide the model to output a specific markdown structure.
    - Example: 
      ```markdown
      ## 🗺️ Technical Solution Map
      * **Trigger:** [What initiates the action]
      * **Logic:** [What happens]
      
      ### Proposed Artifacts
      1. **[Artifact Type] - [Proposed Name]**
         - **Logic:** [Summary of logic]
         - **Context Required:** `@contexts/business-rules.md`, `@contexts/gliderecord.md`
      ```

2. **Context Auto-Selection:**
    - The prompt will contain a mapping table instruction. E.g., "If artifact is a Business Rule, you MUST list `@contexts/business-rules.md` in Context Required." 
    - This allows the human operator to easily copy-paste the required files into the next prompt window, or tooling (like Claude Desktop) to auto-fetch.

3. **Handoff Protocol:**
    - The output of `/story` must end with an explicit instruction block for `/developer`.
    - Example: "When approved, use `/developer` with this specification to generate the code."

4. **Quality Gate Formalization:**
    - The `/developer` prompt (already existing, but potentially needing a tweak) or the output of the `/story` prompt needs to remind the user that the code will be evaluated against the 5 Gates (Security, Performance, Maintainability, Upgrade Safety, Testability).
    - *Correction:* The roadmap states we need to integrate the SYSTEM.md Quality Gates. The `/story` role specifies *what* to build, and `/developer` builds it. To satisfy this, `/story` should mandate that the Technical Solution Map includes a "Quality Gate Focus" for the developer. Let's research if we should update `SYSTEM.md` or `story-analyzer.md`.
    - **Decision:** Update `prompts/story-analyzer.md` to include a "Quality Gate Considerations" section per artifact in the Technical Solution Map.

## Integration Points
- `prompts/story-analyzer.md`: Vastly expand the "Output Format" section.
- `SYSTEM.md`: Ensure the naming disclaimer rule is added to the general rules or the meta-prompt output.

---
*Created: 2026-04-09*
