# Agent team

The Mona's Project Pulse dashboard will be built by a coordinated team of four custom agents. GitHub Copilot CLI in a Codespace orchestrates the team, assigning work by responsibility and file scope.

## Custom agents

### Orchestrator

- **Target model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Coordinates the Planner, Coder, and Designer; breaks the dashboard work into phases, manages dependencies and file ownership, delegates tasks, verifies the integrated result, and reports the outcome.
- **Definition:** `.github/agents/orchestrator.agent.md`

### Planner

- **Target model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Researches the repository, documentation, dependencies, and edge cases, then produces an implementation plan with ordered steps, file assignments, dependencies, parallelization opportunities, risks, and validation expectations. The Planner does not write code.
- **Definition:** `.github/agents/planner.agent.md`

### Designer

- **Target model:** Gemini 3.1 Pro (copilot)
- **Responsibility:** Defines and implements the Project Pulse user experience, including information hierarchy, accessibility, responsive behavior, visual clarity, project cards, status badges, priority treatment, spacing, typography, and deterministic styling hooks such as `.dashboard` and `.project-card`.
- **Definition:** `.github/agents/designer.agent.md`

### Coder

- **Target model:** GPT-5.5 (copilot)
- **Responsibility:** Writes code, fixes bugs, and implements logic within the file scope assigned by the Orchestrator, following clear, deterministic, testable practices with explicit errors. For Project Pulse runnable app work, the Coder can configure `.vscode/launch.json` to use `${workspaceFolder}/app` as its working directory and open `index.html`.
- **Definition:** `.github/agents/coder.agent.md`

## Collaboration model

The Orchestrator starts by requesting a repository-informed plan from the Planner. It then divides that plan into phases with explicit file assignments and dependencies, delegating UI/UX and visual design work to the Designer and implementation work to the Coder. Independent work may run in parallel when file scopes do not overlap; dependent or overlapping work runs sequentially. Finally, the Orchestrator verifies that the integrated Project Pulse dashboard works together and reports the outcome, while all agents leave staging, commits, and pushes to the learner.
