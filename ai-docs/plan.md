# The plan: goals, epics, stories and tasks of the workflow engine

The plan of the work, in the hierarchy of `workflow.md` 2: **goal** (`G-nn`) → **epic** (`E-nn`) → **user story** (`US-ee.ss`, the unit that is built, merged and deployed) → **task** (`T-ee.ss.n`, the work of one agent on one story). Where the work stands now: `../todo.md`. How to start or resume: `../kickoff-prompt.md`.

| Folder | One file per | Template | Written by |
|---|---|---|---|
| `ai-docs/goals/` | goal | `workflow.md` 17.1 | Product Owner (with the Lead) |
| `ai-docs/epics/` | epic | `workflow.md` 17.2 | Lead (scope by the Product Owner) |
| `ai-docs/stories/` | story card | `ai-docs/stories/_template.md` (`workflow.md` 4.1) | Lead, with `/start-story` |
| `ai-docs/tasks/` | task spec | `workflow.md` 17.4 | Lead; `completion_notes` by the agent |

Detail is refined just in time (WFL-005): the goals and all epics are written; the stories of the active epic are precise cards; stories of later epics are one line in their epic file and get a card when their epic becomes active.

## The plan at a glance

| Goal | Epic | Content | Status |
|---|---|---|---|
| [G-01](goals/G-01.md) Technical setup | [E-00](epics/E-00.md) | Repository, toolchain, skeleton page, AI check, local pipeline, local dev, test and prod, spike US-00.S1 | Planned |
| [G-02](goals/G-02.md) MVP business requirements | [E-01](epics/E-01.md) | `projects`: all projects, wish, files, session key, GitHub sign-in (screens 0, 1) | Proposed |
| | [E-02](epics/E-02.md) | `questions`: research, questions, technology choice (screen 2) | Proposed |
| | [E-03](epics/E-03.md) | `documents`: requirements and project document (screen 3) | Proposed |
| | [E-04](epics/E-04.md) | `workflows`: BPMN-like workflow, edit a task, contract (screens 4, 5, 8) | Proposed |
| | [E-05](epics/E-05.md) | `settings`: models per tier, editable AI team and development workflow (screens 6, 7) | Proposed |
| | [E-06](epics/E-06.md) | `execution`: sandbox, the run of `build-one-task`, stop, resume, push, ZIP, report (screens 9 to 13) | Proposed |
| | [E-07](epics/E-07.md) | Case epic "From wish to result": the cross-domain journey end to end | Proposed |
| [G-03](goals/G-03.md) Audit and testing | [E-08](epics/E-08.md) | Integration, audit and verification | Proposed |
| [G-04](goals/G-04.md) CI/CD and infrastructure | [E-09](epics/E-09.md) | Completion of the delivery path on the local machine (test and prod already exist, ADR-041); a remote host only if wanted | Proposed |
| | [E-10](epics/E-10.md) | Release of the MVP through dev, test, prod | Proposed |

States: goal and epic `Proposed → Planned → Active → Gate → Closed` (`workflow.md` 11); story `Draft → Ready → In progress → Verification → Done`; task `open → in progress → done | blocked` (17.4).

## Where each piece is built and deployed (as the documents define it)

| Unit | Built and checked | Deployed to | Rule |
|---|---|---|---|
| Task (`T-…`) | In the story branch by its agent; `ci/run` steps of its area | Not deployed alone: a task is part of a story | WFL-136, AGT-031 |
| Story (`US-…`) | `ci/run all-local` (local containers: build, check, images, `env-up layers`, migrate, seed, tests, smoke, report) | **local** containers, then after the merge **dev** on the local machine (`deploy dev`, `verify dev`, pointer `env/dev`) | WFL-130, INF-BRN-013, CIC 4.1 |
| Epic (`E-…`) | Epic gate (`workflow.md` 11) on the integrated state | **test** (`promote dev test`, `verify test`), then **prod** after the Product Owner's approval (`promote test prod`, `verify prod`) | WFL-131, WFL-133, INF-ENV-005, CIC-140, CIC-142 |

All environments run on the local machine with Docker until the presentation (ADR-041): `dev`, `test` and `prod` are separate compose projects with their own volume, env file and ports, created in Epic 0 (US-00.10, US-00.14). The deployment commands run on the local machine. A remote host is not planned; if one is wanted later (for example `amberneurons-engine`), its first deployment is the first story of G-04.

## Target for 20 November 2026: from the wish to the reviewed workflow (PO decisions D-11, D-13)

By 20 November the engine shows the first part of the BPMN model (level 1, `bpmn/workflow-engine-lifecycle.png`) working end to end with Claude: **Receive wish → Check API key → Create project → Prepare questions → Receive answers → Plan requirements and workflow → Ready to run (review) → Freeze contract**. Not every function of these screens is there yet: each story below is the thinnest version that makes the step work. AI is available from the first build (US-00.15) and used in every step. Execution (Prepare build space, Build each task) is **prepared, not built**: its tables and records exist from the skeleton (US-00.03), the Run button on Ready to run is shown but disabled ("execution follows"), and E-06 starts after 20 November.

| # | Story | BPMN level 1 | Screen | Thin version for 20 Nov |
|---|---|---|---|---|
| E-00 | US-00.01–06, 15, 08, 10 (09 in parallel) | — | skeleton page | Repository, toolchain, database, backend, frontend, AI check with own key, local pipeline, dev |
| 1 | US-01.01 | Receive wish, Create project | 1 | Name, document language, wish; app shell en/de |
| 2 | US-01.05 | Check API key, Key valid? | 1 | Session key on screen 1 (reuses US-00.15) |
| 3 | US-01.07 | Prepare questions (brief) | 1 → 2 | Project brief by Claude |
| 4 | US-02.01 | Prepare questions | 2 | Questions by Claude, validated, one retry |
| 5 | US-02.02 | Receive answers | 2 | Answer and save; the stack is the default template (Java × Svelte) shown read-only until US-02.05 |
| 6 | US-03.01 | Plan requirements | 3 | Requirements by Claude, grouped by epic, shown in the browser |
| 7 | US-04.01 | Plan workflow | 4 | Workflow generated by Claude with the workflow tools, validated |
| 8 | US-04.02 | Plan workflow | 4 | The workflow shown as a BPMN-like diagram (lanes, events, tasks, arrows) |
| 9 | US-04.06 | Receive confirmed contract (review) | 8 | Review of wish, answers, stack, requirements and workflow before execute; Run shown disabled |
| 10 | US-04.07 | Freeze contract | 8 | Confirm: immutable contract snapshot |

After 20 November, in this order unless the PO changes it: the rest of E-00 (US-00.13, 11, 14, 07, 12, S1) and the E-00 gate; the remaining stories of E-01 to E-04 (all projects page, files, harness choice, GitHub sign-in, question editing, research, technology choice, project document, downloads, task editing, validation, edit operations); E-05 settings; E-06 execution (after the spike); E-07.

**Process for this target (D-13).** Business stories start as soon as the local pipeline and dev exist (US-00.08, US-00.10); the remaining Epic 0 stories and the E-00 gate follow after 20 November (deviation from WFL-070, decided by the PO). Each story still goes through its full loop (`kickoff-prompt.md` 4: tests, verification, review, deployment to dev); no gate or test is weakened. Domain epics E-01 to E-04 stay Active in parallel until their remaining stories are done; their gates and the promotion to test and prod come after 20 November.

**Developers.** From Epic 0 on, other developers work on the engine with their own Claude login and their own key in `.env` (D-12); everyone works through pull requests on `github.com/ekarimova/workflow-engine` (D-09, US-00.09) and keeps `todo.md` true. An example workflow is not shown in the product for now; it may be used as test data where a test needs one (D-10).
