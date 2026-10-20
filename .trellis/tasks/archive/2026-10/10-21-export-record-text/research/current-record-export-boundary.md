# Current record export boundary

## Verified implementation points

- `index.js:4650-4770` renders each record's action group. The current order is search, message expand/collapse, jump-to-bottom, `查看全文`, then delete.
- `index.js:5092-5156` binds record action clicks. Action handlers stop propagation before operating on the record.
- `index.js:5619-5632` defines `getReadContent(record, format)`. The default `formatted` branch returns `getFullPromptText(record)`; the `raw` branch serializes `record.rawBody` and must not be used for this export.
- `index.js:2889-2917` stores finalized response text in `record.reply.content`; a record without a finalized response has `record.reply === null`.
- `style.css:1062-1086`, `1104-1112`, `1180-1187`, and `2603-2612` define the shared action-button, collapsed-state, search-state, and mobile rules that a new export button must join.
- `README.md` and the record-action step in `tour.js` are the user-facing descriptions that become incomplete when the new action is added.

## Required compatibility boundary

Use the existing formatted full-view helper and the raw `record.reply.content` value. Do not change request capture, reply parsing, record shape, API-mode display-name logic, or the raw-format toggle in the full-view overlay.
