# Agent team

We are using GitHub Copilot CLI in a GitHub Codespace to orchestrate a specialist
team that will build Mona's Project Pulse dashboard.

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 | Coordinates the workflow, delegates scoped work, manages dependencies, and validates the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 | Researches the repository and requirements, then creates the implementation plan, file assignments, dependencies, and validation expectations. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.1 Pro | Defines the dashboard's information hierarchy, visual design, accessibility, responsive behavior, and polished project-card experience. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 | Implements the static HTML, CSS, JSON data, and assigned VS Code launch configuration while validating the result. | `.github/agents/coder.agent.md` |

The Orchestrator will ask the Planner to plan first, then coordinate Designer
and Coder work with explicit file ownership. The learner controls staging,
committing, and pushing through Copilot CLI prompts.
