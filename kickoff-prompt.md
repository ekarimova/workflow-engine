# Kickoff: start or continue work on the workflow engine

## Once per machine

```bash
gh repo clone ekarimova/workflow-engine && cd workflow-engine   # ask the owner for collaborator access first
node .claude/hooks/selftest.mjs                 # guard: all cases pass
node .claude/skills/docs-check/check-refs.mjs   # documents: 0 broken references
```

Put **your own** Anthropic key (with a spend limit) into a local `.env` as `ANTHROPIC_API_KEY=...`. It is git-ignored; never commit, paste or log it, and do not export it in the shell that runs Claude Code. Use your own Claude login for Claude Code. Tools: Java 21, Node 24, Docker, GitHub CLI (`ai-docs/README.md` 10).

## Phase 0: first session of the developer who starts it

```bash
claude
```

then type **`/kickoff-epic0`**. It checks your machine and the repository, explains Epic 0 (the technical setup due 20 November) and its order, records you as the Epic 0 developer in `todo.md`, and starts the first story US-00.01. The plan is approved by the owner (Product Owner); open decisions in `todo.md` stay with the owner.

## Every session after that

```bash
claude
```

then type **`/next`**. It reads `todo.md` and the checkpoint of the current story, says where the work stopped, and does the next step: plan the story (you approve), build it task by task, run the checks, open the pull request. You review and merge the pull request on GitHub (not your own), then type `/next` again: it syncs `main`, deploys to dev and takes the next story.

To work on a specific story: `/next US-ee.ss`.

## Branch rules

- `main`: **no commits, no pushes**, not even by the owner. It changes only through a pull request from `dev`, merged by the owner at an epic gate or release.
- `dev`: the working trunk and the default branch; a clone starts on it. No direct commits either.
- Your work: a branch from `dev` per story (`us-ee.ss-short-title`) or per document change (`docs-yyyy-mm-dd-topic`); commit and push it, open the pull request into `dev` (`gh pr create --base dev`). Someone else reviews and merges; never your own.
- `/kickoff-epic0` and `/next` follow these rules for you.

## Where things are

- [`todo.md`](todo.md): where the work stopped, open decisions, what is next. Keep it true after every task, story, decision or deployment; update it in your story branch, take a story by writing your name under **In progress**, keep both sides on a merge conflict.
- [`ai-docs/plan.md`](ai-docs/plan.md): goals, epics, the target for 20 November.
- [`CLAUDE.md`](CLAUDE.md): the rules of every session.
