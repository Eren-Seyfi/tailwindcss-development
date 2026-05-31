---
name: tailwindcss-development
description: "Always invoke when the user's message includes 'tailwind' in any form. Also invoke for: building responsive grid layouts (multi-column card grids, product grids), flex/grid page structures (dashboards with sidebars, fixed topbars, mobile-toggle navs), styling UI components (cards, tables, navbars, pricing sections, forms, inputs, badges), adding dark mode variants, fixing spacing or typography, Tailwind v3/v4 work, and any React + Vite + Tailwind setup or configuration. The core use case: writing or fixing Tailwind utility classes in JSX/TSX files. Skip for backend logic, database queries, API routes, JavaScript with no HTML/CSS component, CSS file audits unrelated to Tailwind, and vanilla CSS."
license: MIT
metadata:
  author: custom
---

# Tailwind CSS Development (Vite + React)

## Stack Assumptions

This skill targets **Vite + React** projects (JS or TS). There is no Laravel, Blade, or PHP involved. Components are `.jsx` / `.tsx` files. Always follow the existing project conventions before introducing new patterns.

---

## Project Setup

### Installation (Tailwind v4 — current)

```bash
npm install tailwindcss @tailwindcss/vite
```

`vite.config.ts`

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

`src/index.css` (entry CSS — no config file needed)

```css
@import "tailwindcss";
```

Import in `src/main.tsx`:

```tsx
import "./index.css";
```

### Installation (Tailwind v3 — legacy projects)

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

`tailwind.config.js`

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: { extend: {} },
  plugins: [],
};
```

`src/index.css`

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## Tailwind v4 Specifics

### CSS-First Configuration

No `tailwind.config.js` in v4. Use `@theme` in CSS:

```css
@import "tailwindcss";

@theme {
  --color-brand: oklch(0.72 0.11 178);
  --color-brand-dark: oklch(0.55 0.14 178);
  --font-sans: "Inter Variable", sans-serif;
  --radius-card: 1rem;
}
```

Custom tokens are available as utilities automatically: `bg-brand`, `text-brand-dark`, `rounded-card`, etc.

### Replaced / Deprecated Utilities

| Deprecated (v3)     | Replacement (v4)       |
| ------------------- | ---------------------- |
| `bg-opacity-*`      | `bg-black/*`           |
| `text-opacity-*`    | `text-black/*`         |
| `border-opacity-*`  | `border-black/*`       |
| `flex-shrink-*`     | `shrink-*`             |
| `flex-grow-*`       | `grow-*`               |
| `overflow-ellipsis` | `text-ellipsis`        |
| `decoration-slice`  | `box-decoration-slice` |
| `decoration-clone`  | `box-decoration-clone` |

---

## Dark Mode

### v4 (automatic via `prefers-color-scheme`)

```css
@import "tailwindcss";
/* dark: variant responds to OS preference by default */
```

### v4 (manual class toggle)

```css
@import "tailwindcss";

@variant dark (&:where(.dark, .dark *));
```

Toggle by adding/removing `dark` class on `<html>`:

```tsx
document.documentElement.classList.toggle("dark");
```

### v3 (class strategy)

```js
// tailwind.config.js
export default {
  darkMode: "class",
  // ...
};
```

### Usage in JSX

```tsx
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  Content adapts to color scheme
</div>
```

If the project supports dark mode, all new components must support it consistently.

---

## Responsive Design

Use mobile-first breakpoint prefixes. Default breakpoints:

| Prefix | Min-width |
| ------ | --------- |
| `sm`   | 640px     |
| `md`   | 768px     |
| `lg`   | 1024px    |
| `xl`   | 1280px    |
| `2xl`  | 1536px    |

```tsx
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
  <Card />
  <Card />
  <Card />
</div>
```

Custom breakpoints in v4:

```css
@theme {
  --breakpoint-xs: 480px;
  --breakpoint-3xl: 1800px;
}
```

---

## Layout Patterns

### Flex

```tsx
<div className="flex items-center justify-between gap-4">
  <span>Left</span>
  <span>Right</span>
</div>
```

### Grid

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map((item) => (
    <Card key={item.id} {...item} />
  ))}
</div>
```

### App Shell (sidebar + content)

```tsx
<div className="flex h-screen overflow-hidden">
  <aside className="w-64 shrink-0 border-r bg-white dark:bg-gray-900 overflow-y-auto">
    <Sidebar />
  </aside>
  <main className="flex-1 overflow-y-auto p-6">
    <Outlet />
  </main>
</div>
```

### Fixed Topbar Layout

```tsx
<div className="flex flex-col h-screen">
  <header className="h-16 shrink-0 border-b flex items-center px-6">
    <Topbar />
  </header>
  <main className="flex-1 overflow-y-auto p-6">
    <Outlet />
  </main>
</div>
```

---

## Spacing

Prefer `gap` over margins between siblings:

```tsx
// ✅ Good
<div className="flex gap-4">
  <Button>Cancel</Button>
  <Button>Save</Button>
</div>

// ❌ Avoid
<div className="flex">
  <Button className="mr-4">Cancel</Button>
  <Button>Save</Button>
</div>
```

---

## Component Patterns in JSX/TSX

### Composing Classes with `clsx` / `cn`

Install:

```bash
npm install clsx
# with tailwind-merge for conflict resolution:
npm install tailwind-merge
```

Utility helper (`src/lib/utils.ts`):

```ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

Usage:

```tsx
<button
  className={cn(
    "px-4 py-2 rounded-lg font-medium transition-colors",
    variant === "primary" && "bg-blue-600 text-white hover:bg-blue-700",
    variant === "ghost" && "bg-transparent text-gray-700 hover:bg-gray-100",
    disabled && "opacity-50 cursor-not-allowed",
    className,
  )}
>
  {children}
</button>
```

### Variant-Based Components with `cva`

```bash
npm install class-variance-authority
```

```tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-lg font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:opacity-50",
  {
    variants: {
      variant: {
        primary: "bg-blue-600 text-white hover:bg-blue-700",
        secondary: "bg-gray-100 text-gray-900 hover:bg-gray-200",
        destructive: "bg-red-600 text-white hover:bg-red-700",
        ghost: "hover:bg-gray-100 text-gray-700",
        outline: "border border-gray-300 hover:bg-gray-50",
      },
      size: {
        sm: "h-8 px-3 text-sm",
        md: "h-10 px-4 text-sm",
        lg: "h-12 px-6 text-base",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
    },
  },
);

interface ButtonProps
  extends
    React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ variant, size, className, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

---

## Animations & Transitions

### Built-in Tailwind Transitions

```tsx
<button className="transition-colors duration-200 hover:bg-blue-700 active:scale-95">
  Click me
</button>
```

### Custom Keyframes (v4)

```css
@theme {
  --animate-fade-in: fade-in 0.3s ease-out;
  --animate-slide-up: slide-up 0.4s ease-out;
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slide-up {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

```tsx
<div className="animate-fade-in">...</div>
```

### Custom Keyframes (v3)

```js
// tailwind.config.js
theme: {
  extend: {
    keyframes: {
      'fade-in': {
        from: { opacity: '0' },
        to:   { opacity: '1' },
      },
    },
    animation: {
      'fade-in': 'fade-in 0.3s ease-out',
    },
  },
}
```

---

## Arbitrary Values & CSS Variables

```tsx
// Arbitrary value
<div className="w-[340px] top-[117px] bg-[#1a1a2e]" />

// CSS variable
<div className="bg-[--color-brand] text-[--font-size-hero]" />

// Arbitrary property (escape hatch)
<div className="[mask-image:linear-gradient(to_bottom,black,transparent)]" />
```

---

## Accessibility

```tsx
// Focus rings
<button className="focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2">

// Screen reader only
<span className="sr-only">Close menu</span>

// Reduced motion
<div className="transition-transform motion-reduce:transition-none">
```

---

## Common Pitfalls

- Dynamically constructing class names from strings (Tailwind purges unused classes). Use full class strings or `cva` variants instead.
- Using v3 utilities in a v4 project (`bg-opacity-*`, `flex-shrink-*`, `@tailwind` directives).
- Using `tailwind.config.js` in a v4 project — use `@theme` in CSS instead.
- Forgetting `tailwind-merge` when accepting an external `className` prop — conflicts won't resolve without it.
- Using margins between siblings instead of `gap`.
- Forgetting `dark:` variants when the project supports dark mode.
- Not adding new content paths to `content` array in v3 config when adding new directories.
