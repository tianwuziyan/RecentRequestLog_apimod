# Quality Guidelines

> Code quality standards for frontend development.

---

## Overview

<!--
Document your project's quality standards here.

Questions to answer:
- What patterns are forbidden?
- What linting rules do you enforce?
- What are your testing requirements?
- What code review standards apply?
-->

(To be filled by the team)

---

## Forbidden Patterns

<!-- Patterns that should never be used and why -->

(To be filled by the team)

---

## Required Patterns

<!-- Patterns that must always be used -->

(To be filled by the team)

---

## Testing Requirements

<!-- What level of testing is expected -->

(To be filled by the team)

---

## Code Review Checklist

<!-- What reviewers should check -->

(To be filled by the team)

## Record Text Export Contract

Record-level text export keeps the formatted full-view request and the returned
`RESPONSE` message as separate browser downloads.

- The full-view file must use `getReadContent(record, 'formatted')`. Do not use
  the raw `{}` mode or `record.rawBody` for this action.
- The RESPONSE file is created only when `record.reply.content` is a non-empty
  string, and its content must remain the stored response text.
- The record action handler must stop event propagation so exporting cannot
  toggle the record or trigger another record action.
- Download filenames must be sanitized before assigning the anchor's
  `download` attribute.

```js
downloadTextFile(`${baseName}_全文.txt`, getReadContent(record, 'formatted'));
if (typeof record.reply?.content === 'string' && record.reply.content.length > 0) {
    downloadTextFile(`${baseName}_RESPONSE.txt`, record.reply.content);
}
```

### Export validation matrix

| Record state | Expected downloads |
| --- | --- |
| Record is missing | None |
| `reply` is `null` or response content is empty | Formatted full-view file only |
| Non-empty `reply.content` | Formatted full-view file and RESPONSE file |

When reviewing this behavior, verify the two download counts, the exact source
strings, filename sanitization, and the unchanged action-button behavior in the
host UI.

## Shadow DOM Font Awesome Icon Contract

Icons rendered inside the extension's Shadow DOM must be registered in
`FA_SOLID_CONTENT` in `index.js` as well as referenced by their `fa-solid
fa-*` class in markup. The document-level Font Awesome stylesheet does not
provide the `::before` glyph content across the Shadow DOM boundary.

When adding an icon, search the mapping and the markup together, add the
correct Font Awesome solid code point to the mapping, and verify the generated
shim rule before treating the button as complete.

## Shadow DOM Stylesheet Freshness Contract

The Shadow DOM stylesheet loader may synchronously serialize the host plugin
stylesheet for early layout measurements, but it must not silently reuse a
stale serialized stylesheet. Before using the cached CSS, verify the selectors
required by the current record actions, including both
`.rlog-export-text-btn` and `.rlog-record.collapsed .rlog-export-text-btn`.

If either selector is missing, keep the action's semantic class independent and
load the same `style.css` through the existing Shadow DOM link path with a
cache-busting query parameter. The normal path should remain synchronous; the
cache-busted path is only a compatibility fallback for stale host CSS.

```js
const hasCurrentExportRules = selfCssTextCache.includes('.rlog-export-text-btn')
    && selfCssTextCache.includes('.rlog-record.collapsed .rlog-export-text-btn');
if (!hasCurrentExportRules) return buildSelfCssLink(true);
```

Do not fix stale CSS by borrowing another button's semantic class or handler;
that hides the stylesheet loading problem and couples unrelated actions.

## Shadow DOM Stylesheet URL Discovery Contract

The stylesheet loader must derive the plugin CSS URL from the loaded
`RecentRequestLog*/index.js` script so forks and upstream installations use
their own sibling `style.css`. If a stylesheet link with the same URL path is
already loaded, reuse that link and preserve its query/version parameters.
When runtime script discovery is unavailable, retain the legacy exact-link-id
lookup and hardcoded upstream path as compatibility fallbacks.
