# Engineering standards

## Framework detection
Before applying any standards, identify the project framework from `package.json` and `claude.md`:

- **React + Vite** — follow Vite + React project conventions
- **Next.js** — follow Next.js App Router conventions (app/, not pages/)
- **Other** — check `claude.md` for project-specific guidance

Follow the framework's own folder conventions. Never impose a fixed folder structure that conflicts with how the framework expects things organized.

---

## TypeScript

- Always run with strict mode — assume `"strict": true` in `tsconfig.json`
- No `any` types — ever. Use `unknown` and narrow it, or define a proper type
- No type assertions (`as SomeType`) unless absolutely unavoidable — if used, add a comment explaining why
- Explicit return types on all functions and components
- Prefer `interface` over `type` for object shapes — use `type` for unions, intersections, and primitives
- Never use `// @ts-ignore` or `// @ts-expect-error` without a comment explaining the reason

### Naming
- Interfaces: PascalCase prefixed with nothing — `ButtonProps` not `IButtonProps`
- Types: PascalCase — `ButtonVariant`
- Enums: PascalCase — `UserRole`
- Generic type parameters: single uppercase letter or descriptive PascalCase — `T`, `TData`, `TResponse`

### Props interfaces
- Every component must have an explicitly named props interface
- Named `[ComponentName]Props` — `ButtonProps`, `CardProps`
- Export the props interface as a named export if consumers need to reference the type
- Extend native HTML element props when the component wraps one:

```ts
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'ghost'
  isLoading?: boolean
}
```

---

## Components

- Functional components only — never class components
- `export default function` — always, never a named export for the component itself
- Export the props interface as a named export if consumers need to reference the type
- One component per file — never define multiple exported components in the same file
- Keep components focused on one responsibility — if it's doing too much, split it
- Extract logic into custom hooks when state or effects get complex
- Helper functions defined inside the component body using `function` declarations, not arrow functions
- Explicit return type on the component function — `React.JSX.Element`

### Pages vs components
- **Pages / views** — top level screen components. Own the layout, compose smaller components, not reused elsewhere
- **Components** — reusable, focused pieces that don't know what page they're on
- Never use Radix primitives directly in a page — always wrap them in your own component first

### className prop
- Only add `className` to a component's props if consumers genuinely need to customize it from outside
- If `className` is accepted, always use `cn` from `clsx` + `tailwind-merge` internally to handle conflicts:

```tsx
import { cn } from '@/lib/utils'

export default function Card({ className }: CardProps): React.JSX.Element {
  return (
    <div className={cn('base-styles', className)}>
  )
}
```

- If no `className` prop is needed, the component is fully self-contained — do not add it by default

### Component structure
Keep this order consistently across all component files, using separator comments to divide sections:

```tsx
import { useState } from 'react'
import { Plus } from 'lucide-react'
import CourseListItem from './CourseListItem'

// ---------------------------------------------------------------------------
// Types
// ---------------------------------------------------------------------------

interface CourseListProps {
  courses: Course[]
  setCourses: (courses: Course[]) => void
}

// ---------------------------------------------------------------------------
// Component
// ---------------------------------------------------------------------------

export default function CourseList({ courses, setCourses }: CourseListProps): React.JSX.Element {
  function handleAddCourse(): void {
    setCourses([...courses, { id: crypto.randomUUID() }])
  }

  return (
    <section>
      ...
    </section>
  )
}
```

---

## Hooks

- File name: camelCase with `use` prefix — `useTaskList.ts`
- Function name matches file name exactly
- Always return a named object, not an array (except for simple value/setter pairs like `useState`)
- Explicitly type the return value

```ts
interface UseTaskListReturn {
  tasks: Task[]
  addTask: (label: string) => void
  deleteTask: (id: string) => void
  isLoading: boolean
}

export function useTaskList(): UseTaskListReturn {
  ...
}
```

---

## Utilities

- File name: camelCase — `formatDate.ts`
- Pure functions only — no side effects
- Explicitly typed parameters and return values
- Export named functions — never a default export from a utility file
- One logical concern per utility file

```ts
export function formatDate(date: Date): string {
  return date.toLocaleDateString('en-US', {
    month: 'short',
    day: 'numeric',
    year: 'numeric',
  })
}
```

---

## Imports

- Import directly from the source file — no barrel files or index re-exports
- Group imports in this order, separated by a blank line:
  1. React and framework imports
  2. Third-party library imports
  3. Internal imports (components, hooks, utils)
  4. Type-only imports (`import type { ... }`)
- Use `import type` for type-only imports

```tsx
import { useState, useEffect } from 'react'

import * as Dialog from '@radix-ui/react-dialog'
import { Trash2 } from 'lucide-react'

import TaskItem from '@/components/TaskItem'
import { useTaskList } from '@/hooks/useTaskList'

import type { Task } from '@/types/task'
```

---

## File naming

| Type | Convention | Example |
|---|---|---|
| Component | PascalCase | `TaskItem.tsx` |
| Page | PascalCase | `Dashboard.tsx` |
| Hook | camelCase with `use` | `useTaskList.ts` |
| Utility | camelCase | `formatDate.ts` |
| Type file | camelCase | `task.ts` |
| Constant file | camelCase | `routes.ts` |

---

## Stack defaults

Unless `claude.md` specifies otherwise, assume this stack:

- **React** with TypeScript
- **Tailwind CSS v4** — see tailwind-conventions skill
- **Radix Primitives** for all interactive components (dialog, dropdown, checkbox, tooltip, etc.)
- **Lucide React** for icons
- **Vite** as the build tool unless Next.js is detected

### cn utility
Use `cn` from `clsx` + `tailwind-merge` when a component accepts a `className` prop:

```bash
npm install clsx tailwind-merge
```

```ts
// src/lib/utils.ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]): string {
  return twMerge(clsx(inputs))
}
```

### Radix usage
Never use a Radix primitive directly in a page or view — always wrap it in your own component first. Never build a custom implementation of something Radix already provides:

| Need | Use |
|---|---|
| Modal / dialog | `@radix-ui/react-dialog` |
| Dropdown menu | `@radix-ui/react-dropdown-menu` |
| Checkbox | `@radix-ui/react-checkbox` |
| Tooltip | `@radix-ui/react-tooltip` |
| Select | `@radix-ui/react-select` |
| Accordion | `@radix-ui/react-accordion` |
| Popover | `@radix-ui/react-popover` |
| Tabs | `@radix-ui/react-tabs` |

### Lucide usage
- Always pass `aria-hidden="true"` on decorative icons
- Always pair icon-only buttons with `aria-label`
- Use the `size` prop for consistent sizing — never CSS width/height on the icon itself

---

## What not to do

- Never use `any`
- Never use class components
- Never use named exports for components — always `export default function`
- Never define multiple exported components in one file
- Never use barrel files — import directly from the source
- Never use Radix primitives directly in pages — wrap them first
- Never build custom implementations of Radix primitives
- Never omit the props interface
- Never use default exports from utility files
- Never mix concerns — one file, one responsibility
- Never use arrow functions for component-internal helper functions