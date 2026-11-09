# GitNexus workflow

Use GitNexus for codebase exploration, cross-file debugging, planning changes, impact analysis, and review when it can add useful signal. Skip it for trivial, isolated tasks.

## Orchestrator: index gate

- Before delegating a GitNexus-relevant task, call `list_repos` and match the active project by its full path, not just its repository name. An index for another checkout is not a match.
- If the project is not indexed, offer the user `gitnexus analyze --index-only`. Explain that it creates the local GitNexus index without injecting instructions into `AGENTS.md`/`CLAUDE.md` or installing project skills. Do not run shell commands or start Planner while waiting for the user's choice; the user can run it from the repository root or decline.
- If the user declines, continue without GitNexus and tell Planner it is unavailable. If the user runs the command, recheck `list_repos` before delegating.
- Pass Planner the matching repository path/name and the index timestamp/commit when available. Do not claim the index is fresh based on presence alone.

## Specialist agents

- Use GitNexus only for the active checkout's matching index. Check reported freshness/coverage when available. If the index is missing, stale, or ambiguous, say so and continue using ordinary file search and source inspection; do not independently launch indexing or repeatedly prompt the user.
- Use `query` to discover relevant code/processes, `context` for symbol relationships, `impact` before changes, and `detect_changes` for a working diff. Use graph results as navigation and impact evidence, not as proof of behavior.
- Verify important conclusions against current source, tests, and the actual diff. Do not infer that a behavior or dependency is absent merely because the graph did not return it; respect index coverage, skipped files, and staleness.
- Executors may use `impact` before edits and `detect_changes` after edits. Reviewer must independently inspect the diff and source; GitNexus is supplemental and must not replace the review skill.
- The MCP server is configured read-only. Do not attempt to apply renames or use GitNexus to mutate project/index data. Indexing remains an explicit user decision.
