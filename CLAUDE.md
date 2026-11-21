# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

`index.html` is the whole app: an IT PMO Kanban board (demo/training tool for a fictitious bank) in vanilla HTML, CSS and JS. There is no build step, package manager, linter or test suite. Run it by opening the file in a browser. The other files in the folder (PDFs, `Doc1.docx`) are unrelated reference material.

## Hard constraints (from the original spec)

- Single file, no frameworks, no external resources. The only network call is the FormSubmit `fetch`, and the only URL in the file is formsubmit.co.
- No storage APIs (`localStorage`, `sessionStorage`, `indexedDB`, cookies). State is in memory, and a refresh resets the board to the seed data. The UI says so.
- No `!important`. No `alert()` or `confirm()`: validation is inline, and delete uses an inline "Delete? Yes / No".
- Task IDs use the format `UOB-ITPM-####`. The `[UOB IT PMO]` email subject is also spec-mandated. Keep the header wordmark neutral ("IT PMO", no logo).

## Architecture (inside the `<script>`)

- A single `state` object (`tasks`, `filters`, `confirmDeleteId`) is the source of truth. `renderBoard()` rebuilds all column DOM from state via `renderCard()` escaped template strings, and updates the count badges and the summary strip. Mutators (`addTask`, `moveTask`, `deleteTask`) change state and then call `renderBoard()`. Don't mutate card DOM directly.
- Events are delegated: drag and drop (`dragstart`, `dragover`, `dragleave`, `drop`, `dragend`) and card buttons are handled on the board container, wired in `wireEvents()`. The keyboard path to move a card is the per-card "Move ▸" select.
- All user-supplied text must go through `escapeHtml()` before it goes into a template string.
- `addTask` is optimistic: the card is added and rendered first. Then `notifyNewTask()` posts to FormSubmit inside try/catch. Success and failure only change the toast (the failure text is "Card added locally — email notification failed"), and the board never breaks.
- Overdue means `status !== "Done"` and the due date is before today. Dates are compared as local-date ISO strings (`todayISO()`), not through `Date` parsing of `YYYY-MM-DD`, which would be UTC.
- Seed tasks use due dates relative to today (`offsetDate`), so some are always overdue.
- Layout: a CSS grid with a breakpoint at 768px (1 column below it, 2 columns up to 1100px, 4 columns above). Colours and spacing are CSS custom properties.

## Config

`FORMSUBMIT_ENDPOINT` at the top of the script holds the notification email address, in the form `https://formsubmit.co/ajax/<email>`. It ships with the placeholder `YOUR_EMAIL@example.com`, so notifications fail (warning toast) until it is replaced. FormSubmit needs one-time activation: the first submission sends a confirmation email, and delivery starts only after its link is clicked. A page opened from `file://` sends `Origin: null`, so test email delivery from a hosted origin if it fails locally.
