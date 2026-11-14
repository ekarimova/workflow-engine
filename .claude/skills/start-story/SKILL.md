---
name: start-story
description: "Start a user story: create or read the story card, check the Definition of Ready, plan one-domain tasks with task specs, and hand them to the role subagents. Use when the human types /start-story US-ee.ss."
argument-hint: "US-ee.ss [short title]"
disable-model-invocation: true
---

# Start story $ARGUMENTS

Follow `.claude/skills/workflow/workflow.md` 4 and 5 and `agents.md` 4 and 12. The documents win over this skill (AGT-080).

1. **Identify the story.** The first argument is the story id (`US-ee.ss`). If it is missing or malformed, ask the human for it and stop.
2. **Gate check.** A business story (not in E-00) starts only when US-00.08 (local pipeline) and US-00.10 (local dev) are Done; the other Epic 0 stories may still be open (PO decision D-13 of 2026-11-14, deviation from WFL-070, `ai-docs/plan.md`). Otherwise stop.
3. **Story card.** Read `todo.md` and the epic file `ai-docs/epics/E-<ee>.md` (the story line, sources, deployment). Open `ai-docs/stories/<id>.md`. If it does not exist, copy `ai-docs/stories/_template.md` and fill it:
   - Epic 0 stories: from `.claude/skills/infrastructure/infrastructure.md` section 17 and `.claude/skills/workflow/workflow.md` 9.
   - Engine feature stories: from `ai-docs/requirements.md` (requirement IDs FR-…, QR-…) and `ai-docs/engine-architecture.md`.
   - Write `OPEN:` in front of anything you could not derive. Do not invent scope.
4. **Definition of Ready.** Go through every item of `.claude/skills/workflow/workflow.md` 4.2 and mark it met, not met or not applicable (with reason). One domain, one epic, size S or M, criteria testable, parts decided with reasons (WFL-027).
5. **If not Ready:** list the gaps and the questions for the Product Owner (the human) or the Architect, and stop here. Do not plan around a gap.
6. **Plan.** Use the `lead` subagent to produce the plan: tasks in the order of WFL-050 (design, contract, data, backend, frontend, tests), one domain and one agent per task, `depends_on`, budget, context packet. The lead writes each task spec to `ai-docs/tasks/T-<ee.ss.n>.md` (template `.claude/skills/workflow/workflow.md` 17.4).
7. **Approval.** Present the plan to the human (plan mode) and wait for approval. Change nothing in code before that.
8. **Delegate.** After approval, start the role subagents task by task (`architect`, `ux-ui-designer`, `backend-developer`, `frontend-developer`, `tester`, `devops` as planned). Give each one its task spec path and context packet; subagents start without this session's history (AGT-082). Run two in parallel only on independent files and different domains (AGT-084).
9. **Collect.** Read each agent report (`agents.md` 15.2). A blocker report goes to the human with the decision it needs.
10. **Checkpoint.** Write the checkpoint into the story file: last verified commit and run, next step, open questions (AGT-067). Next step after the build: `/team-verification <id>`.
11. **todo.md.** Update `todo.md` at the root: current position (goal, epic, story, task and states), the tasks of this story under "In progress", new decisions under "Decisions needed", and the date (`kickoff-prompt.md`). Update the story row in `ai-docs/epics/E-<ee>.md`. If this is the first story of an epic, set the epic and its goal to Active in their files and in `ai-docs/plan.md`.
