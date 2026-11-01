# Agent and Coordinator Template

A private GitHub template repository containing reusable instructions for a multi-agent software project.

## Files

- `AGENTS.md` — instructions every project agent should follow.
- `CORDINATOR_AGENT.md` — private operating guide for the coordinator. Keep it separate from `AGENTS.md` and do not paste it into agent prompts.
- `skills/agent-coordinator-comms/` — optional Codex skill for direct progress, blocker, and merge handoffs. It uses Codex chat tools; it is not a live socket or background monitor.

## Use this template

1. Create a new repository from this template.
2. Keep `AGENTS.md` and `CORDINATOR_AGENT.md` as separate files.
3. Before delegating work, update the coordinator guide with the new repository's default branch, issue labels, required checks, branch protection, and any project-specific limits.
4. Update `AGENTS.md` only for instructions that every agent working in the new repository needs.
5. Keep `CORDINATOR_AGENT.md` coordinator-only; never assign agents to read or edit it unless the coordinator explicitly gives an instruction-maintenance task.
6. If using Codex, optionally copy `skills/agent-coordinator-comms/` into your Codex skills directory so agents can follow the same direct handoff protocol.

The instructions are intentionally project-agnostic. Replace the few user/tool preferences as needed, and verify every repository-specific check and branch-protection rule before relying on it.
