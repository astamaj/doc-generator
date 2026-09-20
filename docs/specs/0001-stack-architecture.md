# 0001. Stack & architecture for the Business Document Generator

**Date**: 2026-09-20
**Status**: Proposed

## Summary

This decision locks the technology stack for the whole product: a Next.js client and a separate Express API, both in TypeScript, sharing a PostgreSQL database on Neon through Drizzle ORM. Auth is handled by Better Auth, a self hosted auth library (a library that runs on your own server rather than a hosted service), and files live in Cloudflare R2. Every layer was chosen to be boring, current, and kind to a solo builder, so the scaffold can boot fast and every later slice builds on real structure.

## Context

> ⚠️ Premise note: you chose pure duplication for the code the client and server share (DTOs, Zod schemas, and eventually the document engine's calculation core), kept in sync by review. This conflicts with the one core principle in PRODUCT_SPEC section 77 and the DTO rules in AGENTS.md: two copies of the calculation engine means every total the app prints is computed by two code paths, and one drift is an invoice that disagrees with its PDF. For the record, a minimal `shared/` npm workspace holding only the DTOs, schemas, and calculation core was the cheaper path: it costs one root package.json of wiring and removes the drift risk entirely. You declined it in favor of the flat layout, and this spec honors that choice. The risk lives in Consequences and Follow up, and the duplication should be revisited the first time it hurts.

The product is a multi tenant SaaS for business documents (invoices, quotations, estimates, receipts, purchase orders), built by one person with no fixed deadline. PRODUCT_SPEC.md sketched a stack (sections 46 and 47) and AGENTS.md recorded it as a draft, with an explicit instruction: this spec confirms or revises that draft before any scaffold exists. No source files exist yet (the source count is 0), so this is a greenfield decision: every layer is open, and the cost of a wrong call is paid on every later slice.

The forces that shaped the choice: a solo builder needs the smallest operations burden that still scales to hundreds of users and thousands of documents per business; the clean architecture layering in AGENTS.md (routes to controllers to services to repositories) needs a server shape that honors it; money math must be exact for BDT amounts; English and Bangla are required from day one; and the architecture must keep room for OAuth, team roles, public links, and later document types without a rewrite.

## Options considered

### Option 1: Next.js client plus a separate Express API

Two processes. Next.js serves the app, Express serves a REST API following the PRODUCT_SPEC endpoint sketch (section 52) and the layered structure (section 48). Each side deploys and scales on its own; business logic lives in services behind thin controllers.

**Pros**:
- Matches the AGENTS.md layering rules exactly: the Express server gives business logic a real home (services and repositories) outside the rendering framework
- Heavy work (PDF generation later) stays off the rendering path
- Each side can be deployed, restarted, and scaled independently

**Cons**:
- Two deployables, plus CORS and cookie wiring between them
- Client and server share no compiled types unless you build that bridge yourself

### Option 2: Next.js full stack, one process

Next.js route handlers and server actions do the API work too. One deployable, less plumbing.

**Pros**:
- Simplest possible deployment: one app, one process
- Server components can read data directly with no API hop

**Cons**:
- Business logic ends up living inside the framework's request model, which fights the routes to controllers to services to repositories layering the project committed to
- PDF generation and heavy work share the rendering process
- A future public API (the Professional tier plan) would need extracting from framework internals

### Option 3: Next.js client plus tRPC or typed RPC

End to end types with no REST layer.

**Pros**:
- Excellent type safety across the boundary with zero duplicated DTOs, which would erase the duplication concern in the premise note

**Cons**:
- The PRODUCT_SPEC's REST surface (section 52) and the future public API plan point at REST; tRPC would be a second contract to maintain or replace
- One more framework level dependency the whole stack leans on, for a team of one

## Decision

**Chosen option**: Option 1: Next.js client plus a separate Express API

The product runs as a Next.js web client and a separate Express REST API in TypeScript strict mode, sharing a Neon PostgreSQL database through Drizzle ORM, with Better Auth for sessions, Cloudflare R2 for files, next intl for English and Bangla, and exact decimal money math in the document engine.

**Implementation skills**: `shadcn` (`shadcn/ui`, `.claude/skills/shadcn/`) · `better-auth-best-practices` (`better-auth/skills`, `.claude/skills/better-auth-best-practices/`) · `neon-postgres` (`neondatabase/agent-skills`, `.claude/skills/neon-postgres/`) · `drizzle` (`bobmatnyc/claude-mpm-skills`, `.claude/skills/drizzle/`) · `tanstack-query` (`tanstack-skills/tanstack-skills`, `.claude/skills/tanstack-query/`) · `wrangler` (`cloudflare/skills`, `.claude/skills/wrangler/`) · `cloudflare-r2` (`secondsky/claude-skills`, `.claude/skills/cloudflare-r2/`)

## Rationale

For a solo build with the layering rules already written into AGENTS.md, the separate Express API is the only option where business logic gets the home the rules demand without fighting the framework. The engineer expressed a preference for pure duplication of shared code instead of a workspace or shared folder. Based on the one core principle in PRODUCT_SPEC section 77, duplication of the calculation engine is the riskier choice, because a drifted copy produces wrong totals on one side only; it is recorded here as the decision and paired with a follow up to revisit it, since the cost of extracting a shared package later is small compared to shipping nothing now.

Neon was picked over Supabase deliberately: you already chose Better Auth and a provider abstracted storage layer, so a pure serverless Postgres with database branching is the clean fit, and Supabase's bundled auth and storage would overlap and clash with both. Better Auth over hand rolled sessions follows the expert rule that auth is never built from scratch; its organizations plugin maps naturally onto businesses with Owner and Staff roles, keeping room for the OAuth providers the scope defers. Hosting stays deliberately undecided (scaffold is host neutral) so the cookie, CORS, and storage wiring never leans on a platform that has not been chosen; the constraint is two deployables plus managed Postgres. Money is stored as `numeric(14,2)` in Postgres and travels as strings through DTOs, with `decimal.js` doing all arithmetic in the engine, because float math on money is how invoices end up one poisha wrong. The duplication decision was made with eyes open: a minimal `shared/` workspace was available at near zero cost and would have removed the drift risk; you chose the flat layout for its simpler start, with a follow up to switch the moment it hurts.

## Proposed stack

| Layer | Choice | Reason |
|---|---|---|
| Language | TypeScript (strict), Node.js, both sides | One language across client and server; strict mode and no `any` per AGENTS.md |
| Client framework | Next.js (App Router) | The PRODUCT_SPEC draft, confirmed; server rendering for the app shell and document previews |
| UI | Tailwind CSS with shadcn/ui | Accessible component base in your own repo, full control over Bangla typography and focus behavior for the WCAG AA baseline |
| Server state | TanStack Query | Cache control, optimistic updates for auto save, retries, loading and error states for free |
| Forms | React Hook Form plus Zod | Shared validation schemas, one source of truth per form on each side |

| Internationalization | next intl, prefix based routing (`/en/...`, `/bn/...`); the bare root redirects to `/en/`, and first visit detects the preferred locale from the Accept Language header, falling back to English | Current Next.js standard for English and Bangla; prefix routing suits two locales on one deployment; JSON message catalogs a translator can edit; Bangla first browsers land on `/bn/` automatically |
| Server framework | Express with routes to controllers to services to repositories | Honors the AGENTS.md layering; business logic lives in services and the document engine |
| Server toolchain | tsx in development, tsc build to dist for production | Fast restarts while developing, real type checking and plain JavaScript at build time |
| Database | PostgreSQL on Neon, Drizzle ORM | Exact numeric types for money, JSON metadata, branching for migration testing; Drizzle is SQL first and type safe |

| Migrations | drizzle-kit, one recorded workflow: change the Drizzle schema, `push` to the dev database to iterate, `generate` the migration files into `server/src/db/migrations/`, then test those files on a fresh Neon branch before applying to production | Fast iteration against a dev database, real versioned migrations where data must survive, and the branching claim actually exercised before anything touches production data |

| Local database | Neon cloud dev database, with a recorded fallback: if Neon is unreachable, point `DATABASE_URL` at a local Postgres (Docker or installed) and the app keeps running | Matches production behavior and no local process to manage in normal times; the fallback keeps a solo builder working through an outage, and the Drizzle schema is portable. The test branch workflow stays Neon only |

| Auth & sessions | Better Auth (self hosted) on the Express server, mounted at `/api/auth/*` and reached through the client's proxy; organizations plugin | Proven library instead of hand rolled auth; organizations map to businesses; OAuth room kept; one process owns sessions, business logic, and tenant context. The product's Owner and Staff names map onto the plugin's configurable roles (Owner to its built in owner role, Staff to a custom staff role); the plugin ships owner, admin, and member by default, so the scaffold must register Staff explicitly |
| Session middleware | A `requireAuth` middleware validates the Better Auth session and attaches `userId`, `organizationId`, and `role` to the request context, rejecting with 401 otherwise | This is the contract the AGENTS.md rule on tenant checks in services depends on; every protected route and every service reads the tenant from one place |
| Multi business sessions | The session carries `currentOrganizationId`; switching business is a session update, never a re login | Keeps the door open for a user in more than one business; the membership model itself belongs to the data model spec |
| API shape | REST JSON with the envelope `{ success, data, message, errors }`, where `errors` is a field level array (PRODUCT_SPEC section 53); the client keeps one small typed fetch wrapper per resource in `client/src/lib/api.ts`, which throws a typed `ApiError` on any non 2xx response so TanStack Query handlers can branch on status (401 to re login, 422 to field errors, 5xx to a server message) | Matches the PRODUCT_SPEC surface and the future public API plan; zero new dependencies; a recorded envelope stops the first three endpoints from inventing three shapes |
| Money handling | `numeric(14,2)` in Postgres, strings in DTOs, `decimal.js` for exact arithmetic in the document engine | No float rounding on invoices; formatting stays a presentation concern |
| Document numbering | The exact mechanism is deferred to the data model spec; the constraint recorded here is that numbering must be atomic per business and per document type, and must never leak across tenants | Two users creating documents at once will break a naive counter; writing the constraint down now blocks race prone designs before the data model spec is drafted |
| Document snapshots | The storage shape is deferred to the data model spec; the constraints recorded here are that snapshots of prices, tax, and party details are immutable once a document is finalized, and that the ORM layer must support JSONB so that shape stays available | Immutability is a rule every later feature leans on; JSONB support keeps the data model spec's options open |
| File storage | Cloudflare R2 behind a storage port defined in `server/src/ports/storage.ts`: `put(key, body)` returns the stored key, `get(key)` returns a stream, `delete(key)`, and `getSignedUrl(key, expiresInSeconds)` returns a URL string; the R2 adapter and any dev stub implement the same contract | The PRODUCT_SPEC's named provider, wired from the scaffold behind an interface so nothing is coupled to it; recording the interface now stops adapters from leaking into business code later |
| Email | A send port with a dev adapter that logs to the console; provider chosen later | Better Auth's reset flow works in development today; no mailbox account needed until real email matters |

| Repo layout | Flat `client/` and `server/` folders; shared code (DTOs, Zod schemas, engine core) duplicated at mirrored paths, `client/src/shared/{dtos,schemas,engine}` and `server/src/shared/{dtos,schemas,engine}` | The engineer's explicit choice; simplest start, with the drift risk recorded in Consequences. Identical path prefixes make drift grep able, and the diff check in Follow-up mechanical |
| Shared code drift check | A CI step (installed by the tooling feature) diffs `client/src/shared/` against `server/src/shared/` and fails on divergence | Turns silent drift into a loud failure before merge; review alone was a hope, not a mechanism, and a drifted total is an invoice that disagrees with its PDF |
| Development workflow | Two terminals, one per process; the client proxies `/api` to the server in development | Each folder stays self contained with its own scripts |
| Hosting | Decided later; scaffold stays host neutral (constraints: two deployables, managed Postgres, no platform locked APIs) | Keeps cookie, CORS, and provisioning decisions honest when a target is chosen |
| Logging | `pino` structured JSON logging on the server with one central error middleware; hosted monitoring deferred per scope | The MVP scope defers analytics and error monitoring; structured logs are the cheap foundation for later |
| Node.js runtime | Node 22 LTS (or the active LTS at scaffold time), recorded in `.nvmrc` and `engines` | One pinned runtime for both processes, locally and in CI |

| Development networking | Server on port 4000, client on port 3000, `next.config` rewrites `/api/*` to the server; CORS allows `http://localhost:3000` with `credentials: true`; the session cookie is SameSite=Lax and host only (no domain attribute) in development, and because the rewrite makes API calls same origin from the browser's point of view, Lax works without any Secure or cross origin cookie settings | The two process shape needs these wiring details from day one, and they change again when hosting is chosen |
| Env validation | A thin `env.ts` wrapper around Zod, parsed once at startup, fail fast. The recorded variable set (and the content of `.env.example`): `DATABASE_URL`, `BETTER_AUTH_SECRET`, `BETTER_AUTH_URL`, `R2_ACCESS_KEY_ID`, `R2_SECRET_ACCESS_KEY`, `R2_BUCKET_NAME`, `R2_ENDPOINT`, `PORT` (server), `NEXT_PUBLIC_API_URL` (client) | AGENTS.md requires env validation at startup; Zod is already in the stack, so no new dependency; a recorded list stops the first run from failing on a forgotten variable |

| Test database | Integration tests run against a dedicated Neon branch, not the shared dev database; the test setup creates the branch from the current schema at run start, seeds fixtures, and drops it after, and nothing in the suite hardcodes a database URL | Branching is instant and disposable; tests stay isolated and behave the same in CI; a fresh branch per run means no shared state hiding failures |
| PDF generation | Deferred to its own spec (scope feature 14, `/architect PDF generation`); the scaffold precommits no renderer | The right choice depends on template design (scope feature 13), and Bangla font support is the deciding factor; the API and storage shape here already leave room |
| Test runner | Deferred to `/test`, which records the preference; the scaffold only reserves a `test` script name | AGENTS.md deliberately leaves the framework to the test skill; naming it here would duplicate that decision |
| Pre-commit tooling | Deferred to `/develop tooling` (scope feature 2), expected to be husky plus lint-staged | AGENTS.md requires lint, format, and typecheck on every commit; the tooling feature installs it from the real scaffold |

## Consequences

**Positive**:
- Every later slice builds on a stack where business logic already has its mandated home, and the document engine can grow without framework interference
- Boring, widely used tools with good docs mean a solo builder spends time on the product, not on the platform
- Exact money math and provider abstracted storage and email remove two whole classes of later rework
- Neon branching lets each feature test migrations on a throwaway copy of the database

**Negative / tradeoffs**:
- Duplicated DTOs, Zod schemas, and eventually the calculation engine must be kept in sync by review; one drift produces totals that disagree between screen and PDF. This is the accepted cost of the flat layout choice
- Two deployables means CORS, cookie domain, and proxy wiring from day one, and two things to deploy when hosting is chosen
- Better Auth brings its own data model expectations, which the data model spec must accommodate rather than the reverse
- R2 wired from the scaffold needs a Cloudflare account and credentials before the first logo upload is testable
- Development and tests need the internet: the Neon dev database and the Neon test branches are remote, so an offline session cannot run the app or the integration suite

**Neutral**:
- The PRODUCT_SPEC env example lists `MONGODB_URI`; the scaffold replaces it with `DATABASE_URL` and Neon equivalents, and `.env.example` follows suit
- The installed agent skills (listed in the Decision) become the conventions for their layers; AGENTS.md should reference them once the scaffold exists
- No credible MCP servers were found for this stack; the registry searches came back empty

## Follow-up

- [ ] The seven installed agent skills are not yet listed in an `## Agent skills` section of AGENTS.md; they belong there (root level, since their layers span the whole project) so later skills load only what they need
- [ ] Revisit the shared code duplication the first time a DTO or calculation drifts: the cheap fix is an npm workspaces `shared/` package, which supersedes the flat layout decision only for shared code
- [ ] The tooling feature installs the shared code drift check (diff `client/src/shared/` against `server/src/shared/`, fail on divergence) alongside lint and pre-commit, so the check exists before the first shared DTO does
- [ ] Choose a hosting target before the first real deploy; the constraints are two deployables plus managed Postgres, and the decision unblocks cookie and CORS specifics
- [ ] Pick an email provider (Resend or similar) when the first real email is needed; the send port is already defined
- [ ] If MCP servers are wanted later, check the smithery.ai or glama.ai registries directly; the standard searches found nothing credible for this stack