## AGENDA : What Are We Going to Automate Today?

| # | Part | Outcome |
| :--: | --- | --- |
| 4 | [Weekly Activity Report Workflow](#4-a-custom-agentic-workflow---weekly-activity-report) | Create a custom workflow that publishes a weekly report issue |
| 5 | [Code-Simplifier from GH Agentics](#5-install-prebaked-code-simplifier-from-gh-agentics) | Install a pre-built workflow that publishes daily status issues |
| 6 | [Create a Daily App Update Workflow](#6-create-a-daily-app-update-workflow) | Create a custom workflow that updates the web app |
| 7 | [Optional: Create a Daily FAQ Workflow](#7-optional-create-a-daily-faq-workflow) | Create a custom workflow that fetches and adds FAQ content |
| 8 | [Optional: Add the Grumpy Reviewer](#8-optional-add-the-grumpy-reviewer) | Install a pre-built workflow that reviews pull requests |

# 🎉 CONGRATULATIONS FOR MAKING IT THIS FAR!!! 🚀🥳

## NOW IT'S TIME FOR THE MOST FUN PART! 🤖✨

Need to revisit setup? Review the
[completed sections 1–3](completed-sections1-2-3.md).

## 4. A custom agentic workflow - Weekly Activity Report 

> [!NOTE]
> **Status update: so far, so good.** 
> Don't forget to synch your local with remote. Hopefully now README is shorter clearer 😊🤞🍀

```bash
git pull --ff-only origin main
```

Let's build our first gh aw to track what's going on in our repository on a weekly basis


<p align="center">
   <img src="images/promptToExecution.png" width="100%" alt="Agentic workflow process from prompt to GitHub Actions execution">
</p>

1. In Copilot Chat, select the **Agentic Workflows Agent**.

2. Submit this prompt:

   ```text
   Create .github/workflows/weekly-report-status.md as an Agentic Workflow
   Markdown file.

   Requirements:
   - Use the Copilot engine.
   - Name the workflow Weekly Report Status.
   - Run on schedule every Monday and on demand with workflow_dispatch.
   - Grant contents: read, issues: read, pull-requests: read, and
     copilot-requests: write permissions.
   - Configure safe-outputs.create-issue with a title prefix of
     "[weekly-report] " and a maximum of one issue.
   - Generate a concise activity report for the previous seven days, covering
     commits, issues, and pull requests. Publish the report in a new issue and
     state clearly when no activity occurred.
   - Do not compile the workflow.
   ```

3. Review the generated Markdown, then validate, compile, commit, and run it:

   ```bash
   gh aw validate weekly-report-status
   gh aw compile .github/workflows/weekly-report-status.md
   git add . 
   git commit -m "added custom weekly report status workflow"
   git push origin main
   gh aw run weekly-report-status
   ```

4. Follow the run in the **Actions** tab. When it succeeds, open the
   **Issues** tab and review the new issue whose title starts with
   `[weekly-report]`.

> [!NOTE]
> Agentic workflow Markdown files (`.md`) are the source of truth. Their
> `.lock.yml` files are generated GitHub Actions workflows. Never edit a
> `.lock.yml` file manually; run
> `gh aw compile .github/workflows/FILENAME.md` after changing its Markdown
> source, then commit both files.

## 5. Install Prebaked Code-Simplifier from GH Agentics

[Agentics](https://github.com/githubnext/agentics) is an open-source collection
of GitHub Agentic Workflows that you can quickly install in your repositories.

Install the
[Code Simplifier](https://github.com/githubnext/agentics/blob/main/docs/code-simplifier.md) automatically analyze recently modified code and create pull requests with simplifications that improve clarity and maintainability

```bash
# Add the prebuilt workflow to your repository
gh aw add-wizard githubnext/agentics/code-simplifier
```

Use these selections when prompted:

| # | Prompt | Selection |
| :--: | --- | --- |
| 1 | Coding agent | **GitHub Copilot CLI** |
| 2 | Authentication | **Copilot requests** |
| 3 | Schedule | **Daily** |
| 4 | Create a setup pull request, if offered | **Yes** |
| 5 | Setup pull request action | **Attempt to merge** |

Selecting **Copilot requests** makes the wizard add
`copilot-requests: write` to the workflow automatically. No PAT or
`COPILOT_GITHUB_TOKEN` secret is required.

<table>
   <tr>
      <td width="50%" align="center">
         <a href="images/daily-p1.png"><img src="images/daily-p1.png" width="100%" alt="Select GitHub Copilot CLI as the coding agent"></a>
         <br><sub><strong>Coding agent: GitHub Copilot CLI</strong></sub>
      </td>
      <td width="50%" align="center">
         <a href="images/daily-p2.png"><img src="images/daily-p2.png" width="100%" alt="Select Copilot requests for authentication"></a>
         <br><sub><strong>Authentication: Copilot requests</strong></sub>
      </td>
   </tr>
   <tr>
      <td width="50%" align="center">
         <a href="images/daily-p3.png"><img src="images/daily-p3.png" width="100%" alt="Choose the daily workflow schedule"></a>
         <br><sub><strong>Schedule: Daily</strong></sub>
      </td>
      <td width="50%" align="center">
         <a href="images/daily-p5.png"><img src="images/daily-p5.png" width="100%" alt="Choose whether to attempt to merge the generated pull request"></a>
         <br><sub><strong>Setup pull request: Attempt to merge</strong></sub>
      </td>
   </tr>
</table>

The wizard creates and compiles
`.github/workflows/code-simplifier.md` and opens a setup pull
request. It may also offer to merge the pull request and start the first run. 

After the setup pull request is merged, update your local default branch:

```bash
git pull --ff-only origin main
```

### Manually Editing Permissions

Add the following lines to the frontmatter of
`.github/workflows/code-simplifier.md`:

```yaml
permissions:
   actions: read
   attestations: read
   checks: read
   contents: read
   deployments: read
   discussions: read
   id-token: none
   issues: read
   packages: read
   pages: read
   pull-requests: read
   security-events: read
   statuses: read
   copilot-requests: write
```

### Compile Commit Push Again to enable updates:

```bash
gh aw compile .github/workflows/code-simplifier.md
git add .
git commit -m "added prebuilt aw Code-Simplifier" 
git push origin main
gh aw status
# run directly from the UI or
gh aw run code-simplifier
```

Open the repository's **Actions** tab to follow the run. When it succeeds, open
the **Issues** tab and find the new repository quality improvement report.

## 6. Create a Daily App Update Workflow

1. In Copilot Chat, select the **Agentic Workflows Agent**.

2. Submit this prompt:

   ```text
   Create .github/workflows/new-day.md as an Agentic Workflow Markdown file.

   Requirements:
   - Use the Copilot engine.
   - Run once per day and on demand with workflow_dispatch.
   - Grant contents: read and copilot-requests: write permissions.
   - Enable file editing with tools.edit.
   - Configure safe-outputs.create-pull-request with index.html as the only
     allowed file and a maximum of one pull request.
   - Use the workflow run's UTC date.
   - In index.html, add that date to the existing Daily Updates navigation and
     add a matching accessible dialog that confirms the daily update ran.
   - Follow the existing HTML structure, ID conventions, date wording, and
     styling. Do not modify styles.css.
   - Do not duplicate a date, navigation control, or dialog. If the UTC date is
     already present, make no change.
   - Preserve every existing daily update.
   - Validate the workflow with gh aw validate new-day.
   - Do not compile the workflow.
   ```

3. Review the generated Markdown, then validate, compile, commit, and run it:

   ```bash
   gh aw validate new-day
   gh aw compile .github/workflows/new-day.md
   git add .
   git commit -m "added daily website update workflow"
   git push origin main
   gh aw run new-day
   ```

4. Follow the run in the **Actions** tab. Review and merge the pull request it
   creates, then update your Codespace without creating a merge commit:

   ```bash
   git pull --ff-only origin main
   ```

   Complete this step before creating the next workflow so both workflows start
   from the same version of `index.html`.

## 7. Optional: Create a Daily FAQ Workflow

1. In Copilot Chat, keep **Agentic Workflows Agent** selected.

2. Submit this prompt:

   ```text
   Create .github/workflows/highlights-of-day.md as an Agentic Workflow Markdown
   file.

   Requirements:
   - Use the Copilot engine.
   - Run every six hours and on demand with workflow_dispatch.
   - Grant contents: read and copilot-requests: write permissions.
   - Enable tools.edit and tools.web-fetch.
   - Configure network.allowed with github.github.com.
   - Fetch the GitHub Agentic Workflows FAQ:
     https://github.github.com/gh-aw/reference/faq/
   - Select one FAQ that is not already represented in index.html.
   - Use the workflow run's UTC date.
   - Add the selected question and a concise, accurate answer to the matching
     Daily Updates dialog in index.html. If that date already has a placeholder
     dialog, reuse it; otherwise add a matching navigation control and dialog.
   - Match the existing HTML structure, ID conventions, date wording, and
     styling. Preserve all existing updates.
   - Never duplicate a date, navigation control, dialog, or FAQ. If today's
     dialog already contains an FAQ, or no unused FAQ remains, make no change.
   - Configure safe-outputs.create-pull-request with index.html as the only
     allowed file and a maximum of one pull request.
   - Validate the workflow with gh aw validate highlights-of-day.
   - Do not compile the workflow.
   ```

3. Review the generated Markdown, then validate, compile, commit, and run it:

   ```bash
   gh aw validate highlights-of-day
   gh aw compile .github/workflows/highlights-of-day.md
   git add . 
   git commit -m "fetched and added highlights of the day"
   git push origin main
   gh aw run highlights-of-day
   ```

4. Follow the run in the **Actions** tab, then review and merge its pull
   request. A later run on the same UTC day should not create a duplicate
   update.

> [!NOTE]
> **What is a safe output?**
>
> A safe output is a controlled GitHub write operation declared under
> `safe-outputs:`. The agent remains read-only and requests a narrowly configured
> operation that a separate, permission-controlled job validates and performs.
>
> | Safe output | What it can do | Example use |
> | --- | --- | --- |
> | `create-issue` | Open a new issue | Report a test-quality gap |
> | `add-comment` | Comment on an issue or pull request | Review changed tests |
> | `create-pull-request` | Propose file changes in a pull request | Update stale documentation |
> | `add-labels` | Add allowed labels | Triage incoming issues |
>
> `noop` is always available. Use it when the workflow succeeds but finds
> nothing useful to change.
>
> #### Configuration Examples
>
> **Create an issue:**
>
> ```yaml
> safe-outputs:
>   create-issue:
>     title-prefix: "[quality] "
>     max: 1
> ```
>
> **Create a pull request:**
>
> ```yaml
> safe-outputs:
>   create-pull-request:
>     draft: true
>     allowed-files:
>       - "**/*.md"
>     max: 1
> ```

## 8. Optional: Add the Grumpy Reviewer

The
[Grumpy Reviewer](https://github.com/githubnext/agentics/blob/main/docs/grumpy-reviewer.md)
is an on-demand workflow that reviews changed lines in a pull request and posts
up to five focused comments.

Install it from a clean working tree:

```bash
gh aw add-wizard githubnext/agentics/grumpy-reviewer
```

Choose **GitHub Copilot CLI**, **Copilot requests**, and **Attempt to merge**
when prompted. After the setup pull request is merged, open any pull request
and add one of these comments:

```text
/grumpy
```

```text
/grumpy focus on security
```

```text
/grumpy check error handling especially
```

## Troubleshooting

| Problem | Resolution |
| --- | --- |
| `gh` uses the restricted Codespaces token | Run `unset GH_TOKEN GITHUB_TOKEN` in the current terminal. |
| `add-wizard` reports a dirty working tree | Commit the intended changes before rerunning the wizard. |
| Copilot requests is unavailable or returns `403` | Confirm the repository belongs to the instructor's organization, then contact the instructor to verify Copilot billing. |
| A workflow is missing or its lock file is stale | Run `git pull --ff-only origin main`, then `gh aw compile .github/workflows/FILENAME.md` and commit both workflow files. |
| A workflow cannot create a pull request | Recheck **Settings > Actions > General**, or ask the instructor whether an organization policy blocks pull request creation. |

## References

- [GitHub Agentic Workflows documentation](https://github.github.com/gh-aw/)
- [GH-AW-WORKSHOP](https://githubnext.github.io/gh-aw-workshop/#00-welcome)
- [`gh-aw` CLI reference](https://github.github.com/gh-aw/setup/cli/)
- [Workflow permissions](https://github.github.com/gh-aw/reference/permissions/)
- [Safe outputs](https://github.github.com/gh-aw/reference/safe-outputs/)

## Appendix

### How to Check the Initial Workflow Status

After creating your repository from the template:

1. Open the repository's **Actions** tab.
2. Find the **configure-codespaces** run named **Initial commit**.
3. Wait for the run to display a green check mark before creating your
   Codespace. After a successful run, **configure-codespaces** appears as
   **Disabled** because it is a one-time setup workflow.

<p align="center">
   <img src="images/initial-boot-fordevcontainer.jpg" width="100%" alt="GitHub Actions page showing the successful initial configure-codespaces workflow run">
</p>

For personal-repository setup instructions, see the [Appendix](appendix.md).
