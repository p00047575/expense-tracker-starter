# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Expense/finance tracker starter app for a Claude Code course (codewithmosh.com). Per the README, it **intentionally ships with a bug, poor UI, and messy code** that get fixed throughout the course — so existing flaws are expected, not accidental. Don't assume the current state is "correct."

## Commands

```bash
npm install      # required before first run; vite is not committed
npm run dev      # Vite dev server at http://localhost:5173
npm run build    # production build to dist/
npm run preview  # serve the production build
npm run lint     # ESLint over the repo
```

There is no test runner configured.

## Architecture

A single-page React 19 app bootstrapped by Vite. `src/main.jsx` is the entry point and mounts `<App />` in `<StrictMode>`. The UI is split into `App` plus three child components, each in its own file under `src/`:

- `src/App.jsx` — owns the shared `transactions` data and the `categories` list in local `useState` (no backend, no persistence, no router, no state library). The seed `transactions` array is the source of truth; `addTransaction` appends to it and `deleteTransaction` removes one by `id` (filtering it out of state). `App` composes the children and passes data/callbacks (`onAdd`, `onDelete`) down. Reloading the page resets everything to the seed data.
- `src/Summary.jsx` — receives `transactions` and derives `totalIncome`/`totalExpenses`/`balance` on each render.
- `src/TransactionForm.jsx` — owns its own form-field state (`description`, `amount`, `type`, `category`); on submit it builds a transaction (coercing `amount` to a number) and calls the `onAdd` callback, then resets its fields.
- `src/TransactionList.jsx` — owns its own `filterType`/`filterCategory` state and renders the filtered table from the `transactions` prop. Each row has a Delete button that prompts via `window.confirm` and, on confirm, calls the `onDelete` callback with the transaction's `id`.
- `src/SpendingChart.jsx` — receives `transactions`, filters to expenses, groups amounts by category, and renders a Recharts `BarChart` (X: category, Y: dollar amount). Returns `null` when there are no expenses. No local state.

Each child manages its own local UI state, so `App` is just data + composition. Lifted/shared state lives in `App`; transient form and filter state stays local to the component that uses it.

### `amount` is numeric

Transaction `amount` is a **number** everywhere: numeric literals in the seed data, and `Number(amount)` coercion in `TransactionForm` before a new transaction is added. `Summary`'s reducers also wrap with `Number()` defensively. (This fixes the original course bug where `amount` was a string and the totals concatenated instead of summing — keep amounts numeric when adding new data or fields.)

## Conventions

- ESLint flat config (`eslint.config.js`); `dist` is ignored. The `no-unused-vars` rule ignores identifiers matching `^[A-Z_]` (constants/components).
- Plain JavaScript + JSX (`.jsx`), ES modules, no TypeScript despite `@types/react` being present.
- Styling is plain CSS: `src/App.css` (component) and `src/index.css` (global).
- Recharts is installed for the spending bar chart (`recharts` package).
