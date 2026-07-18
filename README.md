# unicorn-dev

A full-stack template for building a React application with TypeScript and Vite, served by a [Hono](https://hono.dev/) backend running on [Cloudflare Workers](https://developers.cloudflare.com/workers/). It provides hot module replacement, ESLint integration, and single-command deployment to Cloudflare's edge network.

This project is based on the [Cloudflare Vite React template](https://github.com/cloudflare/templates/tree/main/vite-react-template).

## Features

- **React 19** with TypeScript for the frontend (`src/react-app`).
- **Hono** backend served from a Cloudflare Worker (`src/worker`), exposing a JSON API under `/api/`.
- **Vite 6** with the `@cloudflare/vite-plugin` for a unified dev server and build.
- **Hot Module Replacement (HMR)** during development.
- **ESLint** (flat config) with TypeScript, React Hooks, and React Refresh plugins.
- **Single-page application** asset handling and **Observability** enabled via `wrangler.json`.
- A [UnicornStudio](https://www.unicorn.studio/) interactive scene embedded in `index.html`.

## Requirements

- **Node.js** (a current LTS release is recommended; developed against Node.js 20).
- **npm** (bundled with Node.js).
- A **Cloudflare account** and the [Wrangler](https://developers.cloudflare.com/workers/wrangler/) CLI (installed as a dev dependency) for deployment.

## Installation

Install dependencies:

```bash
npm install
```

## Usage

Start the development server:

```bash
npm run dev
```

The application is served at [http://localhost:5173](http://localhost:5173) by default. The Worker API is available under `/api/` (for example, `GET /api/` returns `{ "name": "Cloudflare" }`).

## Development Commands

All commands are defined in `package.json`:

| Command | Description |
| --- | --- |
| `npm run dev` | Start the Vite dev server with HMR. |
| `npm run build` | Type-check the project (`tsc -b`) and build for production with Vite. |
| `npm run preview` | Build the project and preview the production output locally. |
| `npm run lint` | Run ESLint across the project. |
| `npm run check` | Type-check, build, and run `wrangler deploy --dry-run` to validate the deploy. |
| `npm run cf-typegen` | Regenerate Worker binding types (`worker-configuration.d.ts`) via `wrangler types`. |
| `npm run deploy` | Deploy the Worker and assets to Cloudflare (`wrangler deploy`). |

To deploy to Cloudflare Workers:

```bash
npm run build && npm run deploy
```

Monitor a deployed Worker's logs:

```bash
npx wrangler tail
```

## Project Structure

```
.
├── index.html                 # HTML entry (embeds a UnicornStudio scene)
├── src/
│   ├── react-app/             # React frontend
│   │   ├── main.tsx           # React entry point
│   │   ├── App.tsx            # Root component (counter + /api/ demo)
│   │   ├── assets/            # SVG logos
│   │   └── *.css              # Styles
│   └── worker/
│       └── index.ts           # Hono app / Cloudflare Worker entry
├── public/                    # Static assets served as-is
├── vite.config.ts             # Vite config (React + Cloudflare plugins)
├── wrangler.json              # Cloudflare Workers configuration
├── worker-configuration.d.ts  # Generated Worker binding types
├── eslint.config.js           # ESLint flat config
├── tsconfig*.json             # TypeScript project references (app / node / worker)
└── package.json
```

TypeScript is split into project references: `tsconfig.app.json` (React app), `tsconfig.worker.json` (Worker), and `tsconfig.node.json` (build tooling such as `vite.config.ts`).

## License

No license file is present, and `package.json` is marked `"private": true`. No open-source license is currently specified for this repository.
