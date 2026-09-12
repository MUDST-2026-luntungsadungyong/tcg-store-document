# AI Working Rules for TCG Project

These rules apply to any AI assistant working in the TCG repositories.

## Before taking action

1. Read [`PROJECT_CURRENT_STATUS.md`](./PROJECT_CURRENT_STATUS.md).
2. Identify the repository and current branch. Do not assume `main` is current locally.
3. For time-sensitive claims, verify Jira, GitHub/CI, and runtime directly.
4. Preserve unrelated dirty worktree changes.

## Evidence language

Use these terms exactly:

- **Done:** Jira is Done and implementation evidence is recorded.
- **In review:** code/PR exists but delivery is not final.
- **Blocked:** a specific issue prevents progress; name it and its owner/next action.
- **Unverified:** evidence has not been collected in the current check.
- **Runtime verified:** state the tested URL/command, time, and commit/image.

Do not call a feature complete from source code, screenshots, or a successful build alone.

## When changing project context

Update `PROJECT_CURRENT_STATUS.md` only with evidence from a source of truth. Include the date, editor, and evidence in the update log. Never add secrets or personal data.

For a material implementation, also update the corresponding Jira issue and link the PR/CI result. If the update is only a local experiment, mark it as local/unverified rather than changing shared delivery status.

## Required handoff format

Every implementation handoff should state:

1. What changed and repository/branch/commit.
2. Validation actually run, including result.
3. What remains unverified or blocked.
4. The next smallest action and its owner when known.

## Priority order when sources conflict

1. Live Jira for workflow ownership/status.
2. Current GitHub PR, commit, and Actions result for merged/CI state.
3. Current checkout test/build output for local code state.
4. Current runtime/browser evidence for operational state.
5. Historical documents only as context, never as proof of current state.
