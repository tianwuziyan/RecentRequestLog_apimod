# Fix export button theme and collapsed visibility

## Goal

Restore the export action to the record-action UI contract in the real
SillyTavern host: it must use the same transparent themed button treatment as
the neighboring actions and disappear when a record is collapsed.

## Background and confirmed evidence

- The current source already contains the intended export selectors in
  `style.css:1062-1086` and `style.css:1106-1115`.
- The live SillyTavern page currently renders the first record with class
  `rlog-record collapsed`, but its export button computes to browser defaults:
  `display: block`, `background-color: rgb(239, 239, 239)`, `border-style:
  outset`, and black text. The neighboring read-full button is hidden and
  themed.
- The live panel Shadow DOM contains only the Font Awesome shim and minimal
  shim styles; it contains no serialized stylesheet with
  `.rlog-export-text-btn` or its collapsed selector.
- The host's actual plugin stylesheet is
  `.../RecentRequestLog_apimod/style.css`, loaded by
  `third-party_RecentRequestLog_apimod-css`. The current `getSelfCssUrl()` only
  recognizes an exact `RecentRequestLog-css` suffix and otherwise falls back to
  `/scripts/extensions/third-party/RecentRequestLog/style.css`, so the fork's
  current stylesheet is not found by the Shadow DOM loader.
- A live regression check reproduced the user-visible failure before the fix:
  the collapsed row had a visible default export button while the read-full
  button was hidden.

## Requirements

- Resolve the current plugin stylesheet from the actual loaded plugin script or
  the matching stylesheet link, including this fork's `_apimod` directory.
- Keep the existing synchronous CSSOM serialization path when the correct
  stylesheet is available; use the existing link/cache-bust fallback only when
  CSSOM access or freshness requires it.
- In an expanded record, the export button must match the neighboring record
  action buttons for transparent background, border, dimensions, text/icon
  color, typography, spacing, and hover behavior.
- In a record with the `collapsed` class, the export button must be hidden just
  like the read-full and delete actions.
- Preserve the independent `.rlog-export-text-btn` semantic class, icon,
  click handler, formatted full-view export, optional `RESPONSE` export,
  filename sanitization, and event propagation behavior.
- Do not alter unrelated record actions, panel-window collapse behavior, or
  upstream integration code.

## Acceptance Criteria

- [ ] After reloading the real host, a collapsed record has no visible export
  button and its computed `display` is `none`.
- [ ] After expanding the same record, the export button is visible and its
  computed background, border, color, dimensions, and display behavior match
  the read-full action button; no browser-default black/white button remains.
- [ ] The Shadow DOM contains current plugin CSS rules for both
  `.rlog-export-text-btn` and `.rlog-record.collapsed .rlog-export-text-btn`.
- [ ] The live regression check is red before the fix and green after the fix.
- [ ] All repository JavaScript files pass `node --check`; `git diff --check`
  passes; focused static assertions cover the path resolver and unchanged
  export contract.
- [ ] Relevant Markdown/spec documentation is reviewed and synchronized if
  the stylesheet-path contract changes.

## Out of Scope

- Do not change the selected `file-arrow-down` icon or export file contents.
- Do not duplicate the entire stylesheet or permanently add a second button
  styling system when correcting the stylesheet path is sufficient.
- Do not reuse the read-full semantic class or handler.
- Do not modify the upstream remote, API-mode request behavior, or unrelated
  active tasks.

## Risks and Deferred Validation

- The browser acceptance loop depends on the connected SillyTavern host and
  must be rerun after the extension reload; static checks alone are not enough
  for this Shadow DOM regression.
- If the host exposes neither the current plugin script nor a matching
  stylesheet link, the hardcoded path remains a last-resort fallback and may
  require a future manifest/runtime contract.
