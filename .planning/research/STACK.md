# Stack Research: SNow-Claude User Story Skill

## Domain
LLM meta-prompting skill for ServiceNow user story analysis & artifact generation.

## Recommended Stack

### Core Runtime
| Component | Choice | Version/Detail | Confidence | Rationale |
|-----------|--------|---------------|------------|-----------|
| LLM | Claude AI | Claude 3.5/4+ | ★★★★★ | Already the target runtime for SNow-Claude |
| Implementation | Markdown modules | .md files | ★★★★★ | Matches existing zero-dependency architecture |
| Input Format | YAML-in-Markdown | Same as existing specs | ★★★★★ | Consistent with spec engine patterns |

### Skill Architecture
| Component | Choice | Rationale |
|-----------|--------|-----------|
| Skill Prompt | `prompts/story-analyzer.md` | New behavioral role alongside architect/developer/reviewer/debugger |
| Story Spec | `specs/user-story.spec.md` | Structured input template for formal story submission |
| Orchestration Logic | Embedded in skill prompt | Chain-of-thought decomposition within a single prompt pass |

### What NOT to Use
| Anti-Choice | Why |
|-------------|-----|
| External APIs/tooling | Violates zero-dependency principle |
| JSON configuration files | Markdown is the standard; keep consistency |
| Multi-turn agent orchestration | Overcomplicated for a single-skill addition |
| Custom scripting/build steps | Framework is copy-paste into Claude Projects |

## Key Insight
The existing SNow-Claude architecture already has all the primitives needed. The skill is a **new behavioral role** (like `/architect` or `/developer`) combined with **internal prompt chaining** (analyze → select contexts → generate artifacts). No new infrastructure is needed.

---
*Research: Stack — SNow-Claude User Story Skill*
