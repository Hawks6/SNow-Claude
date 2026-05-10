# Technical Concerns & Debt

This document tracks known issues, technical debt, and future concerns for the SNow-Claude project.

## 1. Context Synchronization
- **Risk**: As ServiceNow releases new versions (GL, Washington, Xanadu), the context modules need manual updates to stay accurate.
- **Debt**: Currently no automated way to verify module accuracy against the latest ServiceNow API documentation.
- **New Roles**: The `/story` and `/playbook` roles introduce new logic that must stay synced with the `story-analyzer.md` and `playbooks.md` modules.

## 2. LLM Performance
- **Token Usage**: Loading `SYSTEM.md` plus 3-4 context modules consumes significant "pre-fill" tokens. While modern models (Claude 3.5, Gemini 1.5/2.0) handle this well, it can increase latency and cost for API-based implementations.
- **Hallucination Risk**: Despite deep context, LLMs may still hallucinate legacy APIs (e.g., using `gr.next()` instead of `gr._next()` in certain scoped contexts) if not strictly guided.

## 3. ServiceNow Platform Changes
- **Rhino vs. ES6**: The transition to ES6 in newer ServiceNow releases (like Xanadu) creates a "dual-mode" requirement where SNow-Claude must know when to use `var` vs `const/let`.
- **Platform UX (UIB)**: The "Now Experience" and "UI Builder" patterns are evolving rapidly, making the `now-experience.md` module subject to frequent obsolescence.

## 4. Maintenance Overhead
- **File Proliferation**: 40+ files require significant mental overhead to navigate for a maintainer.
- **Documentation Drift**: The `README`, `QUICKSTART`, and `GEMINI.md` must be manually updated whenever file structures change.
- **Skill Expansion**: The addition of `snow-story` logic increases the surface area for logic errors in role-playing.

## 5. Security Gates
- **Dynamic Policy**: Quality gates are currently static in `SYSTEM.md`. Future versions should consider dynamic gates based on the specific artifact type being generated (e.g., specific gates for Playbooks).

