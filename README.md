# Acquisitions API

A Node.js backend API for an acquisitions system, built with Express.js. This application uses a modern ES modules setup with path aliases, leveraging PostgreSQL via Neon and Drizzle ORM for database operations.

## 🚀 Tech Stack

- **Runtime**: [Node.js](https://nodejs.org/) (ES Modules)
- **Framework**: [Express.js](https://expressjs.com/) 5.x
- **Database**: PostgreSQL with [Neon](https://neon.tech/) Serverless
- **ORM**: [Drizzle ORM](https://orm.drizzle.team/)
- **Authentication**: JWT & bcrypt
- **Validation**: [Zod](https://zod.dev/)
- **Logging**: [Winston](https://github.com/winstonjs/winston) & Morgan
- **Code Quality**: ESLint, Prettier, & Jest (experimental)

## 📋 Prerequisites

- Node.js (v18+ recommended)
- Docker & Docker Compose (for containerized development)
- A [Neon Account](https://console.neon.tech/) for the database

## 🛠️ Getting Started

### 1. Installation

```bash
git clone https://github.com/sketch-afk/acquisitions.git
cd acquisitions
npm install
```

### 2. Environment Variables

Create your environment files. You can copy the provided example:

```bash
cp .env.example .env.development
```

Required variables (see `.env.example`):

- `PORT` (default 3000)
- `DATABASE_URL` (Neon PostgreSQL connection string)
- `JWT_SECRET` (For signing tokens)
- `NODE_ENV` (`development` or `production`)
- `LOG_LEVEL` (default `info`)

### 3. Database Setup

Ensure your `.env.development` has a valid `DATABASE_URL`.

```bash
# Generate database migrations
npm run db:generate

# Apply database migrations
npm run db:migrate

# Open Drizzle Studio to inspect the database
npm run db:studio
```

### 4. Running the Application

```bash
# Start the development server (with hot-reload)
npm run dev

# Start the production server
npm start
```

## 🐳 Docker & Production Deployment

The project includes configurations for running via Docker in both development and production modes.

### Local Deployment

- **Development (`npm run dev:docker`)**: Uses Neon Local for an ephemeral database branch, enabling a fresh database for each session without impacting production data. Features hot-reloading on port 3000.
- **Production (`npm run prod:docker`)**: Connects directly to the Neon Cloud database, running an optimized multi-stage build image with resource limits and health checks.

For detailed setup, networking, and migration instructions inside containers, please refer to the [Docker Setup Guide](./DOCKER_SETUP.md).

## 🏗️ Project Architecture

The application follows a layered request-handling structure:

- **Routes** (`src/routes/`): Express route definitions mapped to controllers.
- **Controllers** (`src/controllers/`): Handle HTTP requests, parsing bodies, and managing responses.
- **Services** (`src/services/`): Core business logic and database interactions.
- **Models** (`src/models/`): Drizzle ORM table definitions.
- **Validations** (`src/validations/`): Zod schemas for request validation.

### Import Aliases

The project uses native Node.js subpath imports defined in `package.json`. Use these aliases instead of relative paths:

- `#config/*` → `src/config/*`
- `#controllers/*` → `src/controllers/*`
- `#middleware/*` → `src/middleware/*`
- `#models/*` → `src/models/*`
- `#routes/*` → `src/routes/*`
- `#services/*` → `src/services/*`
- `#utils/*` → `src/utils/*`
- `#validations/*` → `src/validations/*`

## 🧪 Testing & CI/CD Workflows

### Code Quality & Local Testing

The project uses ESLint and Prettier for code quality, and Jest for experimental ES Modules testing.

```bash
# Lint code
npm run lint

# Auto-fix lint issues
npm run lint:fix

# Format code
npm run format

# Run tests (runs with NODE_OPTIONS=--experimental-vm-modules)
npm run test
```

### GitHub Actions Workflows

Automated CI/CD pipelines are managed via GitHub Actions:

- **Tests Workflow (`.github/workflows/tests.yml`)**: Triggered on `push` and `pull_request` to `main` and `staging` branches. It sets up Node.js 20.x, runs `npm test` against a local test database or the provided `TEST_DATABASE_URL`, and automatically uploads code coverage reports which are retained for 30 days.

- **Docker Build & Push (`.github/workflows/docker-build-and-push.yml`)**: Triggered on `push` to the `main` branch or manually via `workflow_dispatch`. It utilizes Docker Buildx for multi-platform builds (`linux/amd64`, `linux/arm64`) and automatically tags and pushes production-ready images to Docker Hub, tagged with the branch name, Git SHA, and a timestamp for production traceability.

## 🤖 AI Assistant Guidelines

If you are using an AI coding assistant (like Cursor, WARP, or GitHub Copilot), please refer to the following guidelines included in the repository:

- [WARP.md](./WARP.md) - General guidance and architecture overview for AI agents.
- [AGENTS.md](./AGENTS.md) - Specific rules, technology stack details, and coding conventions.
