---
emoji: ✨
description: Add FAQ highlights to the Daily Updates dialog in index.html
on:
  schedule:
    - cron: '0 */6 * * *'
  workflow_dispatch: {}
permissions:
  contents: read
  copilot-requests: write
tools:
  edit: {}
  web-fetch: {}
  github:
    mode: gh-proxy
    toolsets: [default]
network:
  allowed:
    - gh-aw
safe-outputs:
  create-pull-request:
    allowed-files:
      - index.html
---

# Highlights of the Day Workflow

## Task

Today is calculated from the workflow run's UTC timestamp.

1. **Fetch the FAQ content** from https://github.github.com/gh-aw/reference/faq/ using the web-fetch tool.

2. **Parse and extract FAQ items**: Identify all question-answer pairs in the FAQ. Each FAQ item should have a distinct question and answer.

3. **Read the current index.html** and extract all questions already present in any daily update dialog. Store these as "used questions".

4. **Select an unused FAQ**: Find the first FAQ item whose question is NOT already in index.html's daily update dialogs. If all FAQs are already represented, or no FAQ remains, call `noop` with the reason: "All available FAQs already represented in Daily Updates or no new FAQ available".

5. **Determine the dialog to update**:
   - Calculate today's kebab-case date ID in the format `{month}-{day}-dialog` (e.g., `august-31-dialog`).
   - Check if a dialog with that ID already exists in index.html.
   - **If it exists**: Reuse it. Update the `<h2>` (with `id="{date-id}-question"`) to contain the selected FAQ question, and update the `<p>` (with `id="{date-id}-answer"`) to contain the selected FAQ answer.
   - **If it doesn't exist**: Create a new navigation button and dialog following these structures:
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
     And the matching dialog:
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
         <h2 id="{date-id}-question">{FAQ question}</h2>
         <p id="{date-id}-answer">{FAQ answer}</p>
       </article>
     </dialog>
     ```
   - Date format: Use `{day}(st|nd|rd|th) of {month}` (e.g., `31st of August`).
   - ID format: Use `{month}-{day}-dialog`.

6. **Preserve all existing content**: Do not modify styles.css or any other files. Do not remove or alter any existing daily update entries, dialogs, or other HTML elements.

7. **Handle duplicates**: Before updating any dialog, verify that its current content does not already contain the selected FAQ question. If it does, call `noop` with the reason: "Selected FAQ already present in today's Daily Update dialog".

8. **Create a pull request** with the updated index.html. The safe-output will restrict changes to this file only.

## Safe Outputs

- Use `create-pull-request` to propose the HTML changes with the FAQ content added to the daily update.
- Call `noop` if all FAQs are already represented, no FAQ is available, the selected FAQ is already in today's dialog, or if no update is needed, with a clear reason.
