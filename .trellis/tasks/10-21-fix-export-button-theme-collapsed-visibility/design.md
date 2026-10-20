# Design: Fix export button theme and collapsed visibility

## Root cause and boundary

The previous repair validated export selectors only after calling
`getSelfCssUrl()`. In the live fork, the host stylesheet link is
`third-party_RecentRequestLog_apimod-css` and its URL points to the `_apimod`
directory. The case-sensitive selector in `getSelfCssUrl()` misses that link,
then the hardcoded upstream-style fallback points at `RecentRequestLog/style.css`.
`buildSelfCssElement()` therefore appends a link that does not provide the
current stylesheet to the Shadow DOM. Native button defaults become visible,
and neither the expanded action styles nor the collapsed selector can apply.

The change is limited to the existing stylesheet URL resolver and its comments;
the current `style.css` action rules remain the source of truth. The export
markup and handler stay unchanged.

## Decision

Resolve the plugin stylesheet in this order:

1. Find the loaded plugin `index.js` script whose URL identifies
   `RecentRequestLog` and derive its sibling `style.css` URL.
2. Prefer the already loaded stylesheet link whose URL has that same path, so
   existing query/version parameters are preserved.
3. If the sibling link is not present yet, return the derived absolute CSS URL
   for the current plugin directory.
4. Retain the existing exact stylesheet-link/fallback behavior only as a
   compatibility fallback when runtime discovery is unavailable.

`buildSelfCssElement()` continues to serialize the current CSSOM synchronously
when possible. Its existing stale-selector detection and cache-busted link
fallback remain in place, but now operate on the actual `_apimod` stylesheet.

## Compatibility and rollback

- Upstream installations with `RecentRequestLog/index.js` resolve to their own
  sibling `style.css` rather than the fork path.
- API-mode storage, request tracking, export data, event handlers, and other
  actions are untouched.
- Rollback is limited to the resolver implementation and its task/spec notes;
  no CSS or data migration is required.

## Validation seam

The connected SillyTavern page provides a direct DOM/CSSOM seam: inspect the
panel Shadow DOM, verify the serialized stylesheet contains both export
selectors, and compare computed styles for collapsed and expanded rows. The
same check reproduced the failure before this change and will be rerun after
reload.
