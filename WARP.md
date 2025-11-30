# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Next.js App Router Playground used by the DX team to explore, test, and demo Next.js features. It serves as a testing ground for writing documentation and identifying bugs in Next.js.

**Key characteristics:**
- Uses Next.js 16.0.0-canary with React 19 canary
- Employs experimental features (viewTransition, cacheComponents, routerBFCache)
- Contains demo implementations of various Next.js App Router patterns
- Uses Turbopack for faster development

## Development Commands

### Core Commands
- **Start dev server**: `pnpm dev` (uses Turbopack)
- **Production build**: `pnpm build`
- **Start production server**: `pnpm start`
- **Format code**: `pnpm prettier`

### Package Manager
This project uses **pnpm** exclusively. The `.npmrc` file enforces exact version matching (`save-exact=true`).

**Important**: Always use `pnpm` commands, not `npm` or `yarn`.

## Architecture

### Path Alias System
The project uses a `#/` path alias (configured in `tsconfig.json`) for absolute imports:
- `#/lib/*` - Utilities and helpers
- `#/ui/*` - Reusable UI components
- `#/styles/*` - Global styles
- `#/app/*` - App directory routes

### Directory Structure

**Core Directories:**
- `app/` - Next.js App Router pages and routes
  - `_internal/` - Internal utilities and mock data (not routes due to `_` prefix)
  - `_hooks/`, `_patterns/` - Demo sections for specific Next.js features
  - Various feature demos: `cached-components/`, `parallel-routes/`, `loading/`, etc.
- `lib/` - Shared utilities and mock database layer
- `ui/` - Reusable React components (Boundary, Button, etc.)
- `styles/` - Global CSS and Tailwind configuration

### Mock Database Layer
`lib/db.ts` provides a Prisma-inspired mock ORM for demo purposes:
- **NOT** intended as a learning resource for database patterns
- Simplifies non-essential parts of demos
- Imports data from `app/_internal/_data.ts`
- Provides methods: `find()`, `findMany()` for products, sections, categories, and demos

**Types available**: `Product`, `Section`, `Category`, `Demo`, `DemoCategory`

### Data Flow
1. Demo routes are defined in `app/_internal/_data.ts`
2. `lib/db.ts` exposes a query interface over this data
3. Root layout (`app/layout.tsx`) fetches demos via `db.demo.findMany()`
4. `GlobalNav` component renders the navigation from this data

### MDX Configuration
- Supports `.mdx` files as pages via `@next/mdx`
- Uses CodeHike for enhanced code highlighting
- Custom MDX components defined in `mdx-components.tsx`
- CodeHike config: uses custom `MyCode` and `MyInlineCode` components

### Styling
- **Tailwind CSS 4.x** with PostCSS
- Dark theme by default (`[color-scheme:dark]`)
- Custom fonts: Geist Sans and Geist Mono (variable fonts)
- Prettier plugin for Tailwind class sorting

## Code Patterns

### Server-Only Code
Use `import 'server-only'` at the top of files that should never run on the client (e.g., `lib/db.ts`).

### Component Boundaries
The `Boundary` component (`ui/boundary.tsx`) is used throughout demos to visually highlight different route segments and components. It supports animation rerendering visualization.

### Experimental Features in Use
From `next.config.ts`:
- `cacheComponents: true` - Component-level caching
- `viewTransition: true` - View transitions API support
- `prerenderEarlyExit: false` - Disable early exit during prerendering
- `routerBFCache: true` - Browser forward/back cache support

### Caching Demos
The project includes extensive caching examples:
- **Cached Routes**: Route segment caching
- **Cached Components**: React Server Component caching  
- **Cached Functions**: Function result caching
- **Remote Cache**: Runtime caching with `use cache: remote`
- **Private Cache**: User-specific caching with `use cache: private`

Check `app/cached-*` directories for implementations.

## TypeScript Configuration

- **Strict mode enabled**: All type checking is strict
- **Path alias**: `#/*` maps to project root
- **Module resolution**: Node-style with ESM
- **React JSX**: Uses new `react-jsx` transform (React 19)

## Deployment

The project includes Azure Web App deployment via GitHub Actions (`.github/workflows/main_app-op.yml`):
- Triggers on push to `main` or manual dispatch
- Uses Node.js 20.x
- Runs `npm install`, `npm run build`, and `npm run test --if-present`
- Deploys to Azure Web App named 'app-op'

**Note**: The CI uses `npm` but local development should use `pnpm`.

## Code Style

Prettier configuration (`.prettier.config.js`):
- Single quotes
- Semicolons required
- Trailing commas
- Arrow function parentheses always included
- Tailwind class sorting via plugin

Run `pnpm prettier` to format all files.

## Demo Routes

The playground is organized into feature categories (defined in `app/_internal/_data.ts`):

1. **Layouts**: Nested layouts, route groups, parallel routes
2. **File Conventions**: loading.tsx, error.tsx, not-found.tsx
3. **Caching**: Various caching strategies and implementations
4. **APIs**: React/Next.js API demos (e.g., useLinkStatus)
5. **Misc**: Additional experimental features

Each demo typically includes its own README.mdx explaining the feature.

## Important Notes

- This is an **experimental playground**, not production code
- Features may be unstable (canary versions)
- Mock data in `_internal/_data.ts` should not be used as reference for real database patterns
- Some features require Next.js canary builds to function properly
