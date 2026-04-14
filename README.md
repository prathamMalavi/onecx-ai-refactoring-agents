# OneCX Refactoring Agents

> AI-assisted refactoring and migration workspace for OneCX applications

## Overview

This repository contains AI-powered agents designed to automate and streamline refactoring tasks across OneCX microapplications. Using VS Code agents and the GitHub Copilot API, these tools guide developers through complex migrations, version upgrades, and code modernization with minimal manual effort.

## Repository Structure

```
onecx-refactoring-agents/
├── LICENSE                    — Apache 2.0 licensing
├── README.md                  — This file
└── angular/                   — Angular agents
    └── updates/               — Angular version migration agents
        └── .github/           — Agent configuration and templates
```

## Key Features
- **AI-Assisted Workflows**: Leverages GitHub Copilot agents for intelligent task execution
- **Modular Architecture**: Clear separation between orchestration, planning, execution, and validation
- **Auto-Injection System**: Core rules automatically applied to all agent invocations without manual setup
- **Evidence-Based Progress**: Detailed tracking of all migration tasks with validation at each phase

## Folders

### [angular/](angular/)
Contains Angular-specific refactoring and migration agents for OneCX microapplications.

## Getting Started

Refer to the appropriate tech-stack folder's README for setup, available agents, and usage:
- **[Angular](angular/)** — for Angular refactoring and migration agents

## Architecture Principles
1. **Orchestrator Pattern**: Single user-facing agent coordinates all work
2. **Subagent Execution**: Specialized agents handle planning, execution, and validation
3. **Automatic Rule Injection**: Core rules apply to all agents without redundant setup
5. **Evidence-Driven**: All decisions backed by official documentation and task logs

## License
Apache 2.0 — see [LICENSE](LICENSE) for details