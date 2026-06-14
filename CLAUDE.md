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

A single-page React 19 app bootstrapped by Vite. The entire application is one component:

- `src/main.jsx` — entry point, mounts `<App />` in `<StrictMode>`.
- `src/App.jsx` — the whole app. All state, business logic, and markup live here.

`App.jsx` holds all data in local `useState` (no backend, no persistence, no router, no state library). The seed `transactions` array is the source of truth; adding a transaction appends to it. Income/expense totals and the balance are derived inline on each render, and filtered views are computed from `filterType`/`filterCategory`. Reloading the page resets everything to the seed data.

### Known intentional bug

In `App.jsx`, transaction `amount` is stored as a **string** (both seed data and the add-transaction form). The `totalIncome`/`totalExpenses` reducers do `sum + t.amount`, which concatenates strings instead of adding numbers. Any fix to the totals must also account for the string-typed `amount` field flowing in from the form.

## Conventions

- ESLint flat config (`eslint.config.js`); `dist` is ignored. The `no-unused-vars` rule ignores identifiers matching `^[A-Z_]` (constants/components).
- Plain JavaScript + JSX (`.jsx`), ES modules, no TypeScript despite `@types/react` being present.
- Styling is plain CSS: `src/App.css` (component) and `src/index.css` (global).
