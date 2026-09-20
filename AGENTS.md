# Business Document Generator

A SaaS web app to create professional invoices, quotations, estimates, receipts, and purchase orders. Multi tenant from day one: every business owned row carries a businessId, and documents snapshot the prices, tax, and customer details they were issued with.

## Stack

- **Language / Runtime**: TypeScript (strict), Node.js
- **Framework**: Next.js (client), Express (server)
- **Key dependencies**: Tailwind CSS with shadcn/ui, TanStack Query, React Hook Form, Zod, PostgreSQL with Drizzle ORM
- **Package manager**: npm

Stack recorded from PRODUCT_SPEC.md recommendations (sections 46 and 47). No architecture spec exists yet: `/architect stack & architecture` confirms or revises this list before the scaffold, and this section then mirrors that spec.

## Build approach

Tracer Bullet: every slice is a vertical cut through database, API, and UI that works end to end; breadth is added to a thread that already runs.

## Commands

No scaffold exists yet. `/develop stack & architecture` creates it and fills in install, dev, build, and test commands here.

## Specs

Stored in `docs/specs/`. Format: `docs/specs/NNNN-title.md`.

## Rules

- Clean Architecture layering: routes call controllers, controllers call services, services call repositories. Dependencies point inward, never the reverse.
- Business logic (totals, numbering, status changes, tenant checks) lives in services and the shared document engine, never in controllers or components.
- Use cases and controllers stay thin orchestrators; no framework or ORM code appears in business logic modules.
- Cross boundary communication uses DTOs or plain objects; domain entities never leak into the presentation layer.
- TypeScript strict mode on, no `any`, exhaustive types on the document engine. Server and client folders follow the by layer layout sketched in PRODUCT_SPEC sections 46 and 48.
- Consistent error handling: one central backend error middleware; the frontend handles loading, empty, validation, server, and network states.
- Validate env vars at startup and fail fast with a clear message.
- Accessibility baseline WCAG AA on every screen: semantic HTML, keyboard navigation, labels, focus states, contrast. English and Bangla from day one.
- Conventional commit messages (feat, fix, chore, and so on).
- Document and application layers are unit tested; infrastructure is integration tested. Tenant isolation is tested explicitly.

## Tooling (chosen, installed by /develop tooling)

- Lint and format: ESLint plus Prettier on client and server
- Pre commit: lint, format, and typecheck on every commit
- Tests: unit plus integration with a framework chosen by `/test`
- CI: basic check on push (lint, typecheck, test)

## Git

- integration: on
- branch prefix: feat/
- commit: per-milestone

## Context files

<!-- Nested AGENTS.md files are listed here as they are created -->

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._