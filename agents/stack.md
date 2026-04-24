# Project Stack Definition
# Location: /agents/stack.md
#
# ⚠️  THIS FILE IS THE SINGLE SOURCE OF TRUTH for all technology decisions.
#     All agents must read this file before generating any output.
#     No library, framework, or tool should be used unless declared here.
#     Versions are intentionally omitted — always use the latest stable release
#     available at the time of implementation.

---

## 🔧 HOW TO USE THIS FILE

1. Fill in ONLY the sections relevant to your project.
2. Delete or mark as N/A any sections that do not apply.
3. When a new dependency is needed, declare it here FIRST, then prompt the agent.
4. Agents must not assume any technology not listed in this file.

---

## 📦 FRONTEND

Framework:        [ Next.js | React | Vue | Nuxt | SvelteKit | Angular | None ]
Language:         [ TypeScript | JavaScript ]
Styling:          [ Tailwind CSS | CSS Modules | Styled Components | SCSS | None ]
State Management: [ Zustand | Pinia | Redux Toolkit | Jotai | Context API | None ]
Form Handling:    [ React Hook Form | VeeValidate | Formik | Native | None ]
HTTP Client:      [ Fetch API | Axios | TanStack Query | SWR | None ]
UI Component Lib: [ shadcn/ui | Vuetify | PrimeVue | MUI | Headless UI | None ]
Routing:          [ Next.js App Router | React Router | Vue Router | None ]

> Instructions to agents: Generate all UI code, components, and state patterns
> using ONLY the libraries listed above. Do not introduce additional frontend
> dependencies without updating this file first.

---

## ⚙️ BACKEND

Runtime:          [ Node.js | Python | Go | Java | .NET | None ]
Framework:        [ Express | Fastify | FastAPI | Django | NestJS | Flask | None ]
Language:         [ TypeScript | JavaScript | Python | Go | Java | C# ]
Input Validation: [ Zod | Pydantic | Joi | class-validator | None ]
ORM / DB Layer:   [ Prisma | Drizzle | SQLAlchemy | TypeORM | Raw SQL | None ]
Auth Strategy:    [ JWT | Session | OAuth2 | API Key | Supabase Auth | None ]
API Style:        [ REST | GraphQL | tRPC | gRPC | Mixed ]

> Instructions to agents: All API routes must use the validation library declared
> above. All database access must go through the ORM/DB layer declared above.
> No direct DB connections from route handlers — always through a service layer.

---

## 🗄️ DATABASE

Primary DB:       [ PostgreSQL | MySQL | MongoDB | SQLite | Supabase | PlanetScale | None ]
Hosting:          [ Supabase | Railway | PlanetScale | Self-hosted | Local only ]
Migrations:       [ Prisma Migrate | Alembic | Flyway | Supabase CLI | Manual | None ]
Caching:          [ Redis | Upstash | In-memory | None ]
Search:           [ PostgreSQL FTS | Typesense | Meilisearch | Algolia | None ]

> Instructions to agents: All schema changes must be done through the migration
> tool declared above. Never modify the database schema directly in production.
> Always generate a migration file for schema changes.

---

## 🔐 AUTH & SECURITY

Auth Provider:    [ Supabase Auth | Auth.js | Clerk | Firebase Auth | Custom JWT | None ]
Session Storage:  [ HTTP-only Cookie | localStorage (dev only) | Supabase Session ]
Role System:      [ Yes | No ]
2FA:              [ Yes | No ]

---

## 🧪 TESTING

Unit Testing:     [ Vitest | Jest | Pytest | Go Test | None ]
E2E Testing:      [ Playwright | Cypress | Selenium | None ]
API Testing:      [ Supertest | Pytest | Postman Collections | None ]
Coverage Target:  [ 80% | 70% | Best effort | None ]

> Instructions to agents: All generated code must include corresponding tests
> using the frameworks declared above. Test files must follow the naming
> convention: [filename].test.[ext] or test_[filename].[ext] for Python.

---

## 🚀 DEPLOYMENT & INFRASTRUCTURE

Frontend Host:    [ Vercel | Netlify | Cloudflare Pages | AWS | Azure | None ]
Backend Host:     [ Railway | Fly.io | Render | AWS | Azure | GCP | None ]
Containerization: [ Docker | Docker Compose | None ]
CI/CD:            [ GitHub Actions | GitLab CI | CircleCI | None ]
Environment Mgmt: [ .env files | dotenv | AWS Secrets | Azure Key Vault | None ]

---

## 📏 CODE CONVENTIONS

Linter:           [ ESLint | Pylint | Ruff | Biome | None ]
Formatter:        [ Prettier | Black | gofmt | None ]
Git Conventions:  [ Conventional Commits | Custom | None ]
Branch Strategy:  [ GitHub Flow | Gitflow | Trunk-based | None ]

---

## ⚠️ DECLARED EXCEPTIONS

> List any intentional deviations from the universal rules in copilot-instructions.md.
> Every exception must have a documented reason.

| Exception | Reason | Approved By | Date |
|---|---|---|---|
| (none) | — | — | — |

---

## 📝 CHANGE LOG

> Every time this file is updated, log the change here.

| Date | Change | Reason |
|---|---|---|
| YYYY-MM-DD | Initial stack definition | Project kickoff |
