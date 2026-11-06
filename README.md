# Agent and coordinator template

Canonical, project-independent instructions for running a multi-agent
software project: one file for the agents, one private file for the
coordinator, plus a sync path so consuming projects stay in step with this
repository.

This repository is the source of truth. Consuming projects do not edit their
copies of these files; they sync them and keep project-specific rules
separate.

## Files

- `AGENTS.md` — instructions every project agent follows: scope, commits,
  branches and worktrees, preflight, parallel work, validation, pull
  requests, chat titles, and the coordinator handoff format.
- `CORDINATOR_AGENT.md` — the coordinator's private operating guide:
  delegation, issue sizing, agent slots and chat titles, the merge queue, and
  status reports. Keep it separate from `AGENTS.md` and do not paste it into
  agent prompts.
- `sync/sync-agent-instructions.mjs` — Node script a project runs to pull
  these files, or to check for drift.
- `sync/sync-agent-instructions.yml` — reference GitHub Actions workflow
  (scheduled plus `workflow_dispatch`) that runs the drift check and opens a
  pull request when a project has drifted.
- `skills/agent-coordinator-comms/` — optional Codex skill for direct
  progress, blocker, and merge handoffs. It standardizes messages over the
  existing chat tools; it is not a live socket or background monitor.

## Consume the template in a project

1. Add the manifest `.agent-template.json` to the project root:

   ```json
   {
     "repository": "OrbiterToad/agent-coordinator-template",
     "ref": "main",
     "files": {
       "AGENTS.md": "AGENTS.md",
       "CORDINATOR_AGENT.md": "CORDINATOR_AGENT.md"
     }
   }
   ```

   Keys are paths in this repository; values are paths in the project.

2. Copy `sync/sync-agent-instructions.mjs` to the project (for example
   `tools/sync-agent-instructions.mjs`) and
   `sync/sync-agent-instructions.yml` to
   `.github/workflows/sync-agent-instructions.yml`.

3. Run the sync once to write the shared files:

   ```bash
   node tools/sync-agent-instructions.mjs
   ```

4. Put everything project-specific in its own files, never in the synced
   ones: `AGENTS.project.md` for agent-facing project rules (default branch,
   required checks, commands, conventions, push policy) and, if the
   coordinator needs them, `CORDINATOR_AGENT.project.md` or a public
   coordinator guide. `AGENTS.md` tells agents to read `AGENTS.project.md`
   and to let it win where it is more specific.

5. Configure the repository secret `AGENT_TEMPLATE_TOKEN` with a token that
   can read this template repository and open pull requests in the project.
   The workflow's default `GITHUB_TOKEN` cannot read another private
   repository. No token value belongs in the repository.

## Stay in sync

- The scheduled workflow runs the drift check weekly and on demand
  (`workflow_dispatch`). When a synced file differs from this repository, it
  writes the template version, pushes `ai/sync-agent-instructions`, and opens
  or updates a pull request. The project's normal required checks gate it.
- Check drift locally at any time:

  ```bash
  node tools/sync-agent-instructions.mjs --check
  ```

  It exits non-zero and names every drifted file.

- Change a shared rule here first, then let the next sync carry it into each
  project. A rule that applies to only one project belongs in that project's
  `AGENTS.project.md`, not here. Do not hand-edit a synced file in a project:
  the next sync overwrites it and the drift check flags it in the meantime.
- Pin `ref` to a tag or commit instead of `main` if a project wants to adopt
  changes deliberately.

## Keeping these instructions generic

Nothing here names a project, a default branch, a CI job, or a model. Agents
read the default branch from the remote or the project file, read required
checks from the project's CI and protection rules, and pick a model from the
user's stated preference. Verify every repository-specific check and
branch-protection rule in the consuming project before relying on it.
