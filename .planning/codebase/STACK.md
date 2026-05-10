# Technology Stack

This document outlines the core technologies and dependencies used in the SNow-Claude project.

## Core Runtime
- **LLM**: Claude AI (Anthropic)
- **Interface**: Claude.ai (Web), Claude Projects, Claude Desktop, or Claude API.

## Project Framework
- **Architecture**: Meta-prompting and context engineering system.
- **Implementation**: Composable markdown modules (.md files).
- **Tooling**: Zero-dependency system. No compilation or installation required.

## Target Platform (ServiceNow)
- **Engine**: Rhino JavaScript (ES5+) with some ES6 support in newer releases.
- **Releases**: Washington DC, Xanadu, Vancouver, Utah.
- **Key ServiceNow APIs**:
    - GlideRecord / GlideRecordSecure
    - GlideAggregate
    - GlideAjax
    - GlideSystem (gs)
    - Flow Designer & IntegrationHub APIs
    - **Process Automation Designer (PAD) & Playbook APIs**
    - REST API Framework
    - Now Experience / UIB Framework


## Configuration & Management
- **Version Control**: Git / GitHub
- **Documentation**: Markdown-based system
- **Deployment**: Copy-paste or manual upload to Claude Projects
