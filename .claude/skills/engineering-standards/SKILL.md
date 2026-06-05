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
- Export the props interface alongside the component so consumers can use it
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
- Default export for the component
- Named export for the props interface
- One component per file — never define multiple exported components in the same file
- Keep components focused on one responsibility — if it's doing too much, split it
- Extract logic into custom hooks when state or effects get complex

### Component structure order
Keep this order consistent across all component files:

1. Imports
2. Types and interfaces
3. Constants (if any)
4. Component function
5. Helper functions used only by this component
6. Default export

### Example structure
```tsx
import { useState } from 'react'
import { Trash2 } from 'lucide-react'

export interface TaskItemProps {
  id: string
  label: string
  onDelete: (id: string) => void
}

export default function TaskItem({ id, label, onDelete }: TaskItemProps): JSX.Element {
  const [isDeleting, setIsDeleting] = useState(false)

  function handleDelete(): void {
    setIsDeleting(true)
    onDelete(id)
  }

  return (
    <div className="card">
      <span>{label}</span>
      <button
        onClick={handleDelete}
        aria-label={`Delete ${label}`}
        className="outline-none focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:ring-primary"
      >
        <Trash2 size={16} aria-hidden="true" />
      </button>
    </div>
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

### Radix usage
Never build a custom implementation of something Radix already provides. Always reach for the Radix primitive first:

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
- Never define multiple exported components in one file
- Never use barrel files — import directly from the source
- Never build custom implementations of Radix primitives
- Never omit the props interface
- Never use default exports from utility files
- Never mix concerns — one file, one responsibility
