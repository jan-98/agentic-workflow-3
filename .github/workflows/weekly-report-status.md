---
name: Weekly Report Status
engine: copilot
on:
  schedule:
    - cron: 'weekly'
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
  copilot-requests: write
safe-outputs:
  mentions: false
  allowed-github-references: []
  max-bot-mentions: 1
  create-issue:
    title-prefix: '[weekly-report] '
    labels: [report]
    close-older-issues: true
    expires: 30
---

# Weekly Activity Report

You are generating a concise weekly activity report for the repository covering the previous 7 full calendar days ending at the workflow start time (UTC).

## Report Window

- **Window**: Last 7 full days ending at workflow start (UTC)
- **Scope**: All commits, issues, and pull requests
- **Grouping**: By activity type (commits, issues, pull requests)
- **Deduplication key**: `weekly-report:activity:{{week_number}}`

## Instructions

1. Query the repository for activity in the past 7 days:
   - **Commits**: Count total commits and summarize key changes
   - **Issues**: Count opened issues by state; list critical or notable issues
   - **Pull Requests**: Count opened/closed PRs; list key merges

2. If NO activity occurred in any category, explicitly state:
   ```
   > [!NOTE]
   > No activity recorded in this repository for the reporting period.
   ```
   Then call `noop("No updates in last 7 full days ({{window_start_utc}} to {{window_end_utc}})")` and stop.

3. If activity exists, generate a report with this structure:

   ### Summary
   - **Reporting Period**: [Start date] to [End date] (UTC)
   - **Commits**: [Count] total commits
   - **Issues**: [Count] opened, [Count] closed
   - **Pull Requests**: [Count] opened, [Count] merged

   ### Commits
   Brief summary of key changes and areas modified. Include file patterns or modules touched.

   <details>
   <summary>View Detailed Commit Log</summary>

   [List of commits with authors and brief descriptions]

   </details>

   ### Issues
   Summary of new issues opened and resolved.

   <details>
   <summary>View Issue Details</summary>

   [List of issues with status, reporter, and brief description]

   </details>

   ### Pull Requests
   Summary of pull request activity.

   <details>
   <summary>View Pull Request Details</summary>

   [List of PRs with status, author, and title]

   </details>

   ### Status
   - Overall activity level: [High/Moderate/Low]
   - Key focus areas: [Areas where most work is happening]

4. Use only `###` (h3) headers for report sections.
5. Use alerts (`> [!NOTE]`, `> [!WARNING]`) instead of emojis.
6. Wrap verbose details in `<details><summary>...</summary>` tags.
7. Do NOT mention users with `@username` or reference issues with `#123` directly (safe-outputs handles escaping).
8. Do NOT add footer attribution — the system appends it automatically.
9. Always include the workflow run in context, formatted as a link: `[§{{run_id}}]({{run_url}})`

## Output

Generate a complete markdown report and publish it as a new GitHub issue using the `create-issue` safe-output configured above.
