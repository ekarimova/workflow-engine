# ai-docs: everything needed to build the workflow engine

| | |
|---|---|
| Document | `ai-docs/README.md` (the only README of the documents) |
| Version / status | 2.0, 13 November 2026. 2.0 unites the former ai-docs/README.md, README-STARTER-KIT.md, bpmn/README.md, ui-mockup/README.md and audit/README.md |
| Purpose | The index of all documents, where each one lives, the reading order, all product decisions on one page, the UI mockup, the BPMN model, the audit records and the setup. Detail and reasons stay in the documents named in each row. |
| Authority | This page summarises. If it differs from a document it points to, that document wins and this page is corrected (`architecture.md` 2). |

## 1. The product in five lines

The **workflow engine** is a web application (Java 21 / Spring Boot backend, SvelteKit frontend, PostgreSQL, one monorepo, Docker). A client writes a wish. AI planning agents research it, ask questions, and write requirements and a BPMN-like business workflow. The client edits and confirms the workflow, the AI team and the development steps (the contract). On Run, an AI team (Claude through Claude Code or opencode) builds the client's software task by task in a Docker sandbox, with 16 development steps per task. The client gets a ZIP, optionally a public GitHub repository and a "Deploy to Render" link.

Course: Hochschule Campus Wien, SDE, Advanced Software Development. Assignment #1 (= Epic 0) is due 20 November 2026. Build time: about 3 hours a day for 5 days.

## 2. Product decisions on one page

| Topic | Decision | Recorded in |
|---|---|---|
| Users | No login in the MVP. Everyone who opens the engine sees all projects and can open, change and continue any of them. Accepted risk. | ADR-020, ADR-034; `audit/deferred-risks.md` |
| AI | Claude only, through Claude Code or opencode. No local models, no other provider. | ADR-032; FR-AI-02 |
| API key | The user enters an Anthropic key on screen 1. It is tested with one call, kept in engine memory for the session and its runs, never stored, and entered again in every new session and after an engine restart. | ADR-033; FR-AI-06, FR-AI-07, QR-SEC-01 |
| GitHub (optional) | The person who starts a project signs in at the start (device flow, scope `public_repo`). Every finished task is committed and pushed to a public repository in that account, created on the first push. Without GitHub: ZIP only. The token is never stored. | ADR-035; FR-STK-08, FR-EXP-09, IR-EXT-05 |
| Render.com (optional) | The engine adds `render.yaml` to the generated repository and shows a "Deploy to Render" link. The starter deploys in their own Render account. The engine keeps no Render key and calls no Render API. | ADR-036; IR-EXT-07 |
| Where builds run | On the machine where the engine runs: one sandbox per run (runner container, Docker-in-Docker, workspace). The Claude API only answers prompts. The engine itself is not hosted on Render (no privileged Docker there). | ADR-037; `engine-architecture.md` 7 |
| The run | One business task after another. Per task the 16 development steps of `workflow.md` 5.2 (story types: 5.3) with the members of `team.md`; steps that do not apply are skipped and shown so. The team and the steps are editable before Run. | ADR-039; screens 6, 7, 11 |
| Errors | Automatic retry and rebuild; a defect gets two fix attempts, then the best model; if still not fixable the development supervisor asks the client one question with short answers. | ADR-038; screen 12 |
| Stop and time | Stop at any time exports the ZIP of the last finished task. 4 hours is a target: a warning, then the run continues and reports overtime. A run resumes up to 3 times after a restart (the key is asked for again). | ADR-027, ADR-028 |
| Result | A ZIP cleaned of agent files, a final report with metrics, the repository link and the Render link. | ADR-029; FR-EXP |
| Generated projects | Backend Java, C# or Python × frontend Angular, Svelte or Nuxt; PostgreSQL fixed; from prepared templates in `templates/`. | ADR-025, ADR-030 |
| App wording | The app never shows configuration file names ("Reset to initial", "recommended"). English and German. | Section 7 of this page; `i18n.md` |
| Security, observability | Not built in the MVP. Correctness rules stay: validation, error handling, no secrets in code, logs or Git. | ADR-020 to ADR-022 |
| Environments | Everything runs on the local machine with Docker until the presentation: local, and dev, test, prod as compose projects; stories deploy to dev, finished epics are promoted to test and prod. No remote host; a later name may be amberneurons-engine. | ADR-041 |
| Documents | Product documents in `ai-docs/`; the team's rule documents (area contracts) as Claude Code skills in `.claude/skills/<area>/`; the agent rules in `agents.md` at the root. Markdown is what agents read; the Word files in `word/` are the masters for human review. | ADR-040 (amended 13 November 2026) |

## 3. Where everything is

```text
CLAUDE.md                        project memory, loaded in every session; project facts in section 9
kickoff-prompt.md                how to start or continue work (`/next`)
todo.md                          where the work stands: current goal/epic/story/task, decisions, next, done, deployments
agents.md                        agent rules (AGT): scope, guardrails, run discipline, project inventory (section 8)
.env.example, .gitignore         placeholders only; secrets stay out of Git
.claude/
  settings.json                  permissions + global guard hook (all sessions)
  hooks/guard.mjs, selftest.mjs  the guard and its self-test (node .claude/hooks/selftest.mjs)
  agents/                        the nine role subagents (lead, architect, backend-developer, frontend-developer,
                                 tester, reviewer, devops, ux-ui-designer, auditor)
  skills/
    <area>/SKILL.md + <area>.md  the 13 rule documents as skills: architecture, workflow, team, ux-ui, frontend, api,
                                 backend, db, i18n, tests, ci-cd, infrastructure, audit (SKILL.md = when to use + key
                                 sections; <area>.md = the full document, the source of truth)
    start-story/                 /start-story US-ee.ss
    team-verification/           /team-verification US-ee.ss
    audit-report/                /audit-report AP2 E-00 (runs in the auditor subagent)
    kickoff-epic0/               /kickoff-epic0: the first plan-mode prompt for Epic 0
    agent-rules/                 the way into agents.md (root): sections to read first, how to find an AGT rule
    screen-build/                one screen → image, page, design source, software-elements section, domain, BPMN
    bpmn-model/                  the BPMN model as the contract for run, task and step states
    find-rule/                   look up a rule ID (WFL-050, BKD-MIG-009 …) in the right document
    docs-check/                  check that every file reference in the documents resolves (check-refs.mjs)
    project-learnings/           lessons learned on this repository; read before restructuring or setup work
ai-docs/
  README.md                      this page
  plan.md                        the plan: goals, epics, how stories and epics are deployed
  goals/                         one file per goal (G-01 … G-04, workflow.md 17.1)
  epics/                         one file per epic (E-00 … E-10, workflow.md 17.2) with its ordered stories
  requirements.md                requirements specification 0.3 (Markdown of the Word file)
  engine-architecture.md         arc42 architecture 0.3 (Markdown of the Word file); 6.8 = both BPMN diagrams
  software-elements.md           screen element → component → API → class → table → AI agent
  bpmn/                          BPMN 2.0 model of the engine (section 8)
  ui-mockup/                     the 14 MVP screens (section 7)
  images/                        figures of requirements.md and engine-architecture.md
  word/                          the four Word masters: concept 0.3, summary 1.2, requirements 0.3, arc42 0.3
  skeleton/backend/              V1 schema per domain (42 tables, tested on PostgreSQL 16), domain records, enums,
                                 AI output records (Java 21), seeds (default AI team, 16-step development workflow)
  skeleton/frontend/             TypeScript models per domain (tsc --strict)
  stories/                       story cards (template _template.md, workflow.md 4.1) with DoR, checkpoint, verdicts, evidence
  tasks/                         task specs written by the lead (workflow.md 17.4)
  audit/                         audit records (section 9)
```

Bare document names in the documents (`backend.md` 4.2, `workflow.md` WFL-050) mean the file of that name above: `requirements.md`, `engine-architecture.md`, `software-elements.md` in `ai-docs/`; `agents.md` at the root; every other area document in `.claude/skills/<area>/<area>.md`. The skill `find-rule` resolves a rule ID to its file.

The skeleton is the starting point of the code, not the code itself: Epic 0 keeps it and builds the monorepo layout of `engine-architecture.md` 5.1 (`backend/`, `frontend/`, `api/`, `ci/` …) around it.

## 4. Reading order

| Step | Read | Why |
|---|---|---|
| 1 | `../CLAUDE.md` | The rules every session keeps; project facts in section 9 |
| 2 | This page, then `../todo.md` and `plan.md` | The product, its decisions, where everything is, where the work stands |
| 3 | `architecture.md` sections 2, 4.1, 5, 6 | Authority, the two BPMN diagrams, the decision register (ADR-001 to ADR-041), the invariants |
| 4 | `workflow.md` 4, 5, 8, 9 and `team.md` | How a story runs (16 stages), who does what |
| 5 | `../agents.md` | Agent rules, permissions, the project inventory (section 8) |
| 6 | For the story at hand: `requirements.md` (the IDs), `software-elements.md` (screen → component → API → class → table → AI agent), the screen in `ui-mockup/` (section 7), the BPMN model (section 8) | What to build |
| 7 | The area document of the work: `backend.md`, `db.md`, `api.md`, `frontend.md`, `ux-ui.md`, `i18n.md`, `tests.md`, `ci-cd.md`, `infrastructure.md` (all in `.claude/skills/<area>/`) | How to build it |

## 5. Build order

The full plan with goals, epics, stories and tasks is `plan.md` (`goals/`, `epics/`, `stories/`, `tasks/`); the current position is `../todo.md`.

| Order | What | Detail |
|---|---|---|
| 0 | Epic 0, technical setup (due 20 November 2026): repository, toolchain, database and first migration, backend and frontend skeleton, compose, local pipeline; spike for the agent harness | `infrastructure.md` 17; skill `/kickoff-epic0` |
| 1 | `projects`: all projects, wish, files, session key, GitHub sign-in | Screens 0, 1; `software-elements.md` 3.0, 3.1 |
| 2 | `questions`: research, questions, technology choice | Screen 2 |
| 3 | `documents`: requirements, project document | Screen 3 |
| 4 | `workflows`: BPMN-like workflow, edit a task, contract | Screens 4, 5, 8 |
| alongside | `settings`: Claude models per tier, editable AI team and development workflow | Screens 6, 7 |
| 5 | `execution`: sandbox, the run of `bpmn/build-one-task`, console, questions to the client, GitHub push, ZIP, report | Screens 9 to 13; section 8 |

Each slice follows the chain of `software-elements.md` 1: screen element → Svelte component → API operation → backend class → table, and names the AI agent that writes the data.

## 6. Open points before or during the build

- Result of the harness spike US-00.S1: Claude Code, opencode or both as default (OI-4).
- Default cost limit and ceiling values (OI-5).
- `GITHUB_OAUTH_CLIENT_ID` of the engine's GitHub OAuth app (device flow); without it the GitHub card is hidden.
- Exact Claude model ids in `settings.provider_models` (placeholders in the seed).
- `requirements.md` does not yet list every feature of `software-elements.md` 0.2 (story types, error types, evidence, audit report, metrics, lessons, edit conflicts, dialogs); add the requirement before the story starts.
- A reference run against the 4-hour target with 16 steps per task (ADR-039 reopen trigger).
- The guard hook's path rules still name the old paths of the area documents; a human runs the script named in the skill `project-learnings` (agents may not edit the guard, AGT-081).
- All open decisions and their state: `../todo.md`.

## 7. UI mockup of the MVP (`ui-mockup/`)

The reference for the screens: what each page shows, in which order, and how the pages link. Use it together with `software-elements.md`, which maps every element on these screens to its Svelte component, API operation, backend class, table and AI agent.

| Folder | Content | For |
|---|---|---|
| `ui-mockup/images/` | One full-page PNG per screen (1280 px wide at 2× resolution) | Humans and AI agents: look at a screen before building it |
| `ui-mockup/pages/` + `ui-mockup/index.html` | Static HTML copies; open `index.html` in a browser. Links between screens work; buttons and fields do nothing | Clicking through the flow |
| `ui-mockup/source/` | The design files of the canvas: one `.dc.html` per screen and `canvas.json` (layout and titles) | Editing the mockup later on the design canvas; exact text, colours and spacing |

The live, editable canvas is the Claude design artifact "Workflow Engine – MVP screens" (owner's account). If the canvas and these files differ, the canvas is newer; export again. The image, page and source of a screen share its number: `images/NN-name.png`, `pages/NN-name.html`.

| Screen | Name (image and page) | Source | Content |
|---|---|---|---|
| 0 All projects | `00-all-projects` | `Projects.dc.html` | Start page without login: all projects with status, GitHub account of the starter and last change; open or continue any project; new project. |
| 1 Wish and AI | `01-wish-and-ai` | `Main.dc.html` | Project name, language of the documents, wish, links, files; agent (Claude Code or opencode), Claude model, Anthropic API key for this session with Test; optional GitHub sign-in of the project starter. |
| 2 Questions and technology | `02-questions-and-technology` | `Questions.dc.html` | AI questions (one choice, several, text, feature) with progress; technology choice; PostgreSQL fixed. |
| 3 Requirements | `03-requirements` | `Requirements.dc.html` | Requirements grouped by epic with ID, priority and source; downloads of the requirements and the project plan. |
| 4 Workflow (BPMN-like) | `04-workflow-bpmn-like` | `Workflow.dc.html` | BPMN-like workflow with lanes, start and end events, tasks and arrows; every task is clickable. |
| 5 Edit a task | `05-edit-a-task` | `StepEdit.dc.html` | Edit a task: name, what happens, lane, type, story type, criteria, AI test scenarios, links; add before or after; delete. |
| 6 AI team (editable) | `06-ai-team-editable` | `Team.dc.html` | AI team: team size (Starter, Standard, Full, Custom), model per member, on or off, edit instructions and write areas. |
| 7 Development workflow (editable) | `07-development-workflow-editable` | `DevWorkflow.dc.html` | Development workflow: 16 steps per task, reorder, edit step; rules (fix loops, best-model try, recovery by error type, CI stages); lessons. |
| 8 Ready to run (with GitHub) | `08-ready-to-run-with-github` | `Ready.dc.html` | Summary before the run; cost limit; time rules; public GitHub repository of the starter with push after every finished task; optional Deploy to Render link; confirm and Run. |
| 9 Workflow in process | `09-workflow-in-process` | `Running.dc.html` | The run: time, tasks, cost, GitHub, restarts; the workflow with task states; latest activity; Stop and ZIP. |
| 10 Run details | `10-run-details` | `RunDetails.dc.html` | Epics and stories with type and GitHub commit; agent team; decisions; time and cost; audit report; metrics. |
| 11 Task details during the run | `11-task-details-during-the-run` | `StepRun.dc.html` | One task during the run: business task, 16 development steps with members, CI by test level, errors with type, evidence, console. |
| 12 Human action required | `12-human-action-required` | `StepHelp.dc.html` | Human action required: question with expected, what happened, requirement, what was tried and cause; answer options. |
| 13 Dialogs and states | `13-dialogs-and-states` | `States.dc.html` | Confirm dialogs (stop, delete, reset), empty, loading and error states, edit conflict from another tab. |

The four figures `images/mockup-1-wish.png` … `mockup-4-execution.png` are the earlier four-page sketches used as figures in `requirements.md` 8.1; the 14 screens above replace them for building.

**Flow**

```text
1 Wish and AI → 2 Questions and technology → 3 Requirements → 4 Workflow (→ 5 Edit a task)
→ 6 AI team → 7 Development workflow → 8 Ready to run (GitHub) → Run
→ 9 Workflow in process → 10 Run details / 11 Task details (→ 12 Human action required)
Pages 6 and 7 are also opened from 10 and 11 during a run (read the running contract; changes apply to the next run).
13 shows the shared dialogs and page states used on several pages.
```

**Rules for building from these screens**

- The screens are a reference for content, order and states, not a pixel specification. The design system and the rules of `ux-ui.md` and `frontend.md` decide the final look.
- Every text on a screen comes from the translation resource (`i18n.md`); the header has a language switch.
- The app never shows file names of the engine's own setup; it says "Reset to initial" and "recommended".
- The example data (canteen pre-order, "Pay", times, costs, commits) is invented demo content, not test data.
- Each screen element is built in the story of its domain (see `software-elements.md` 3). Check the matching image before the story starts and attach it to the story card.

The BPMN 2.0 diagrams of the engine itself are on the same design canvas (`ui-mockup/source/canvas.json` lists them) and in `bpmn/` (section 8).

## 8. BPMN 2.0 model of the engine (`bpmn/`)

How the workflow engine itself runs one client project, end to end, and how its AI team builds one task. Version 1.0, final for the MVP. Related: `requirements.md`, `engine-architecture.md` 6.8, `architecture.md` 4.1 (ADR-032 to ADR-039), `workflow.md` (the 16 development steps), `team.md`.

| File | Content |
|---|---|
| `bpmn/workflow-engine-lifecycle.bpmn`, `bpmn/workflow-engine-lifecycle.png` | Level 1: collaboration "Run a client project": client, engine (3 lanes), Claude API, GitHub and Render.com of the project starter |
| `bpmn/build-one-task.bpmn`, `bpmn/build-one-task.png` | Level 2: called process "Build one task", run once per business task (sequential multi-instance) |

The `.bpmn` files are BPMN 2.0 XML with diagram interchange (positions of every shape and every line). They open in any BPMN 2.0 tool, for example bpmn.io / Camunda Modeler. Both diagrams are descriptive (`isExecutable="false"`): the engine is a Spring Boot application, not a BPMN engine. The diagrams are the contract for its states and transitions. The decisions they show are those of section 2 (users, AI, GitHub, Render, where builds run, stop at any time with the ZIP of the last checkpoint, 4-hour target with a non-interrupting timer).

### Level 1: Run a client project

![Workflow engine – project lifecycle](bpmn/workflow-engine-lifecycle.png)

| Element | Meaning | Screen |
|---|---|---|
| Receive wish | Wish, choice of Claude Code or opencode, API key, optional GitHub sign-in | 1 Wish and AI |
| Check API key / Key valid? | Test call to Claude; on failure the client gets "Key error" and nothing is stored | 1, 13 |
| Create project | Project row in the engine database | 1 |
| Prepare questions (planning agents) | Brief and questions with technology choice | 2 |
| Receive answers | Answers and stack choice | 2 |
| Plan requirements and workflow | Requirements, BPMN-like business workflow, tasks | 3, 4, 5 |
| Receive confirmed contract | Workflow, AI team and development steps confirmed | 6, 7, 8 |
| Freeze contract | Version of the contract that the run uses | 8 |
| Prepare build space | Sandbox on the engine machine | 9 |
| Build each task | Calls level 2 once per business task, one after another | 9, 10, 11, 12 |
| Run failed (error) | A task ended with "Run failed" (client chose to stop when asked); the report is still produced | 10, 13 |
| 4 h target (timer) | Overtime warning, the run continues | 9 |
| Assemble final report and metrics / Export clean ZIP | Report, metrics, ZIP, links to the repository and the Render link | 9, 10 |
| Stop at any time | Event subprocess: ZIP of the last checkpoint | 9, 13 |

### Level 2: Build one task

![Build one task](bpmn/build-one-task.png)

The steps are the 16 steps of `workflow.md` section 5.2 (story types in 5.3) and the members of `team.md`:

| BPMN activity | Development steps | Members |
|---|---|---|
| Plan the task | 1 Plan the task | Development supervisor |
| Define and plan | 2 Define and plan | PO, Architect, Lead, Tester |
| Design, contract and data (collapsed) | 3 Design, 4 Contract, 5 Data, each only if needed | UX/UI, Architect, Backend |
| Build the change | 6 Build | Backend, Frontend |
| Write tests | 8 Test | Tester |
| Run continuous integration | 7 Environment (if needed), 9 Continuous integration | DevOps |
| Verify as a team, review and audit | 10 Team verification, 11 Review and audit | all active members, then Reviewer and Auditor |
| Fix and verify | 12 Fix and verify (only on a finding) | the member who owns the failing part |
| Ask the client | Human action required: question with short answer options | Development supervisor |
| Accept | 13 Accept | PO |
| Document | 14 Document | Backend, Frontend |
| Merge and push | 15 Merge and push: commit to the starter's public repository | Lead |
| Sum up the task | 16 Sum up | Development supervisor |

Every AI step sends its prompt to Claude through Claude Code or opencode with the key of this session (message flows "Prompts" and "Replies" at the pool border, the same as on level 1).

### Modelling rules used

The model follows the BPMN 2.0.2 specification (OMG), Bruce Silver's Method and Style rules and the Seven Process Modeling Guidelines (7PMG) of Mendling, Reijers and van der Aalst:

- External parties are black-box pools; message flows carry nouns ("Questions", "Task commit") and are repeated on both levels.
- Activities are verb–object; no more than about ten activities per level; detail goes to a called process (hierarchy instead of one large diagram, 7PMG G7).
- One none start per process; every end state is named when there is more than one.
- Exclusive gateways only (no OR, 7PMG G5); a splitting gateway asks a question and its gates are labelled; merges are not labelled.
- Structured loops: CI red and "Approved? No" both return to one merge before "Fix and verify".
- Errors are interrupting boundary events; the time target is a non-interrupting timer (dashed).
- No crossing lines; every node lies on a path from a start to an end.

Checks that were run on both files (script in the starter kit history, not part of the product): XML parses, every reference resolves, every element has diagram interchange, incoming/outgoing lists match the sequence flows, every node is in exactly one lane, every node reachable from a start and able to reach an end, no line passes through a shape, no lines cross, no label overlaps a shape, line or other label. Then three rounds of rendering and visual inspection.

Sources: OMG, BPMN 2.0.2, formal/13-12-09; B. Silver, BPMN Method and Style, 2nd ed., Cody-Cassidy Press; J. Mendling, H. A. Reijers, W. M. P. van der Aalst, Seven Process Modeling Guidelines (7PMG), Information and Software Technology 52 (2010); M. Dumas, M. La Rosa, J. Mendling, H. A. Reijers, Fundamentals of Business Process Management, 2nd ed., Springer 2018; W. J. Hopp, M. L. Spearman, Factory Physics (Little's law, work-in-progress limits: one task at a time).

## 9. Audit records (`audit/`)

Written by the Auditor only (`audit.md` 11, AUD-090). Method: `audit.md` 6. Templates: `audit.md` 12.

| File | Content |
|---|---|
| `audit/findings.md` | Register of all findings and their state (12.4) |
| `audit/patterns.md` | Repeated causes, proposals, results |
| `audit/deferred-risks.md` | The deferred-risk register (12.5) |
| `audit/reports/AR-<point>-<scope>-<date>.md` | Audit reports |

Register of simplifications of this project (AUD-020): none yet.

## 10. Setup and first session

**Check the setup.** `CLAUDE.md` and `.claude/` must be at the project root, otherwise Claude Code does not find them (use the terminal; Finder hides dot folders).

```bash
ls -la                              # CLAUDE.md, agents.md, .claude, ai-docs, .gitignore, .env.example
node .claude/hooks/selftest.mjs     # must end with "All N cases passed."
node .claude/skills/docs-check/check-refs.mjs   # every document reference resolves
```

**API key with a spending limit (for testing the engine).** The engine stores no user keys: each user enters an Anthropic key on screen 1 for their session only (ADR-033). The key in `.env` is only for the engine's own automated tests; use a separate key with a hard cap, not your personal default key.

1. Sign in at **platform.claude.com** (Claude Console). This is separate from claude.ai; API usage is paid with Console credits (Billing), not with a Pro or Max subscription.
2. Organization settings → **Billing** → buy a small amount of credit and set an organization **spend limit**.
3. **Workspaces** → create a workspace, for example `workflow-engine-dev` (the Default workspace cannot have its own limit).
4. In that workspace: **Spend limits** → *Change limit* → for example 20 USD per month (lower than the organization limit).
5. In that workspace: **API Keys** → *Create Key* → name `engine-local`. Copy it once.
6. `cp .env.example .env` and paste the key after `ANTHROPIC_API_KEY=`. `.env` is git-ignored and the guard blocks agents from reading or writing it.

Do **not** put the key into `~/.zshrc` or export it in the terminal where you start Claude Code: if `ANTHROPIC_API_KEY` is set there, Claude Code bills that key instead of your subscription.

**GitHub CLI (`gh`).**

```bash
brew install gh
gh auth login          # GitHub.com → HTTPS → Login with a web browser
gh auth status
gh repo create workflow-engine --private --source=. --remote=origin --push
```

Then protect `main` on GitHub (Settings → Branches → require a pull request). Claude Code uses `gh pr create` and `gh pr view`; pushing and merging always ask you first (`.claude/settings.json`).

**First session.**

1. `node .claude/hooks/selftest.mjs` (all cases pass).
2. `claude` in the project root. `/agents` shows the nine roles, `/hooks` the guard, `/skills` the skills.
3. Switch to plan mode (Shift+Tab) and run `/kickoff-epic0`. Approve or change the plan.
4. Story by story: `/start-story US-00.01` → the subagents build → `/team-verification US-00.01` → you review the diff and create the pull request.
5. At the end of Epic 0: `/audit-report AP2 E-00`.

| Piece | What it does | Note |
|---|---|---|
| Main session | You and Claude Code: start skills and subagents, approve plans | Subagents cannot start other subagents, so the main session orchestrates; the `lead` subagent plans and writes task specs |
| Subagents | One file per role; tools and model from `agents.md` 13 and TEAM-051 (`opus` for lead, architect, auditor; `sonnet` for the others) | Each has a path guard hook in its frontmatter and preloads the skills of its area |
| Area skills | The 13 rule documents; Claude loads the short `SKILL.md` when the work matches and opens the full document by section or rule ID | The document in the skill folder is the source of truth, not `SKILL.md` |
| Global guard | Every session: no secrets, no newly disabled or skipped tests (JUnit disable annotations, `it.skip`, Gradle `ignoreFailures`), no force push or push to `main`, no `reset --hard`, no volume removal, no `curl \| sh`, no reading `.env`, no edits of the guard itself | A blocked call returns the reason to Claude; it is logged in `.claude/hooks/blocked.log` |
| Path guard | Per role, the write areas of `agents.md` 11; shell writes are checked best-effort (redirections, `rm`, `mv`, `cp`, `sed -i`) | The Reviewer still rejects out-of-scope files (AGT-070) |
| Workflow skills | `/start-story`, `/team-verification`, `/audit-report`, `/kickoff-epic0` are started by you only (`disable-model-invocation`) | `/audit-report` runs in the auditor's own context for independence |

If a guardrail blocks something legitimate, change the rule in `.claude/hooks/guard.mjs` yourself, add a case to `selftest.mjs`, and run the self-test. Agents are not allowed to change the guard.

**Known limits.** The hooks use Node; no `jq` or Python needed. Claude Code's tool names and hook details change between versions: if `/hooks` does not show the guard, or a case behaves differently in practice than in the self-test, check the current hooks documentation and adjust `settings.json`. The path guard for shell commands is a heuristic. No key, token or personal data belongs in this repository; the guard catches common key formats, not all of them.

## 11. Change history of the documents

- **13 November 2026, plan.** `plan.md`, `goals/`, `epics/`, the 14 story cards and 24 task specs of E-00; `kickoff-prompt.md` (replaces the content of the skill `kickoff-epic0`) and `todo.md` at the root.
- **13 November 2026, structure.** One README (this page) replaces five. The engine documents moved from ai-docs/engine-docs/ to `ai-docs/`; the code skeleton to `ai-docs/skeleton/`; the 13 area documents to `.claude/skills/<area>/` with a `SKILL.md` each; the duplicate copies of `README.md` and `agents.md` were removed (`agents.md` stays at the root); kickoff-prompt.md became the skill `/kickoff-epic0`. Every path reference was updated (ADR-040 amended, AGT-004, AGT-042, `agents.md` 8). New skills: `agent-rules`, `screen-build`, `bpmn-model`, `find-rule`, `docs-check`, `project-learnings`, `kickoff-epic0`; each subagent preloads the skills of its area (`skills:` in `.claude/agents/*.md`).
- **13 November 2026, second round of the starter kit.** The documents folder is `ai-docs/` (every `docs/` path in the documents, hooks, skills and agents was changed; ADR-040). `architecture.md` 0.3: ADR-032 to ADR-040 and section 4.1 with both BPMN diagrams; ADR-024 replaced by ADR-039, ADR-023 partly by ADR-038. Broken references fixed: `db.md` (WFL-288 → ARC-060, WFL-291 → WFL-080), `architecture.md` (WFL-291 removed, CIC-265 → CIC-011, CIC-040), `agents.md` (WFL-290 → AGT-042). `agents.md` section 8: three rows added (documents folder, product references, product decisions an agent must not break).
- **Starter kit, first round.** `architecture.md` 0.2: ADR-023 to ADR-031 (engine profile; apply only to the engine's unattended runs and templates); the document map and plan list `requirements.md`, `engine-architecture.md`, `audit.md` and `CLAUDE.md` as existing. `audit.md`: AUD-002 cited the non-existent WFL-283; now WFL-117. `team.md`, `workflow.md`: the former separate decisions file → the decision register in `architecture.md` 5; the former process file → `workflow.md` and `CLAUDE.md`; stale "not yet written" notes removed (also in `ci-cd.md`, `db.md`, `agents.md`). `agents.md` section 8 (project inventory) filled; `CLAUDE.md` sections 5 and 9 filled. Defaults chosen: Gradle with Kotlin DSL, local only for G-01 until WFL-173 is decided.
