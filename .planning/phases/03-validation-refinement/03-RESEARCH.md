# Research: Phase 3 Validation & Refinement

## 1. Testing LLM Behavioral Roles
To validate the `/story` role effectively, we must move beyond simple unit tests and into **Behavioral UAT**.
- **Scenario Injectors**: Using stories that intentionally lack detail to test "Ambiguity Detection".
- **Anti-Pattern Check**: Verifying that the role does NOT recommend "Business Rules" when a "Flow" is more appropriate (per ServiceNow best practices).
- **Output Consistency**: Ensuring the Technical Solution Map follows the exact schema required for `/developer` consumption.

## 2. Gap Detection Protocol (CORE-06)
Foundations for detecting what the user *didn't* specify but the platform *requires*.
- **Mandatory Fields**: If the story mentions a new table, the analyzer must check if "Role-based access" or "Auditing" is mentioned.
- **Integration Handshakes**: If a REST API is mentioned, check if "Authentication" or "Error Handling" is defined in the story.
- **Logic**: 
  - Scan story for key terms (e.g., "external system").
  - Match against a checklist of "Complementary Artifacts".
  - If missing, append a "Gap Report" to the Solution Map.

## 3. Playbook Detection Refinement
Playbooks are best suited for:
- Processes spanning >2 teams.
- Steps requiring significant human-in-the-loop interaction.
- Dynamic branching based on complex data.
- **Detection Trigger**: Look for transition verbs like "handoff", "assign to next group", "wait for approval from manager".

---
*Research synthesized: 2026-05-10*
