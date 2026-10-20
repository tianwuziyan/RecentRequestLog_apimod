# Implementation Plan

1. Read the task artifacts and frontend quality/spec guides before editing.
2. Search all current stylesheet URL resolution call sites and update
   `getSelfCssUrl()` to derive the sibling CSS path from the actual plugin
   script/link, while preserving the existing fallback behavior.
3. Do not alter the export markup, Font Awesome mapping, export handler, or
   `style.css` selectors unless the live check proves a separate current-source
   defect.
4. Run the live browser regression check before and after reloading the host:
   collapsed export is hidden, expanded export is themed, and the Shadow DOM
   contains both selectors.
5. Run `node --check` for every repository JavaScript file, `git diff --check`,
   and focused static assertions for URL derivation and the unchanged export
   contract.
6. Run the Trellis check agent against the full task scope.
7. Review and update the relevant frontend spec with the actual stylesheet
   discovery contract if the new convention is durable.

Rollback point: before committing, revert only the resolver/spec changes if the
live host still loads the wrong stylesheet. Do not touch unrelated working-tree
changes or the active Bootstrap Guidelines task.
