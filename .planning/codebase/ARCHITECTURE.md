# Architecture

SNow-Claude follows a **Composable Context Injection** architecture designed for Large Language Models (LLMs), specifically Claude.

## Design Philosophy
The system is built on the principle of minimal context footprint with maximum expertise. Instead of a monolithic prompt, it uses modular components that can be combined as needed.

## Architectural Layers

### 1. The Core Persona (SYSTEM.md)
The "Brain" of the system. It establishes:
- Expert identity as a Senior ServiceNow Architect/Developer.
- Universal coding standards (logging, error handling, naming).
- Quality gate protocols (25-point mental checklist).

### 2. Behavioral Roles (prompts/)
These modules switch the system's operational mode without changing its core knowledge:
- `architect.md` (`/architect`): Design-first approach, trade-off analysis.
- `developer.md` (`/developer`): High-efficiency code generation from specs.
- `reviewer.md` (`/reviewer`): Critical analysis against platform anti-patterns.
- `debugger.md` (`/debugger`): Systematic root-cause analysis and platform diagnostics.
- `story-analyzer.md` (`/story`): User story breakdown, artifact identification, and Playbook detection.

### 3. Knowledge Base (contexts/)
Composable modules containing deep platform knowledge:
- `_base.md`: Essential platform APIs and scoping rules (Always active).
- Specialized modules: `business-rules.md`, `script-includes.md`, `client-scripts.md`, `gliderecord.md`, `flow-designer.md`, `playbooks.md`, `atf.md`, etc.

### 4. Specification Engine (specs/)
Standardized input format using YAML-in-Markdown. This ensures that the LLM receives structured, unambiguous requirements, reducing hallucinations and improving consistency.

## Data Flow
1. **User Request** (e.g., Raw Story or Spec) → 2. **Role Activation** (e.g., `/story`) → 3. **Context Selection** (System + Role + Domain Modules) → 4. **Spec Generation/Filling** → 5. **LLM Code Generation** → 6. **Quality Gate Verification**.

