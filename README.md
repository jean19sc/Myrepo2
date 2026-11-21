# IT PMO Project Board

A single-file Kanban board for IT project management. It is a demo and training tool built around a fictitious bank. It uses vanilla HTML, CSS and JavaScript, with no frameworks, no build step and no external resources.

**Live demo:** _link added after the first deploy_

## Features

- Four columns (Backlog, In Progress, Blocked, Done) with drag and drop, plus a keyboard-friendly "Move" select on every card
- Filters by project, assignee and priority, and a free-text search on title or ID
- Overdue and due-soon badges, and a soft WIP limit on In Progress
- Add-task form with inline validation, and delete with inline confirm and Undo
- Export the filtered view to CSV
- Light and dark theme toggle
- Shortcuts: `N` adds a task, `/` focuses search

## Run locally

Open `index.html` in a browser. Nothing else is needed.

State lives in memory only. Refreshing the page resets the board to the seeded demo tasks.

## Email notification (optional)

Adding a task posts a notification through [FormSubmit](https://formsubmit.co). Set `FORMSUBMIT_ENDPOINT` at the top of the script in `index.html` to `https://formsubmit.co/ajax/<your-email>`.

It ships with the placeholder `YOUR_EMAIL@example.com`, so notifications fail with a warning toast until you replace it. FormSubmit needs one-time activation: the first submission sends a confirmation email, and delivery starts once you click its link. Pages opened from `file://` send `Origin: null`, so test delivery from a hosted origin.

Do not commit your real email address to a public repo.
