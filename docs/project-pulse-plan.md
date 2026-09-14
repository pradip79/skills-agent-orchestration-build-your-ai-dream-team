# Project Pulse implementation plan

## Goal and repository context

Build Mona's lightweight Project Pulse dashboard as a small static app for
contributors. The first view must make active projects easy to scan by showing
project names, owners, current status, recent activity, priority or risk, and
short contributor-friendly context. The result must be a polished, accessible,
responsive card-based frontend rather than a plain page or a server directory
listing.

This repository is an instructional GitHub Copilot CLI exercise. The
Orchestrator coordinates the work, the Planner defines the implementation
sequence, the Designer owns experience decisions, and the Coder implements the
assigned artifacts. Existing agent definitions in `.github/agents/`, the brief
in `.github/project-pulse-brief.md`, and the validation workflow are the
authoritative repository context.

## Responsibilities

### Planner

- Translate the Project Pulse brief into an implementable sequence.
- Define file ownership, interfaces between files, dependencies, risks, and
  acceptance criteria before implementation begins.
- Identify which tasks are safe to run in parallel and which require a
  handoff.
- Specify validation for source structure, JSON correctness, launch behavior,
  and the integrated browser experience.
- Keep the plan aligned with the existing static-app and Copilot CLI exercise
  conventions; do not implement application source files.

### Designer

- Define the information hierarchy for the dashboard header, summary context,
  project collection, cards, metadata, status badges, and priority treatment.
- Specify a polished visual system with readable typography, spacing, contrast,
  rounded cards, shadows, clear status/priority affordances, and a responsive
  layout.
- Recommend semantic HTML, accessible labels, heading order, keyboard-safe
  interactions, and non-color-only status cues.
- Own design decisions and styling guidance in `app/styles.css`; coordinate
  markup hooks with the Coder without editing the Coder's assigned files unless
  the Orchestrator explicitly changes ownership.

### Coder

- Implement the static dashboard in the assigned files using clear,
  deterministic, dependency-free browser code.
- Build `app/index.html` with the exact title `Project Pulse`, a reference to
  `styles.css`, a reference to `project-data.json`, and visible project cards
  using the `project-card` class.
- Render each project's `name`, `owner`, `status`, `recentActivity`, and
  `priority` from the JSON data; do not duplicate the data as the only source
  in the markup.
- Implement the Designer's layout and accessibility decisions in
  `app/styles.css`, including `.dashboard`, `.project-card`,
  `border-radius`, `box-shadow`, readable spacing, and responsive behavior.
- Create `.vscode/launch.json` as strict JSON with no comments. Add the
  `Run Project Pulse Dashboard` configuration, serve from the `app` directory
  with `python3 -m http.server 5500`, and configure `serverReadyAction` to open
  `http://localhost:%s/index.html`.
- Surface loading or data errors clearly in the UI rather than silently
  presenting an empty successful-looking dashboard.
- Validate the implementation and report any remaining limitations to the
  Orchestrator. Do not stage, commit, or push.

### Orchestrator

- Read this plan and delegate each scoped task to the appropriate specialist.
- Give Designer and Coder explicit file assignments and preserve ownership
  boundaries.
- Collect the Designer's visual and accessibility decisions before the Coder
  finalizes shared HTML/CSS hooks.
- Coordinate integration of the HTML, stylesheet, JSON data, and launch
  configuration; resolve mismatches rather than hiding them.
- Run or delegate the validation listed below, including the repository
  workflow validation and a real browser smoke test.
- Report participating agents, completed files, validation results, and known
  limitations in the final handoff. The learner controls all git operations.

## File assignments and interfaces

| File | Owner | Required implementation and interface |
| --- | --- | --- |
| `app/index.html` | Coder, informed by Designer | Accessible Project Pulse document with exact title text, linked stylesheet and JSON data, dashboard container, and visible `.project-card` elements. The page must expose status, recent activity, and priority for each project. |
| `app/styles.css` | Designer defines; Coder implements | Complete polished presentation for `.dashboard`, `.project-card`, badges, metadata, priority/risk treatment, focus states, spacing, contrast, and responsive breakpoints. CSS hooks must match the HTML. |
| `app/project-data.json` | Coder, using Planner's schema and Designer's display needs | Valid JSON with a top-level `projects` array. Every project object includes `name`, `owner`, `status`, `recentActivity`, and `priority`; values are suitable for rendering multiple realistic project cards. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named `Run Project Pulse Dashboard`; uses `python3 -m http.server 5500`, serves with `cwd` set to `${workspaceFolder}/app`, and opens `http://localhost:%s/index.html` so the app opens instead of a directory listing. |

The JSON field names are the data contract between `project-data.json` and
`index.html`. The class names and semantic elements agreed by Designer and
Coder are the presentation contract between `index.html` and `styles.css`.
The launch configuration depends on all three app files being present under
`app/`.

## Ordered implementation phases

### Phase 1: Confirm requirements and contracts

1. Orchestrator reads `.github/project-pulse-brief.md`, the four custom agent
   definitions, and this plan.
2. Planner confirms the scope above, especially the JSON schema, required
   selectors, launch behavior, and validation gates.
3. Orchestrator assigns Designer and Coder only the files listed in the table;
   no application source files are changed during planning.

### Phase 2: Produce the design direction

1. Designer defines the information hierarchy, card anatomy, status and
   priority treatment, responsive layout, contrast, and accessibility rules.
2. Designer publishes the required CSS hooks and any markup assumptions to the
   Orchestrator and Coder.
3. This phase has no dependency on the final project values, but its output is
   a dependency for the Coder's final HTML/CSS integration.

### Phase 3: Implement the static app and preview configuration

1. Coder creates the JSON data contract and representative project records.
2. Coder implements the HTML structure and rendering behavior against that
   contract.
3. Coder implements the stylesheet against the agreed markup hooks.
4. Coder creates and validates the VS Code launch configuration.
5. The Orchestrator checks that the four assigned files work together and
   resolves any contract or ownership issue.

### Phase 4: Validate and hand off

1. Run the repository's `bash scripts/validate-exercise.sh` workflow
   validation command from the repository root.
2. Run focused JSON checks for `app/project-data.json` and
   `.vscode/launch.json`, confirming the top-level `projects` array and all
   required per-project fields.
3. Perform the static-server/browser smoke test described below.
4. Orchestrator records the result, any limitations, and the contributions of
   Planner, Designer, Coder, and Orchestrator for the final handoff.

## Dependencies

- The repository brief and agent definitions must be read before delegation.
- Designer's markup and visual decisions must be available before Coder
  finalizes the shared HTML/CSS selectors.
- `app/project-data.json` must define the agreed schema before HTML rendering
  can be considered complete.
- `app/index.html` depends on both `styles.css` and `project-data.json`; the
  stylesheet depends on the HTML hooks; `launch.json` depends on the `app/`
  directory and its entry point.
- Integrated browser validation must wait until all four assigned files exist.
- `bash scripts/validate-exercise.sh` validates repository exercise
  conventions and JSON/configuration expectations, but it does not replace
  the browser smoke test.

## Explicit parallel-work decisions

### Work that may run in parallel

- During Phase 2, Designer can work on the visual/accessibility direction
  while the Coder prepares the independent project records in
  `app/project-data.json`, because the data schema and design direction do not
  require the same file.
- After the Planner has fixed the contracts, Coder can prepare launch
  configuration details while Designer finalizes styling guidance, provided
  the Coder does not finalize conflicting HTML/CSS hooks.

### Work that must remain sequential

- Planner requirements and file ownership precede Designer/Coder delegation.
- Designer's shared markup and CSS-hook decisions precede final HTML/CSS
  integration.
- JSON schema agreement precedes data-driven rendering validation.
- All implementation must finish before integrated validation or the browser
  smoke test.
- Any fix that changes a shared selector, data field, or launch command must be
  followed by the relevant focused checks and then the integrated smoke test.

The Orchestrator should not parallelize tasks that edit the same file. In
particular, Designer and Coder must not concurrently edit `index.html` or
`styles.css`; if a design change requires an implementation edit, it is a
sequential handoff to Coder.

## Validation expectations

### Repository workflow validation

From the repository root, run:

```bash
bash scripts/validate-exercise.sh
```

The command must complete successfully. It confirms repository workflow
conventions and checks the expected learner-file paths and launch-related
requirements without requiring the app to be committed.

### JSON checks

Run strict JSON parsing for both generated JSON files:

```bash
python3 -m json.tool app/project-data.json >/dev/null
python3 -m json.tool .vscode/launch.json >/dev/null
```

Also inspect the parsed data to confirm that `projects` is an array and that
every project has non-empty `name`, `owner`, `status`, `recentActivity`, and
`priority` fields. Confirm that `launch.json` contains one configuration named
`Run Project Pulse Dashboard`, uses `python3 -m http.server 5500`, serves from
`${workspaceFolder}/app`, and opens `http://localhost:%s/index.html`.

### Static-server/browser smoke test

Start the same server used by the launch configuration from the app directory:

```bash
cd app
python3 -m http.server 5500
```

Open `http://localhost:5500/index.html` in a browser. Confirm that the
Project Pulse dashboard, not a directory listing, appears; the title and
header are visible; multiple project cards render; each card visibly includes
owner, status, recent activity, and priority; the layout remains readable at a
 narrow viewport; and browser developer tools show no failed request for
`styles.css` or `project-data.json`. Stop the server after the smoke test.

### Acceptance criteria

- The four assigned files exist at the exact repository paths.
- The page is data-driven, accessible, polished, responsive, and clearly
  recognizable as Project Pulse.
- Required selectors, fields, launch name, server command, working directory,
  and `index.html` URL are present and connected.
- Repository validation, JSON parsing, and the browser smoke test all pass.
- No unrelated application or exercise infrastructure files are modified.

## Risks and mitigations

- **Directory listing opens instead of the app:** set the launch working
  directory to `app` and include `/index.html` in `serverReadyAction`.
- **Cards render without data:** keep the exact JSON field contract and show an
  explicit data-load error instead of silently rendering an empty state.
- **Status or priority is unclear by color alone:** use text labels, semantic
  structure, and sufficient contrast in addition to color.
- **Parallel edits conflict:** keep Designer's output advisory and reserve
  final shared-file edits for the Coder.
- **Validation gives false confidence:** use the repository script and JSON
  checks for structure, then perform the real browser smoke test for runtime
  integration.

## Open questions and assumptions

- No framework, package manager, build step, or external API is required; the
  dashboard is intentionally a static app served by Python's standard HTTP
  server.
- The brief does not prescribe exact project names or a fixed number of
  records, so Coder may choose representative contributor-friendly examples
  as long as every required field is present and multiple cards are visible.
- The browser smoke test assumes a local browser or Codespaces port preview is
  available. If a graphical browser is unavailable, the Orchestrator should
  still perform the HTTP request and document that limitation rather than
  claiming a visual smoke test passed.
