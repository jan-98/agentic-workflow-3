---
emoji: 📅
description: Add daily updates to the index.html navigation and dialog when the workflow runs
on:
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch: {}
permissions:
  contents: read
  copilot-requests: write
tools:
  edit: {}
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  create-pull-request:
    allowed-files:
      - index.html
---

# Daily Updates Workflow

## Task

Today is **{{ '{{ github.event.workflow.created_at' }} | utc_date }}**, or you can calculate it from the workflow run timestamp.

1. **Read the current index.html** and check if an entry already exists for today's UTC date.
2. **If the date already exists**: Do not make any changes. Call `noop` with the reason "Date already present in Daily Updates".
3. **If the date does not exist**: Add a new daily update entry following these rules:
   - Add a new `<li>` with a `<button>` to the `<ul class="daily-updates-list">` inside the `<aside class="daily-updates">` section.
   - Use this button structure (replace `{date-id}` with the kebab-case ID and `{display-date}` with the formatted date):
     ```html
     <li>
       <button
         class="daily-update-trigger"
         type="button"
         aria-haspopup="dialog"
         aria-controls="{date-id}-dialog"
         data-dialog-trigger
       >
         <span>{display-date}</span>
         <span aria-hidden="true">&#8594;</span>
       </button>
     </li>
     ```
   - ID format: Use `{month}-{day}-dialog` (e.g., `august-1-dialog`, `december-25-dialog`).
   - Display format: Use `{day}(st|nd|rd|th) of {month}` (e.g., `1st of August`, `21st of December`).

4. **Add the matching dialog** below the existing dialogs, using this structure:
   ```html
   <dialog
     class="daily-update-dialog"
     id="{date-id}-dialog"
     aria-labelledby="{date-id}-question"
     aria-describedby="{date-id}-answer"
   >
     <article class="daily-update-dialog-content">
       <header class="daily-update-dialog-header">
         <p>Daily Update / {display-date}</p>
         <form method="dialog">
           <button class="dialog-close" type="submit" aria-label="Close dialog" title="Close dialog">
             <span aria-hidden="true">&#10005;</span>
           </button>
         </form>
       </header>
       <h2 id="{date-id}-question">Daily update for {display-date}</h2>
       <p id="{date-id}-answer">
         The daily update workflow ran successfully on {display-date}.
       </p>
     </article>
   </dialog>
   ```

5. **Preserve all existing content**: Do not modify styles.css or any other files. Do not remove or modify any existing daily update entries or dialogs.

6. **Create a pull request** with the updated index.html. The safe-output will restrict changes to this file only, so the PR will be clean and focused.

## Safe Outputs

- Use `create-pull-request` to propose the HTML changes.
- Call `noop` if the date already exists in the Daily Updates navigation, with the reason: "Date already present in Daily Updates".
