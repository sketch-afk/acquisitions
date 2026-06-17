# AGENTS.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Technology Stack

- **Runtime**: Node.js with ES modules (`"type": "module"` in `package.json`).
- **Framework**: Express.js.
- **Database**: PostgreSQL via Neon (`@neondatabase/serverless`), using Drizzle ORM for schema definition and queries.
- **Validation**: Zod (schemas in `src/validations/`).
- **Auth**: bcrypt for password hashing, jsonwebtoken for JWTs, cookie-parser for cookies.
- **Logging**: Winston with file transports (`logs/`) and console transport in non-production.
- **Middleware**: helmet, cors, morgan (HTTP logging piped to Winston), express.json/urlencoded, cookie-parser.

## Common Commands

| Command                | Purpose                                                                          |
| ---------------------- | -------------------------------------------------------------------------------- |
| `npm run dev`          | Start the development server with `node --watch` (auto-restart on file changes). |
| `npm run lint`         | Run ESLint across the codebase.                                                  |
| `npm run lint:fix`     | Run ESLint with auto-fix.                                                        |
| `npm run format`       | Run Prettier and write formatting changes.                                       |
| `npm run format:check` | Check Prettier formatting without writing changes.                               |
| `npm run db:generate`  | Generate Drizzle migration SQL files from schema changes.                        |
| `npm run db:migrate`   | Apply Drizzle migrations to the database.                                        |
| `npm run db:studio`    | Open Drizzle Studio to inspect the database.                                     |

There is no test runner configured yet. The `eslint.config.js` references a `tests/` directory for future test globals.

## Environment Variables

Copy `.env.example` to `.env` and set the required values:

- `PORT` — Server port (default 3000).
- `NODE_ENV` — `development` or `production`.
- `LOG_LEVEL` — Winston log level (default `info`).
- `DATABASE_URL` — Neon PostgreSQL connection string.
- `JWT_SECRET` — Secret used for JWT signing/verification (fallback exists in code for development only).

## Code Architecture

The project follows a layered request-handling structure. Requests flow through these layers in order:

1. **Entry Point**: `src/index.js` loads `dotenv/config` and then `src/server.js`. `src/server.js` imports `src/app.js` and starts the Express listener on `PORT`.
2. **App Configuration**: `src/app.js` mounts global middleware (helmet, cors, express.json, express.urlencoded, cookie-parser, morgan) and route modules under `/api`.
3. **Routes**: `src/routes/` define Express routers and map HTTP methods/paths to controller functions. Only the `auth` module is currently wired in (`/api/auth`).
4. **Controllers**: `src/controllers/` handle the HTTP request/response cycle. They parse `req.body`, call validation schemas, invoke services, set cookies via `src/utils/cookies.js`, and return JSON responses. Errors are passed to `next(e)` for centralized handling (or returned with specific status codes like 400/409).
5. **Services**: `src/service/` contain business logic and database interactions. They use Drizzle ORM (`eq`, `insert`, `select`) and bcrypt for hashing. They do not handle HTTP directly.
6. **Models**: `src/models/` define Drizzle ORM `pgTable` schemas. The `users` table is the only schema currently defined.

### Subpath Imports

The project uses Node.js subpath imports defined in `package.json` under the `"imports"` field. Always use these aliases instead of relative paths:

- `#config/*` → `src/config/*`
- `#controllers/*` → `src/controllers/*`
- `#middleware/*` → `src/middleware/*`
- `#models/*` → `src/models/*`
- `#routes/*` → `src/routes/*`
- `#service/*` → `src/service/*`
- `#utils/*` → `src/utils/*`
- `#validations/*` → `src/validations/*`

### Drizzle ORM Usage

- Schema files are in `src/models/*.js` and referenced by `drizzle.config.js`.
- `src/config/database.js` creates a Drizzle client using `neon(process.env.DATABASE_URL)`.
- Migrations are output to the `drizzle/` directory.
- When modifying the schema, run `npm run db:generate` then `npm run db:migrate` to apply changes.

### Code Style

- ESLint config is in `eslint.config.js` (flat config). Prettier config is in `.prettierrc`.
- 2-space indentation, single quotes, semicolons required, `prefer-const`, arrow functions preferred.
- Unused variables prefixed with `_` are allowed.
- `linebreak-style` is commented out in ESLint config, so both LF and CRLF are accepted in this repo.
