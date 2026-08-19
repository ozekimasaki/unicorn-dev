# AGENTS.md

Guidance for coding agents working in the `unicorn-dev` repository.

## Overview

Full-stack app based on the Cloudflare Vite React template:

- **Frontend:** React 19 + TypeScript, built with Vite 6 (`src/react-app`).
- **Backend:** A [Hono](https://hono.dev/) app running as a Cloudflare Worker (`src/worker/index.ts`), exposing a JSON API under `/api/`.
- **Tooling:** The `@cloudflare/vite-plugin` integrates the Worker into the Vite dev server and build. Deployment uses Wrangler.

## Project Structure & Entry Points

- `index.html` — HTML entry. Note: it currently embeds a [UnicornStudio](https://www.unicorn.studio/) scene via an inline `<script>` and does **not** include a `#root` element or a `<script type="module">` importing `src/react-app/main.tsx`. As a result, the React app is not mounted by the current `index.html`. Keep this in mind before assuming UI changes in `src/react-app` are visible in the served page; wire up `main.tsx` in `index.html` if React rendering is required.
- `src/react-app/main.tsx` — React entry point (`createRoot` on `#root`).
- `src/react-app/App.tsx` — Root component (a counter and a button that fetches `/api/`).
- `src/worker/index.ts` — Hono app / Worker entry. `app.get("/api/", ...)` returns `{ name: "Cloudflare" }`.
- `vite.config.ts` — Vite config with the React and Cloudflare plugins.
- `wrangler.json` — Cloudflare Workers configuration (name, main entry, compatibility flags, SPA asset handling, observability).
- `worker-configuration.d.ts` — Generated Worker binding types (do not edit by hand; regenerate with `npm run cf-typegen`).
- `eslint.config.js` — ESLint flat config.
- `tsconfig.json` — Root project-references config pointing at `tsconfig.app.json`, `tsconfig.node.json`, and `tsconfig.worker.json`.

## Setup

```bash
npm install
```

Requires Node.js (developed against Node.js 20) and npm.

## Build / Test / Lint / Typecheck Commands

All are defined in `package.json`:

- **Dev server:** `npm run dev` (Vite with HMR).
- **Build:** `npm run build` — runs `tsc -b` (type-check across project references) then `vite build`.
- **Type-check:** There is no standalone `typecheck` script. Type-checking happens via `tsc -b` inside `npm run build`, or run `npx tsc -b` directly. `npm run check` runs `tsc && vite build && wrangler deploy --dry-run`.
- **Lint:** `npm run lint` (`eslint .`).
- **Preview:** `npm run preview` (builds, then `vite preview`).
- **Type generation:** `npm run cf-typegen` regenerates `worker-configuration.d.ts` via `wrangler types`.
- **Deploy:** `npm run deploy` (`wrangler deploy`).

There is **no test framework or test script** configured in this repository. Do not invent test commands; if tests are needed, add a framework (e.g. Vitest) explicitly.

Before finishing a change, run `npm run lint` and `npm run build` (which includes type-checking) and ensure both pass.

## Coding Conventions

- **Language:** TypeScript with `strict` mode enabled (see `tsconfig.*.json`). `noUnusedLocals`, `noUnusedParameters`, and `noFallthroughCasesInSwitch` are on, so avoid unused variables/parameters.
- **Modules:** ESM only (`"type": "module"` in `package.json`).
- **React:** Function components with hooks; `react-jsx` runtime (no need to import `React`). Follow the `eslint-plugin-react-hooks` rules; `react-refresh/only-export-components` is enabled as a warning.
- **Formatting:** Match the existing style — two-space indentation, double quotes, and semicolons.
- **Imports:** Keep imports at the top of files. Frontend code imports relatively within `src/react-app`; assets (SVGs, CSS) are imported directly.

## Notes & Gotchas

- Keep the React app (`src/react-app`) and the Worker (`src/worker`) in their respective TypeScript project references; the Worker uses `tsconfig.worker.json` with `worker-configuration.d.ts` types, the app uses `tsconfig.app.json` with DOM types.
- The Worker's `Env` interface comes from the generated `worker-configuration.d.ts`. After changing bindings in `wrangler.json`, run `npm run cf-typegen`.
- `wrangler.json` uses `not_found_handling: "single-page-application"` and serves built assets from `./dist/client`.
- Deployment (`npm run deploy`) requires valid Cloudflare credentials; do not attempt to deploy without them.
- Do not commit build output (`dist`) or `node_modules` — both are gitignored.
