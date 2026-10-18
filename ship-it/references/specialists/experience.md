# Experience Specialists

Load when the change has a user-facing surface: web or mobile UI, a public API or SDK, a CLI, documentation, or messages users read (emails, errors, notifications).

---

## Frontend Engineer

**Activate when:** web UI changes.

| Stage | Lens |
|---|---|
| Design | Component structure using the existing design system and components. State management and data loading. Every state from the design (empty, loading, error, success). |
| Build | Reuse existing components and tokens before creating new ones. Handle loading and error states. Keep bundle size in check. |
| Verify | Render every state and look at it. Check narrow and wide viewports, and light and dark themes if supported. |

**Red flags:** new one-off components that duplicate the design system; spinners forever on error; layout that breaks at phone width.

**Gate evidence:** screenshots of each state.

### Reviewer Injection — Frontend Engineer

Append to reviewer prompts when this specialist is active.

- Are all states rendered and checked: empty, loading, error, success?
- Does the layout hold at phone width and in both light and dark themes if supported?

---

## Mobile Engineer

**Activate when:** native or cross-platform mobile app changes.

| Stage | Lens |
|---|---|
| Design | Offline and poor-network behavior. Platform conventions. Older app versions still in use calling the same backend. |
| Build | Handle backgrounding, permissions, and interrupted requests. |
| Verify | Test on small screens and slow networks. |
| Launch | Store review time; you can't roll back an installed app, so ship risky changes behind a server-side flag. |

**Red flags:** a backend change that breaks app versions still installed.

---

## Accessibility Specialist

**Activate when:** any UI change.

| Stage | Lens |
|---|---|
| Design | Keyboard flow, focus order, labels, contrast, text alternatives, motion. |
| Build | Semantic HTML or native controls first; ARIA only where needed. |
| Verify | Navigate by keyboard only. Run the repo's accessibility checker if there is one. Check contrast. |

**Red flags:** clickable divs; icons with no label; focus lost after a dialog closes.

**Gate evidence:** keyboard walkthrough done; checker output.

### Reviewer Injection — Accessibility Specialist

Append to reviewer prompts when this specialist is active.

- Is the UI navigable by keyboard only, with no focus traps?
- Do interactive elements have labels (no unlabeled icon buttons)?
- Does contrast meet the project's stated standard?

---

## API Designer / Developer Experience

**Activate when:** a public or cross-team API, SDK, CLI, webhook, or event contract is added or changed.

| Stage | Lens |
|---|---|
| Define | Who calls this and what do they need to do? Treat breaking changes as a product decision. |
| Design | Consistent naming with existing APIs, safe defaults, pagination, idempotency for writes, versioning, and error messages that say what to do next. Write a usage example before the implementation. |
| Build | Validate inputs with clear errors. Keep the change additive where possible. |
| Verify | Call it as a client would, from the docs alone. Check backward compatibility against existing clients or contract tests. |
| Launch | Changelog, migration guide for anything breaking, deprecation notices with dates. |

**Red flags:** silent breaking changes; errors like "invalid request" with no detail; inconsistent naming across endpoints.

**Gate evidence:** a working usage example; compatibility check results.

### Reviewer Injection — API Designer / DX

Append to reviewer prompts when this specialist is active.

- Are breaking changes flagged as product decisions, not implementation details?
- Do error messages say what happened and what to do next?
- Is there a working usage example written from the docs alone?

---

## Technical Writer

**Activate when:** behavior users see changes, setup changes, or a new feature needs explaining.

| Stage | Lens |
|---|---|
| Design | Write the user-facing description early; if it's hard to explain, the design may be too complicated. |
| Launch | Update README, docs, CHANGELOG, and in-product copy. Write for the user's task, not the implementation. |

**Red flags:** docs that describe how it was built instead of how to use it; outdated screenshots and examples.

### Reviewer Injection — Technical Writer

Append to reviewer prompts when this specialist is active.

- Are README, docs, and CHANGELOG updated for any behavior users see?
- Does the documentation describe how to use it, not how it was built?

---

## Localization

**Activate when:** the product supports multiple languages or regions.

| Stage | Lens |
|---|---|
| Build | No hard-coded user-facing strings; locale-aware dates, numbers, and currency; layouts that tolerate longer text and right-to-left scripts. |
| Verify | Check with a long-text locale and, if supported, a right-to-left one. |
