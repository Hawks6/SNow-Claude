# Project Structure

SNow-Claude is organized as a flat, modular directory of markdown files.

## Directory Layout

- `/`
  - `SYSTEM.md`: Core system instructions and coding standards.
  - `QUICKSTART.md`: Onboarding guide for new users.
  - `README.md`: Project overview and documentation.
  - `GEMINI.md`: Project-specific GEMINI context.
  - `LICENSE`: MIT License file.

- `contexts/`
  - `_base.md`: Core platform knowledge (Scoping, Glide APIs).
  - `atf.md`: Automated Test Framework patterns.
  - `business-rules.md`: Patterns for server-side logic.
  - `client-scripts.md`: UI-layer logic and policies.
  - `flow-designer.md`: Low-code automation patterns.
  - `gliderecord.md`: Database access patterns.
  - `now-experience.md`: UI Builder and modern UX patterns.
  - `performance.md`: Optimization and caching.
  - `playbooks.md`: PAD and Playbook patterns.
  - `rest-api.md`: Integration and endpoint patterns.
  - `script-includes.md`: Class patterns and GlideAjax.
  - `security.md`: ACLs and data protection.
  - `service-portal.md`: Widget and portal patterns.
  - `update-sets.md`: Deployment and lifecycle management.

- `prompts/`
  - `architect.md`: Design-time strategy instructions.
  - `debugger.md`: Diagnostic and investigative instructions.
  - `developer.md`: Code generation instructions.
  - `reviewer.md`: Qualitative audit instructions.
  - `story-analyzer.md`: User story breakdown and analysis.

- `specs/`
  - `_spec-guide.md`: Format guide for YAML-based specs.
  - `business-rule.spec.md`: Template for Business Rules.
  - `client-script.spec.md`: Template for Client Scripts.
  - `flow.spec.md`: Template for Flow Designer.
  - `integration.spec.md`: Template for integrations.
  - `playbook.spec.md`: Template for PAD Playbooks.
  - `rest-endpoint.spec.md`: Template for REST APIs.
  - `scoped-app.spec.md`: Template for Scoped Applications.
  - `script-include.spec.md`: Template for Script Includes.
  - `user-story.spec.md`: Template for User Stories.
  - `widget.spec.md`: Template for Service Portal Widgets.

- `knowledge/`
  - `anti-patterns.md`: Catalog of common platform mistakes.
  - `api-patterns.md`: Reference for 15+ common API snippets.
  - `naming-conventions.md`: Standards for artifact naming.
  - `scoping-rules.md`: Reference for cross-scope connectivity.
  - `table-reference.md`: Core system table schema reference.

## Key File Locations
- **Brain**: `SYSTEM.md`
- **Knowledge**: `contexts/*.md` & `knowledge/*.md`
- **Roles**: `prompts/*.md`
- **Templates**: `specs/*.md`

