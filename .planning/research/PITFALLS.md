# Pitfalls Research: SNow-Claude User Story Skill

## Domain
Common mistakes when building LLM prompt-based user story analysis skills.

## Critical Pitfalls

### 1. Monolithic Prompt Syndrome
**Risk:** Cramming all logic (parsing, analysis, generation, validation) into a single massive prompt that exceeds context window or produces inconsistent output.
**Warning Signs:** Prompt file exceeds 300 lines; output quality degrades on complex stories.
**Prevention:** Use internal chain-of-thought stages with clear section markers. Each stage has its own instructions, even within a single prompt file.
**Phase:** Should be addressed in Phase 1 (skill prompt design).

### 2. Context Overload
**Risk:** Loading ALL context modules regardless of what the story needs, wasting context window budget and confusing the LLM.
**Warning Signs:** Irrelevant artifact types appearing in output; mentioning REST APIs when the story is about a UI change.
**Prevention:** The skill prompt must include explicit artifact-to-context mapping rules. Only load what's needed.
**Phase:** Should be addressed in Phase 1 (context selection logic).

### 3. Vague Story → Hallucinated Implementation
**Risk:** When a user story is underspecified (no table, no scope, no acceptance criteria), the LLM invents details that may be wrong or dangerous.
**Warning Signs:** Generated code references tables/fields that don't exist; hardcoded assumptions about business logic.
**Prevention:** Build a "completeness check" stage before generation. If critical fields are missing, ASK — don't assume. This mirrors the existing spec protocol (SYSTEM.md §4: "If a spec field is blank, ask before assuming").
**Phase:** Should be addressed in Phase 1 (parsing stage).

### 4. Breaking Default Behavior
**Risk:** The new skill interferes with SNow-Claude's normal operation when not explicitly invoked.
**Warning Signs:** Regular `/developer` or `/architect` commands start producing story-analysis output; context modules loaded incorrectly.
**Prevention:** The skill MUST be opt-in only — activated by `/story` command or by loading `story-analyzer.md`. It should not alter any existing files' behavior. Registration in SYSTEM.md §8 is additive only.
**Phase:** Should be addressed in Phase 2 (SYSTEM.md integration).

### 5. Ignoring Existing Patterns
**Risk:** The skill generates code that doesn't follow the conventions established by `developer.md`, `_base.md`, or the naming conventions in SYSTEM.md §5.
**Warning Signs:** Generated artifacts use different naming patterns; missing JSDoc; no error handling.
**Prevention:** The skill prompt must explicitly reference SYSTEM.md §5 (Code Standards) and §7 (Response Format) as mandatory output constraints. It should reuse the exact output template from `developer.md`.
**Phase:** Should be addressed in Phase 1 (generation stage).

### 6. No Dependency Ordering
**Risk:** Generating artifacts in random order, making it unclear what depends on what and what to deploy first.
**Warning Signs:** Client Script references a Script Include that's listed later; Business Rule calls a function not yet defined.
**Prevention:** Always generate in dependency order: Constants → Script Includes → Business Rules → Client Scripts → Flows → Tests. This matches `developer.md` §5 (Multi-Artifact Generation).
**Phase:** Should be addressed in Phase 1 (output formatting).

---
*Research: Pitfalls — SNow-Claude User Story Skill*
