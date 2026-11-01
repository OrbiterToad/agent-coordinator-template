# Agent Instructions

These instructions apply to every coding agent working in this repository. Follow the assigned issue and any applicable repository guidance.

## Task scope and coordinator-only guidance

- Work only on the assigned issue. Do not broaden scope or start a competing task without asking the coordinator.
- `CORDINATOR_AGENT.md` is for the coordinator only. Do not read, quote, or include it in your task prompt unless the coordinator explicitly assigns an instruction-maintenance task.
- Preserve user data, secrets, and assets. Do not delete assets or generated files unless the assigned issue clearly requires it.

## Keep the coordinator informed

Send clear milestone updates directly to the coordinator chat. A final answer in your own task chat, a title change, or a PR comment is not a coordinator handoff. When available, use `skills/agent-coordinator-comms/SKILL.md` for the message route and handoff fields. Do not silently stall, contact the user directly, or message other agents directly.

Start every agent-to-coordinator message with
`[Agent <number>] [#<issue> <short task>] [<current state>]`. Put the details
on the next line, beginning with `Further info:`. Use the current state, such
as `preflight complete`, `progress`, `blocked`, `merge request`, `merged`, or
`ready for new task`. Coordinator-to-agent messages use the same structure
with `[Coordinator]` in the first field.

Report at least:

- **Preflight complete:** issue/PR, branch and base SHA, likely file conflicts, and required checks.
- **Roadblock or uncertainty:** what is blocked, evidence, files/branches involved, and what decision is needed. Stop before making risky or conflicting changes.
- **Merge ready:** use the `merge request` state and say “I want to merge PR #<number>” in the `Further info:` line. Include the PR link, tested head SHA, current base SHA, required-check results, review state, and mergeability. The coordinator owns the queue and merge decision.
- **Merge completed:** say “I have merged PR #N” and give the merge SHA only when the coordinator explicitly delegated the merge.
- **Task complete / ready for new work:** provide the issue and PR links, final commit/merge SHA, checks, remaining limitations, and say whether you are ready for a new assignment.

Keep updates concise and factual. Distinguish local results from GitHub CI results.

## Preflight before editing

1. Confirm the assigned issue is open and read its scope and acceptance criteria.
2. Fetch the current remote default branch and record its exact SHA. Do not assume the branch is named `main` or `master`.
3. Inspect open PRs, local and remote branches, worktrees, and uncommitted changes to detect duplicate assignments and understand integration risk. Shared-file overlap between separate branches is expected and does not block implementation.
4. Identify the repository's required checks from its CI workflows and branch protection, plus any task-specific checks.
5. Report genuine blockers or ambiguity promptly. Continue useful implementation in your own branch when another task touches the same files; do not edit another agent's branch/worktree. The coordinator owns conflict resolution during integration.

## Branches, worktrees, and commits

- Keep the primary checkout on its existing branch and do not switch it to your task branch.
- Work in an isolated temporary directory or Git worktree outside the primary checkout.
- Name AI-created branches `ai/<short-description>` unless the coordinator specifies otherwise.
- Commit coherent, reviewable milestones regularly. Use `<type>(<scope>): <description>` (for example, `fix(input): preserve simultaneous key presses`). Do not commit secrets, local tokens, temporary output, or unrelated changes.
- Push your task branch to `origin` and keep the coordinator informed of the pushed head SHA.

## Validation and browser tests

- Run the task-relevant tests and all required CI checks before handing off a PR. Re-check the exact final commit, not an earlier local state.
- If a check fails, determine whether it is caused by your change or is a pre-existing baseline failure. Report evidence; do not hide or silently weaken a test.
- For browser tests, use a free port supported by the project's test configuration. Keep and poll the same long-running command session instead of starting duplicate servers. Never stop a server or process owned by another worktree.
- Report the exact tested SHA and the status of each required check.

## Pull requests and merges

- Open or update a PR against the repository's configured default branch and link it to the assigned issue with an appropriate closing reference.
- Keep the task open while its PR is pending. Follow up on reviews and required checks and address failures.
- Do not merge, enable auto-merge, bypass branch protection, or change required checks. Ask the coordinator for a merge decision after the PR is current and all required reviews/checks pass. The coordinator manages merge order and merges unless explicitly delegated otherwise.
- After a merge, verify the resulting default-branch SHA and report it to the coordinator.
