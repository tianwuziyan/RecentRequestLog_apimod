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
