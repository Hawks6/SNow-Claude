# Testing Patterns

This document outlines the testing strategies and patterns for ServiceNow development using SNow-Claude.

## 1. Automated Test Framework (ATF)
ServiceNow's native ATF is the primary tool for testing.

### Test Categories
- **Unit Tests**: For Script Includes (business logic).
- **Functional Tests**: For Business Rules and UI Actions.
- **Integration Tests**: For REST APIs and external connectivity.
- **UI Tests**: For Client Scripts and UI Policies (use sparingly).
- **Process Tests**: For **Flows, Subflows, and Playbook Activities** (validate state transitions).

## 2. Testing Principles
- **One Test Suite per Artifact**: Map each major Script Include or Business Rule to an ATF suite.
- **Mocking**: Use "Step Configurations" in ATF to set up data prerequisites rather than relying on existing demo data.
- **Validation**:
    - Server-side: Check field values, record counts, and log entries.
    - Client-side: Check field visibility, read-only state, and mandatory state.
    - **Process Layer**: Check record state changes triggered by Flows or PAD activities.

## 3. Test Documentation
SNow-Claude responses include **ATF Test Recommendations**.
- **Scenario**: Happy path, edge cases, error conditions.
- **Expected Result**: Specific field state or return value.

## 4. specialized Testing
- **REST API**: Validate HTTP status codes (200, 201, 400, 401, 404), payload schema, and security.
- **Flow Designer**: Use the "Run Flow" test step to trigger flows with specific inputs.
- **Playbooks**: Manual verification of workspace UI state + ATF for record-level updates.

