# WCAG Standards

## Authoritative Sources
- WCAG 2.2 Specification — https://www.w3.org/TR/WCAG22/
- WAI-ARIA 1.2 Specification — https://www.w3.org/TR/wai-aria-1.2/
- ARIA Authoring Practices Guide — https://www.w3.org/WAI/ARIA/apg/
- axe-core Rules Reference — https://github.com/dequelabs/axe-core

## Conformance Levels
- **Level A** — Required. Must pass.
- **Level AA** — Required. Must pass.
- **Level AAA** — Nice to have. Flag as potential improvements, never block.

## Target Assistive Technology
- VoiceOver on macOS and iOS
- Safari as the paired browser for VoiceOver testing

---

## Semantic HTML

- Use correct landmark elements: `<main>`, `<nav>`, `<header>`, `<footer>`, `<section>`, `<article>`, `<aside>`
- Every page must have exactly one `<main>` landmark
- `<header>` and `<footer>` are landmark elements only when outside `<article>` or `<section>`
- Use `<button>` for actions, `<a>` for navigation — never a `<div>` or `<span>` as an interactive element
- Use `<ul>` or `<ol>` for lists of items, never styled `<div>` groups
- Prefer native HTML elements over ARIA equivalents when available
- Never use a table for layout — only for tabular data

## Heading Hierarchy
- Every page must have exactly one `<h1>`
- Heading levels must be sequential — never skip levels (h1 → h3 is a violation)
- Headings must describe the content that follows them
- Never use a heading purely for visual size — use CSS instead

## Images and Media
- All meaningful images must have descriptive `alt` text
- Decorative images must have `alt=""`
- SVGs used as icons must have `aria-hidden="true"` when decorative
- SVGs conveying meaning must have a `<title>` element or `aria-label`
- Never use `alt` as a tooltip replacement

## Links and Buttons
- Link text must describe the destination — never "click here", "read more", or "learn more"
- Links that open in a new tab must warn the user visually or via `sr-only` text
- Buttons must have an accessible name via text content, `aria-label`, or `aria-labelledby`
- Icon-only buttons must have an `aria-label`
- Adjacent links to the same destination should be combined into one

---

## ARIA

- Never use ARIA to override semantics that native HTML already provides
- Never use redundant ARIA — `role="button"` on a `<button>` is a violation
- All `aria-labelledby` and `aria-describedby` values must reference an element that exists in the DOM
- `aria-hidden="true"` must never be applied to focusable elements
- `aria-hidden="true"` must never be applied to the `<body>` element
- ARIA states must update dynamically with interactions (`aria-expanded`, `aria-selected`, `aria-checked`)
- Required ARIA attributes must be present for every role that demands them

## Live Regions
- Use `aria-live="polite"` for routine dynamic updates (search results, filter counts)
- Use `aria-live="assertive"` only for critical alerts that require immediate attention
- Live regions must exist in the DOM before content is injected into them
- Never debounce live region updates so fast that announcements are skipped
- Loading states longer than 2 seconds must be announced

---

## Forms

- Every input must have a programmatically associated label via `<label for>`, `aria-label`, or `aria-labelledby`
- Never rely on placeholder text as the only label — placeholder disappears on input
- Required fields must use the `required` attribute and be indicated visually
- Error messages must be associated with their input via `aria-describedby`
- `aria-invalid="true"` must be set on fields with validation errors
- On invalid form submission, focus must move to the first error
- Radio and checkbox groups must use `<fieldset>` and `<legend>`
- Identity fields must have appropriate `autocomplete` attributes

---

## Keyboard Navigation

- All interactive elements must be reachable via keyboard alone
- Tab order must follow the visual reading order of the page
- Never use positive `tabindex` values
- `tabindex="0"` to include a non-interactive element in tab order only when necessary
- Every interactive element must have a visible focus indicator
- Never use `outline: none` without replacing it with a visible alternative
- Focus must never be trapped outside of a modal or dialog
- Escape key must close any open modal, dialog, popover, or overlay
- Custom widgets must follow WAI-ARIA keyboard interaction patterns

## Focus Management
- When a modal opens, focus must move inside it
- When a modal closes, focus must return to the element that triggered it
- When content is deleted, focus must move to a logical nearby element
- SPA route changes must move focus to the new page heading or main landmark
- `sr-only` class must be used for visually hidden but screen-reader-accessible text
- Content visually covered by an open overlay, modal, or full-screen panel must be excluded from focus and the accessibility tree using `inert`, not `display: none` — the covered content is already visually hidden by the overlay itself, so `display: none` only adds unnecessary layout reflow without providing any additional benefit. Reserve `display: none` (or a framework's hidden/show mechanism) for elements that are not already visually covered by something else.

---

## Color and Contrast

- Normal text must meet 4.5:1 contrast ratio (WCAG AA)
- Large text (18pt or 14pt bold) must meet 3:1 contrast ratio (WCAG AA)
- UI components and focus indicators must meet 3:1 contrast ratio
- Never convey information through color alone — always pair with text, icon, or pattern
- Disabled element contrast is exempt from requirements but should still be reasonable

---

## Framework-Specific Gotchas

Accessibility itself is platform-agnostic — a missing label or an unreachable button is the same violation no matter what generated the HTML. This section is not a second set of a11y rules. It exists because each framework has its own ways of *accidentally* producing the violations above — through syntax, rendering behavior, or reconciliation — that are easy to miss if you're only checking the rendered output for compliance. Only frameworks actually in use on this project are listed below. Add a new subsection the first time a project uses a framework not yet covered here, rather than writing speculative rules in advance.

### React and TypeScript
- `aria-*` props use hyphenated syntax in JSX — `aria-label` not `ariaLabel`
- Verify `useEffect` cleanup handles focus management on component unmount
- `React.Fragment` must not break landmark structure
- Never use `dangerouslySetInnerHTML` without considering ARIA implications
- React portals must maintain focus trap context when used for modals
- List `key` props must not cause focus loss on re-render
- Tailwind: `sr-only` class is the approved pattern for visually hidden text
- Tailwind: `focus-visible:ring-*` is the approved pattern for focus indicators
- Tailwind: never use `outline-none` without a `focus-visible:ring-*` replacement
- Tailwind: check `text-*` on `bg-*` combinations for contrast compliance
- Tailwind: `dark:` variants must maintain contrast ratios in dark mode

### Astro
- Astro ships zero client-side JS by default. A component can render fully correct static markup (`aria-expanded="false"`, etc.) that silently never updates, if the `<script>` tag that's supposed to wire up the interaction is missing, broken, or scoped incorrectly. Verify dynamic ARIA states actually have working JS behind them — don't assume correct markup implies correct behavior.
- Framework UI components dropped into an `.astro` page (React/Vue/Svelte islands) require an explicit `client:*` directive (`client:load`, `client:visible`, etc.) to hydrate. A missing directive means the component renders its initial HTML only and never becomes interactive — including any focus/ARIA-state logic inside it.
- A plain `<script>` tag at the bottom of an `.astro` file runs automatically as real client-side JS — no directive needed. This is the correct, lower-overhead choice for simple DOM interactions (toggles, class swaps) that don't need a full framework component.
- Tailwind: `sr-only` class is the approved pattern for visually hidden text
- Tailwind: `focus-visible:ring-*` is the approved pattern for focus indicators
- Tailwind: never use `outline-none` without a `focus-visible:ring-*` replacement
- Tailwind: check `text-*` on `bg-*` combinations for contrast compliance
- Tailwind: `dark:` variants must maintain contrast ratios in dark mode

---

## Severity Classification

### Critical (must fix before merge)
- Missing form labels
- Keyboard traps outside modals
- Missing alt text on meaningful images
- Focus indicators completely absent
- Interactive elements unreachable by keyboard

### Serious (must fix before release)
- Incorrect heading hierarchy
- Missing ARIA states on dynamic components
- Color alone conveying information
- Contrast failures on body text

### Moderate (should fix, report clearly)
- Ambiguous link text
- Missing autocomplete on identity fields
- Live regions not announcing dynamic content
- Missing landmarks

### Minor (flag as improvement)
- Redundant ARIA
- Non-critical AAA opportunities
- VoiceOver announcement phrasing improvements
