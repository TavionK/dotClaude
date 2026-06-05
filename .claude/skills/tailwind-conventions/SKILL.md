# Tailwind Conventions

## Version
Tailwind CSS v4. All configuration lives in CSS via `@theme` and `@layer`.
There is no `tailwind.config.js` unless explicitly present in the project.

---

## CSS Variables and Theming

- All colors must use CSS variables defined in `@theme` — never hardcoded hex, rgb, or hsl values
- CSS variables are used as Tailwind utilities directly — `bg-primary` not `bg-[#4F46E5]`
- New colors are added to the `@theme` block in the main CSS file, then used as utilities
- Never use arbitrary color values when a CSS variable exists for that color

```css
/* Correct */
@theme {
  --color-primary: #4F46E5;
  --color-primary-hover: #4338CA;
}

/* Usage */
<div className="bg-primary hover:bg-primary-hover">
```

---

## Arbitrary Values

- Only use arbitrary values when no Tailwind scale equivalent exists
- If a scale value clearly covers the need, use it — `w-8` not `w-[32px]`
- Arbitrary values are acceptable for truly custom measurements with no scale equivalent
- Never use arbitrary values for colors — always use CSS variables instead

---

## Focus Indicators

The project uses a consistent `a11y-rings` focus pattern. Never remove focus indicators without replacing them.

- Never use `outline-none` alone — always pair with `focus-visible:ring-*`
- The base ring pattern is:
```css
@apply focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-4;
```
- Ring color varies by context — match the accent or button color for that element
- The ring offset and width stay consistent across the project
- When building interactive elements, always include the full ring pattern with an appropriate color

---

## Class Extraction

Extract repeated inline style combinations into reusable classes when:
- A combination of 3 or more utilities appears 3 or more times across the project
- The combination represents a distinct UI concept (a button, a card, an input)

Extracted classes live in `@layer components` in the appropriate CSS file:

```css
@layer components {
  .btn {
    @apply px-4 py-2 rounded-md font-medium transition-colors;
  }

  .card {
    @apply rounded-lg border border-neutral-200 bg-white p-4 shadow-sm;
  }
}
```

### Naming Conventions
- Use descriptive kebab-case names that describe the UI concept — `.btn`, `.card`, `.input`, `.badge`, `.btn-primary`
- No project or framework prefixes — just descriptive names
- Names should be immediately obvious from the class name alone
- Variants use a hyphen suffix — `.btn-primary`, `.btn-ghost`, `.card-flat`

---

## Animations and Motion

- Any animated element must include a `motion-reduce:` variant
- Never animate without considering users who prefer reduced motion

```tsx
<div className="animate-fade-in motion-reduce:animate-none">
```

---

## Screen Reader Utilities

- Use `sr-only` for text that should be announced by screen readers but not visible
- Common uses: icon button labels, skip links, supplementary context
- Never use `sr-only` on interactive elements that need to be focusable — use `not-sr-only` to reverse it when needed

```tsx
<button aria-label="Delete task">
  <Trash2 size={16} aria-hidden="true" />
  <span className="sr-only">Delete task</span>
</button>
```

---

## Inline Styles

- Never use the `style` prop for values that Tailwind can handle
- Inline styles are only acceptable for truly dynamic values that cannot be expressed as utilities (e.g. a width derived from a JS calculation)

```tsx
// Wrong
<div style={{ padding: '16px', borderRadius: '8px' }}>

// Correct
<div className="p-4 rounded-lg">

// Acceptable inline style — dynamic value
<div style={{ width: `${progress}%` }} className="h-2 bg-primary rounded-full">
```

---

## What the Tailwind Checker Should Flag

| Issue | Severity |
|-------|----------|
| Hardcoded color value | Serious |
| `outline-none` without `focus-visible:ring-*` | Serious |
| Arbitrary value where scale exists | Moderate |
| Inline style where Tailwind utility exists | Moderate |
| Missing `motion-reduce:` on animation | Moderate |
| `sr-only` used incorrectly | Moderate |
| Repeated combination ready for extraction | Info |
