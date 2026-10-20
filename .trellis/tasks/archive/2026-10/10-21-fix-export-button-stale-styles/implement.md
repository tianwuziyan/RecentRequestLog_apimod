# Implementation Plan

1. Read the current task artifacts and frontend quality/spec guides.
2. Replace `fa-download` with the selected `fa-file-arrow-down` markup and
   `\f56d` mapping; remove the now-unused old mapping.
3. Update the Shadow DOM stylesheet path to detect missing export base/
   collapsed selectors and load a cache-busted current stylesheet only for the
   stale path. Keep `.rlog-export-text-btn` independent.
4. Run `node --check` for all repository JavaScript files and `git diff --check`.
5. Run focused static assertions for the independent icon class/mapping, the
   stale-style fallback, the collapsed-state CSS rule, and the unchanged
   export contract.
6. Run the Trellis check agent against the full task scope.
7. Review Markdown/spec impact. Update only relevant internal guidance if the
   compatibility pattern is a durable convention.

Rollback point: before committing, revert only the icon and Shadow DOM style
path changes if host behavior fails validation. Do not alter unrelated
working-tree changes.
