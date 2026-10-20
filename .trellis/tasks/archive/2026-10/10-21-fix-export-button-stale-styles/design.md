# Design: Fix export button stale Shadow DOM styles

## Problem and boundary

The current repository already contains the intended `.rlog-export-text-btn`
base and collapsed-state rules in `style.css`, but the host can copy an older
stylesheet into the extension's Shadow DOM. In that state the export button
has no matching visual rule and no collapsed-state rule, while the older
`.rlog-read-full-btn` rules remain available. The observed symptoms are
therefore both a style mismatch and a visibility mismatch.

The change is limited to the record action markup, icon mapping, and Shadow
DOM stylesheet loading in `index.js`. The existing export-specific CSS remains
the current-source contract for fresh loads.

## Decision

Keep the export button semantically independent:

```html
<button class="rlog-export-text-btn" ...>
```

Do not reuse the read-full class or handler. Instead, make the existing Shadow
DOM style path detect whether the current export-button base and collapsed
selectors are present in the serialized stylesheet. If the host stylesheet is
stale, load a cache-busted copy of the same `style.css` into the Shadow DOM so
the independent export class receives its own current rules.

Replace the current `fa-download` markup/mapping with the Font Awesome solid
`file-arrow-down` icon, preserving the existing Shadow DOM font shim pattern.
The dedicated export handler remains the only click handler for export.

## Compatibility and rollback

- Fresh style loads continue to use `.rlog-export-text-btn` rules.
- Older copied styles are detected and refreshed rather than silently reused.
- No export data, filename, icon, propagation, or other action-button behavior
  changes.
- Rollback is limited to the Shadow DOM stale-style detection/cache-bust path
  and the icon class/mapping replacement.

## Risks

The cache-busted fallback adds a second stylesheet request only when the
existing stylesheet fails the explicit export-action contract check. It must
not change the normal synchronous stylesheet serialization path.
