# PR conventions

## Branch flow
- Feature branches → `dev` or `main` (ask if not specified)
- `dev` or `main` → `prod` for release PRs

When the user doesn't specify a target branch, ask before writing the PR:
"Is this going into `dev` or `main`?"

---

## PR title format
Follow conventional commits format:

```plaintext
<type>: <short description>
```

### Types
- `feat:` — new feature
- `fix:` — bug fix
- `chore:` — maintenance, dependencies, config
- `refactor:` — code change that neither fixes a bug nor adds a feature
- `style:` — formatting, missing semicolons, whitespace
- `a11y:` — accessibility improvements
- `test:` — adding or updating tests
- `docs:` — documentation changes

### Title rules
- Lowercase after the type prefix
- No period at the end
- 72 characters max
- Imperative mood — "add button component" not "added button component"

### Examples
```plaintext
feat: add player search to euroleague stats app
fix: resolve focus trap in delete confirmation modal
a11y: add aria-labels to icon-only buttons in task list
chore: update radix primitives to latest version
refactor: extract pir calculation into utility function
```

---

## PR description structure

```plaintext
## What this PR does
[1-3 sentences describing what changed and why]

## Changes
- [Specific change]
- [Specific change]
- [Specific change]

## How to test
- [Step to verify the change works]
- [Step to verify edge cases]

## Accessibility
- [What was checked or any a11y considerations]
- [If no a11y impact, write "No accessibility impact"]

## Issues
Closes #[issue number]
```

### Section rules
- **What this PR does** — plain English, no jargon, one paragraph max
- **Changes** — specific and scannable, not vague. "Add `aria-label` to delete button in `TaskItem.tsx`" not "fix accessibility issues"
- **How to test** — concrete steps someone could follow to verify the PR works
- **Accessibility** — always present, never omit. Reflects your standards
- **Issues** — only include if there is a linked issue. Use `Closes` if the PR fully resolves it, `Relates to` if it's partial

---

## Release PRs (dev/main → prod)

Release PRs follow a slightly different format:

```plaintext
## Release [version or date]

## What's included
- [Summary of features/fixes since last release]
- [Summary of features/fixes since last release]

## Testing
- [ ] Tested on desktop
- [ ] Tested on mobile
- [ ] Accessibility checked
- [ ] No console errors

## Issues closed
Closes #[n], #[n], #[n]
```

---

## Tone and length
- Professional but not formal — write like a developer talking to another developer
- Concise — no filler sentences
- Every section should earn its place — if there's nothing meaningful to say in a section, say so briefly rather than padding it out
