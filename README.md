# tailwindcss-development

An agent skill for writing and maintaining Tailwind CSS in **Vite + React** projects.

## What it does

Guides AI agents (Claude Code, Copilot, Codex, etc.) on how to correctly use Tailwind CSS in a React/Vite environment. Covers:

- **Project setup** — Tailwind v4 (CSS-first, no config file) and v3 (PostCSS)
- **Dark mode** — OS-preference and manual class toggle strategies
- **Responsive design** — mobile-first breakpoints and custom breakpoint tokens
- **Layout patterns** — flex, grid, app shell, fixed topbar
- **Component patterns** — `clsx`, `tailwind-merge`, `cva` (class-variance-authority)
- **Animations** — built-in transitions and custom keyframes
- **Arbitrary values & CSS variables**
- **Accessibility** — focus rings, screen reader utilities, reduced motion
- **Common pitfalls** — dynamic class names, v3/v4 migration traps

## Installation

```bash
npx skills add Eren-Seyfi/tailwindcss-development --skill tailwindcss-development
```

## Usage

Once installed, the skill activates automatically when you ask your agent anything related to Tailwind CSS — layout, styling, dark mode, component variants, etc.

## Structure

```
tailwindcss-development/
└── SKILL.md
```

## License

MIT
