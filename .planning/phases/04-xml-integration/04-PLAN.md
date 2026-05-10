# Plan: Phase 4 XML Context Integration

**Goal:** Enable SNow-Claude to "read" the instance state via XML exports.

## Wave 1: Research & Schema Mapping
**Objective:** Define the mapping between ServiceNow XML and SNow-Claude context.

- [ ] **Task 4.1: XML Schema Audit**
  - Analyze XML structure for Business Rules, Script Includes, and Client Scripts.
  - Identify key tags: `<script>`, `<collection>`, `<when>`, `<condition>`.
- [ ] **Task 4.2: Create `contexts/xml-metadata.md`**
  - Document the mapping for the LLM.

## Wave 2: The XML Analyzer & Decomposition
**Objective:** Create the specialized role for ingestion, including large file handling.

- [ ] **Task 4.3: Implement `prompts/xml-analyzer.md`**
  - Define the persona: "ServiceNow XML Architect".
  - **Decomposition Logic**: Add protocols for breaking down "Entire App XML" into individual artifact chunks.
  - Build the extraction logic: Convert XML chunks to a "Developer-Friendly Summary".
- [ ] **Task 4.4: Sequential Ingestion Protocol**
  - Define how the user should provide multiple chunks if the file is too large for one prompt.
  - Verification of extraction logic with a large "App Scope" sample.

## Wave 3: Intelligent Gap Analysis
**Objective:** Update the `/story` skill to use the XML context.

- [ ] **Task 4.5: Update `story-analyzer.md`**
  - Add logic: `if (xml_context) { compare(story, xml_context) }`.
  - Refine the output Solution Map to highlight "Already Implemented" vs "To Be Added".
- [ ] **Task 4.6: Final Validation**
  - Run end-to-step scenario: Story + XML -> Gap Analysis -> Refined Solution Map.

---
*Plan created: 2026-05-10*
