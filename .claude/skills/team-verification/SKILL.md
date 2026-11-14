---
name: team-verification
description: "Run the team verification round of a built story: local pipeline, one verdict per active role, the verdict table and the evidence block in the story file. Use when the human types /team-verification US-ee.ss."
argument-hint: "US-ee.ss"
disable-model-invocation: true
---

# Team verification of $ARGUMENTS

Follow `.claude/skills/workflow/workflow.md` 5.1 (verification round), 8 (Definition of Done) and 17.3 (evidence block). The documents win over this skill.

1. **Inputs.** Read `ai-docs/stories/$ARGUMENTS.md`, its task specs in `ai-docs/tasks/` and the agent reports. List the changed files (`git diff --stat dev...HEAD`).
2. **Pipeline.** Run `ci/run all-local`. If the command does not exist yet (early Epic 0), run what exists and record every missing step as "not run" with the reason. A red step stops the round: the story returns to the owner of the cause (AGT-023).
3. **Verdicts.** For every active role in `CLAUDE.md` section 9, get one verdict: `pass`, `fail` (with the finding and rule ID) or `n.a.` (with the reason). Use the matching subagent; give it the changed files, the story card and its contracts, **not** the authors' reasoning (AGT-083). What each role verifies is the table in `.claude/skills/workflow/workflow.md` 5.1.
   - Product Owner: ask the human; record the answer and the date.
   - Reviewer: the `reviewer` subagent checks the code review is done and all other verdicts are present.
   - Auditor: only at stage 16 or by sample; otherwise `n.a. (sampled later, AUD-050)`.
4. **Record.** Write the verdict table into the story file:

   | Role | Verdict | Finding / reason | Rule IDs |
   |---|---|---|---|

5. **Fail.** Any `fail` returns the story to the owner of the cause with the finding. Do not write the evidence block. Stop.
6. **Pass.** Write the evidence block of `.claude/skills/workflow/workflow.md` 17.3 into the story file: criterion → test, verdicts, contributions, last full run (date, steps, result), loops needed, findings closed, PR link (or "not yet merged").
7. **Do not merge.** Merging is the human's (or the Lead's after reviewer approval) decision; propose `gh pr create` with the story id in the title.
8. **Checkpoint.** Update the checkpoint in the story file (AGT-067).
9. **Deploy and record.** After the merge, the merge run deploys to dev (`ci/run deploy dev`, `ci/run verify dev`; "not available" while no dev host exists). Record the build id and result under "Deployments" in `todo.md`, move the story to "Done" with the date, set the next story as current position, and mark the story Done in `ai-docs/epics/E-<ee>.md`. When it was the last story of the epic, write "epic gate" as the next action: `/audit-report AP2 E-<ee>`, then `promote dev test`, `verify test`, PO approval, `promote test prod`, `verify prod` (`ai-docs/plan.md`).
