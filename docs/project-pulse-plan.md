# Project Pulse Implementation Plan

## Summary

Build Mona’s Project Pulse as a small, static dashboard that lets contributors scan active projects, owners, status, recent activity, priority/risk, and a concise contributor-facing summary. The repository brief requires three frontend assets under `app/` and a VS Code launch configuration that serves the app and opens `index.html`, rather than exposing a directory listing.

The implementation should use the repository’s custom-agent workflow:

- **Orchestrator** coordinates phases, preserves file ownership, and performs integration review.
- **Planner** (this document) defines the work, dependencies, risks, and validation.
- **Designer** defines the information hierarchy, accessibility, responsive behavior, and visual system.
- **Coder** implements the static page, fixture data, and launch configuration.

No framework or package dependency is currently present or required. The app should remain deterministic and runnable with Python’s built-in HTTP server.

## Ordered implementation steps

1. **Confirm the contract and ownership**
   - Use `.github/project-pulse-brief.md` as the product contract.
   - Use `.github/agents/designer.agent.md` and `.github/agents/coder.agent.md` to preserve specialist boundaries.
   - Confirm the four implementation files and keep all other repository files unchanged.

2. **Agree on the data shape**
   - Create a top-level `projects` array in `app/project-data.json`.
   - Every project must include `name`, `owner`, `status`, `recentActivity`, and `priority`.
   - Include a short `summary` field as the clearest way to satisfy the brief’s contributor-friendly summary requirement.
   - Use deterministic sample values that exercise active, steady, at-risk, and completed or otherwise distinguishable states.

3. **Define the user experience**
   - Designer specifies the page hierarchy: Project Pulse heading/context, optional aggregate context, and a scannable grid/list of project cards.
   - Define semantic labels and stable hooks before Coder finalizes markup. Required visual hooks include `.dashboard` and `.project-card`.
   - Make status and priority understandable as text, not through color alone; specify focus, contrast, wrapping, and narrow-screen behavior.

4. **Implement the page and data**
   - Coder creates `app/index.html` with the exact title **Project Pulse**, semantic structure, stylesheet reference, and data-loading/rendering behavior.
   - Render one visible `.project-card` per project and show name, owner, status, recent activity, priority, and summary.
   - Coder creates `app/project-data.json` with valid JSON and the agreed fields.
   - Include an explicit loading, empty, and data-error state so malformed or unavailable data does not silently produce a misleading dashboard.

5. **Implement the visual system**
   - Designer creates `app/styles.css` with the required `.dashboard` and `.project-card` selectors.
   - Provide polished typography, spacing, rounded cards, shadows, readable badges, status/priority treatments, visible keyboard focus, and responsive layout without horizontal scrolling.
   - Reconcile any class or semantic-hook mismatch with Coder before integration is considered complete.

6. **Configure the local preview**
   - Coder creates `.vscode/launch.json` as strict JSON with no comments.
   - Add the exact configuration name **Run Project Pulse Dashboard**.
   - Use `python3 -m http.server 5500`, set `cwd` to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`.
   - The launch target must open the dashboard page directly, not the `app/` directory listing.

7. **Integrate and hand off**
   - Orchestrator checks that JSON field names, HTML rendering logic, CSS selectors, and launch settings agree.
   - Resolve defects through the owning agent, then run the validation pass described below.
   - Do not stage, commit, or push as part of implementation; repository instructions leave Git control to the learner.

## File assignments

| File | Owner | Scope and acceptance intent |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic dashboard shell, exact Project Pulse title, stylesheet/data references, project-card rendering, visible required fields, and loading/empty/error states. Coordinate required hooks with Designer. |
| `app/styles.css` | Designer | Complete visual system and responsive behavior, including `.dashboard`, `.project-card`, badges, spacing, typography, contrast, rounded corners, shadows, and focus states. |
| `app/project-data.json` | Coder | Valid deterministic fixture data with top-level `projects`; each item has `name`, `owner`, `status`, `recentActivity`, `priority`, and the agreed `summary`. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named **Run Project Pulse Dashboard**, using the app working directory, Python server command, and direct `index.html` browser URL. |
| `docs/project-pulse-plan.md` | Planner | This repository-informed plan only; it must not contain dashboard implementation. |

### Designer responsibilities

- Translate the brief into a clear, contributor-first information hierarchy.
- Define card layout, status/priority presentation, typography, spacing, responsive breakpoints, and visual affordances.
- Ensure accessibility: semantic expectations, contrast, visible focus, text alternatives to color, and usable behavior on small screens.
- Own only `app/styles.css`; communicate required class/data hooks to Coder rather than editing Coder-owned files.
- Recommend the visual and accessibility checks needed during integration.

### Coder responsibilities

- Implement `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` only.
- Keep rendering deterministic and errors explicit; do not add a framework or package without a demonstrated need.
- Match the Designer’s agreed hooks and the JSON contract.
- Use strict JSON for both data and launch configuration; ensure the launch setup serves `app/` and opens `index.html`.
- Validate assigned files and report integration issues to the Orchestrator.

## Dependencies

- The brief and agent definitions are prerequisites for all implementation decisions.
- The required JSON field contract must precede final rendering logic in `app/index.html`.
- Designer’s information hierarchy and CSS hooks must be communicated before Coder finalizes card markup.
- `app/styles.css` depends on the final HTML hooks, while its initial layout exploration can proceed independently from data content.
- `.vscode/launch.json` depends on the fixed app location and entry point, but not on visual styling.
- Browser preview and end-to-end validation depend on all four deliverables being present and mutually consistent.
- No external runtime dependency is expected; Python 3 and VS Code’s browser/debug launch support are the only environment assumptions.

## Parallel and sequential work

### Work that can run in parallel

- Orchestrator/Planner repository review and Designer review of the brief and design-agent requirements can happen in parallel before implementation.
- After the minimum JSON contract and required hooks are agreed, Coder can draft `app/project-data.json` while Designer develops `app/styles.css`.
- Coder can prepare `.vscode/launch.json` in parallel with styling because it only depends on the app directory and entry point.

Parallel work requires exclusive file ownership: Designer edits `app/styles.css`; Coder edits `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.

### Work that must run sequentially

1. Repository and brief review precedes final phase assignment.
2. Data fields and Designer hook names precede final HTML rendering integration.
3. HTML/CSS reconciliation follows both specialists’ work.
4. All four deliverables must be integrated before launch and browser validation.
5. Fixes discovered in integration return to the owning agent and are revalidated by the Orchestrator.

## Edge cases

- Missing, malformed, empty, or unexpectedly shaped JSON must show a readable in-page state rather than an uncaught failure.
- Missing optional `summary`, unknown status, or unknown priority should fall back to sensible text/neutral styling instead of hiding content.
- Long names, owners, activity text, and summaries must wrap without overflowing cards.
- Empty project arrays need an explicit “no projects” state.
- Narrow viewports must avoid horizontal scrolling and preserve readable controls/content.
- Status and priority must remain meaningful for color-blind users and users with reduced color perception.
- Non-interactive cards should not be given misleading button semantics; any actual controls need keyboard access and visible focus.
- `fetch`-based loading should be previewed over HTTP, not `file://`, because browser security behavior differs.
- The launch configuration must open `index.html`, not a server directory index.

## Validation expectations

- Confirm this document contains the required sections and explicit assignments for all four implementation files.
- Parse `app/project-data.json` and verify a top-level `projects` array; verify every project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Inspect `app/index.html` for the exact `Project Pulse` title, `styles.css` and `project-data.json` references, `.project-card` rendering, and visible status/activity/priority/summary output.
- Inspect `app/styles.css` for `.dashboard`, `.project-card`, `border-radius`, `box-shadow`, responsive rules, readable spacing, contrast, and focus treatment.
- Parse `.vscode/launch.json` as strict JSON and verify the exact launch name, `python3 -m http.server 5500`, `${workspaceFolder}/app`, `serverReadyAction`, and `index.html` URL.
- Run `scripts/validate-exercise.sh` where appropriate; it validates repository exercise invariants and should not be weakened for the dashboard.
- Run **Run Project Pulse Dashboard** and confirm the browser shows the Project Pulse UI rather than a directory listing.
- Perform a manual responsive/accessibility pass covering keyboard focus, text alternatives to color, narrow layout, long-content wrapping, empty/error states, and readable contrast.

## Open questions

- Does the learner’s VS Code environment support the chosen browser/debug launch type directly, or should the Orchestrator adapt only the launch adapter while retaining the required command, `cwd`, and URL?
- Should the first dashboard include aggregate counts (active/risk) or remain focused on project cards?
- Is `summary` acceptable as an additional JSON field, or should the exercise later formalize it in the brief’s required schema?
- Should status and priority vocabularies be standardized now for future filtering/sorting, or remain representative fixture values?
- Should `recentActivity` remain a display string for this exercise or become structured timestamped activity data in a later iteration?
