# Scope: Business Document Generator

A SaaS web app for small businesses, freelancers, agencies, and online sellers: enter business and customer information once, generate a professional invoice, quotation, estimate, receipt, or purchase order in minutes, then download, print, or share it. Simpler than accounting software, focused on fast and correct documents.

**Build approach:** Tracer Bullet (every slice is a vertical cut through database, API, and UI that works end to end; breadth is added to a thread that already runs).
**Workflow:** GA (after /develop, run /check verify, /test, a fresh model /check review, then /document). The project default level of rigor. `/architect` is the recommended first stop for a feature with a real decision, but skippable when you already know the build. Any feature can carry its own tag (e.g. `· Beta`) to do more or less.

_These are recommendations to keep your build orderly, not requirements. Skip anything that does not fit: if you already know how to build a feature, use `/develop` and skip `/architect`. You decide when a feature is `done`._

Planning decisions recorded 2026-09-20: full five document type MVP; solo build with no fixed deadline; success metric is documents created per week; no product analytics or error monitoring in the MVP; Owner and Staff roles, auto save, audit log, and public document links are all in scope; English and Bangla from day one; accessibility per the PRODUCT_SPEC checklist; performance for small but real load (hundreds of users, thousands of documents per business).

## At a glance

| # | Feature | Phase | Status |
|---|---------|-------|--------|
| 1 | Stack & architecture | Foundation | in-progress |
| 2 | Coding standards & tooling | Foundation | planned |
| 3 | Data model | Foundation | planned |
| 4 | Design system & UI foundation | Foundation | planned |
| 5 | Core invoice loop | Slice 1 | planned |
| 6 | Customer book | Slice 2 | planned |
| 7 | Product & service catalog | Slice 2 | planned |
| 8 | Supplier book | Slice 2 | planned |
| 9 | Quotations | Slice 3 | planned |
| 10 | Estimates | Slice 3 | planned |
| 11 | Receipts | Slice 3 | planned |
| 12 | Purchase orders | Slice 3 | planned |
| 13 | Templates | Slice 4 | planned |
| 14 | PDF generation | Slice 4 | planned |
| 15 | Print | Slice 4 | planned |
| 16 | Share | Slice 4 | planned |
| 17 | Search, filter, pagination | Slice 5 | planned |
| 18 | Duplicate & archive | Slice 5 | planned |
| 19 | Auto save | Slice 5 | planned |
| 20 | Public document links | Slice 5 | planned |
| 21 | Dashboard | Slice 6 | planned |
| 22 | Roles & team | Slice 6 | planned |
| 23 | Audit log | Slice 6 | planned |

## Foundations

### 1. Stack & architecture
Decide the stack once and scaffold a runnable project so every later slice builds on real structure. PRODUCT_SPEC.md recommends a stack; `/architect` confirms or revises that call and records it as the one place tools and frameworks are chosen.
**Done when:** the stack is recorded in a spec and the empty scaffold boots locally and passes build.
- [x] Decide the stack (spec): `/architect stack & architecture`
- [ ] Scaffold from the decision: `/develop stack & architecture`

spec [0001](../specs/0001-stack-architecture.md)

### 2. Coding standards & tooling
Capture conventions and install lint, format, strict types, pre-commit checks, and CI from the real scaffolded project, not guesses.
**Done when:** root `AGENTS.md` reflects the real stack, and lint, format, and pre-commit run clean.
- [ ] Capture conventions + tooling choices: `/audit`
- [ ] Install the tooling: `/develop tooling`

### 3. Data model · needs a decision
The entities every feature builds on: users, businesses, memberships, customers, suppliers, products, documents, line items, status history, audit entries. Multi tenant from day one: every business owned row carries a businessId, and documents snapshot the item prices, tax, and customer details they were issued with, so history never changes under later edits.
**Done when:** the model supports numbering, snapshots, roles, public links, and future document types without a breaking migration, with indexes sized for thousands of documents per business.
- [ ] Design it (spec): `/architect data model`

### 4. Design system & UI foundation · needs a decision
Visual language, responsive app shell (desktop sidebar, compact sidebar, mobile header), and base components, with English and Bangla text and the PRODUCT_SPEC accessibility checklist (semantic HTML, keyboard, labels, focus, contrast) built in from the start.
**Done when:** `design.md` covers type, color, spacing, components; base components handle keyboard and focus; Bangla and English both render correctly.
- [ ] Design it (spec): `/architect design system & UI foundation`

## Slice 1: Core invoice loop

### 5. Core invoice loop · needs a decision
The thinnest real thread through every layer, and the walking skeleton: register, set up the business with currency and tax defaults, create a customer inline, build an invoice with line items and correct totals, get an automatic number, preview it on the Clean template, save as draft or finalize, and find it in document history. Real auth, real database, real UI, narrow breadth. The shared document engine (one core, per type configuration) is born here, so later document types are configuration, not rewrites.
**Done when:** a new owner goes from account to a saved, correctly calculated invoice in one sitting, one business's data never visible to another.
- [ ] Design it (spec): `/architect core invoice loop`

## Slice 2: Reusable data

### 6. Customer book · needs a decision
The full customers page so the same people are never re-entered: list, create, edit, search, archive, and a customer profile with document count, invoiced, paid, and outstanding.
**Done when:** the user manages customers end to end and picks or searches an existing customer while creating a document.
- [ ] Design it (spec): `/architect customer book`

### 7. Product & service catalog
Reusable items with default price and tax rate; choosing an item in a document fills both automatically, and the price can be overridden per document.
**Done when:** the user manages products and services, and adding an item in a document pulls its defaults while accepting overrides.
- [ ] Build it: `/develop product & service catalog`

### 8. Supplier book
The supplier counterpart of customers, so purchase orders have someone to address.
**Done when:** the user can create, edit, search, and archive suppliers on the same pattern as customers.
- [ ] Build it: `/develop supplier book`

## Slice 3: Remaining document types

### 9. Quotations
The quotation type on the shared engine: its fields, QUO numbering, its statuses, and per type validation.
**Done when:** the user can create, preview, save, and manage quotations with correct totals and their own lifecycle.
- [ ] Build it: `/develop quotations`

### 10. Estimates
The estimate type for project and service estimates.
**Done when:** estimates work on the engine with their own fields, numbering, and statuses.
- [ ] Build it: `/develop estimates`

### 11. Receipts
Payment confirmation: amount, payment method, transaction reference, issued status.
**Done when:** the user can issue a receipt recording how and when payment arrived.
- [ ] Build it: `/develop receipts`

### 12. Purchase orders
Orders addressed to suppliers: delivery address, expected delivery date, payment terms.
**Done when:** the user can create a purchase order to a supplier with correct totals.
- [ ] Build it: `/develop purchase orders`

## Slice 4: Output

### 13. Templates · needs a decision
Professional and Minimal join Clean on the renderer built in slice 1, with template selection before preview and a default template per document type in settings.
**Done when:** all three templates render every document type without breaking on long names or many items, and each business's defaults are remembered.
- [ ] Design it (spec): `/architect templates`

### 14. PDF generation · needs a decision
Download a professional A4 PDF behind a service abstraction: correct spacing, page breaks, logos, totals, and numbering, for Bangla and English alike.
**Done when:** short, long, many item, and long name documents all produce correct multi page PDFs, and the provider sits behind a service that can be swapped.
- [ ] Design it (spec): `/architect PDF generation`

### 15. Print
A print friendly view of the same rendering: A4, margins, correct page breaks, no app chrome or buttons.
**Done when:** printing from the browser yields a document that matches the PDF.
- [ ] Build it: `/develop print`

### 16. Share
Web Share API where the device supports it, with copy link and copy summary as fallbacks.
**Done when:** the user can share a document from a phone, and copy its link or details anywhere else.
- [ ] Build it: `/develop share`

## Slice 5: Document management

### 17. Search, filter, pagination
Find documents fast as they accumulate: search by number, customer or supplier name, phone; filter by type, status, customer, date range, amount; server side pages of 20.
**Done when:** results stay fast and correct over thousands of documents, and search is debounced.
- [ ] Build it: `/develop search, filter, pagination`

### 18. Duplicate & archive
Duplicate creates a new draft with a fresh number and never touches the original; archive is the default removal, and real deletion needs confirmation.
**Done when:** duplicate and archive behave this way for every document type.
- [ ] Build it: `/develop duplicate & archive`

### 19. Auto save
Draft edits save themselves with a debounced save; finalized documents change only on explicit action.
**Done when:** closing the browser mid edit loses nothing in a draft, and no finalized document is ever changed silently.
- [ ] Build it: `/develop auto save`

### 20. Public document links · needs a decision
An optional public link per document rendering only customer facing fields; internal notes, costs, and private metadata are never exposed.
**Done when:** a public link shows a clean customer view and nothing private leaks, by design.
- [ ] Design it (spec): `/architect public document links`

## Slice 6: Oversight

### 21. Dashboard
A light overview: counts by document type, invoiced, paid, outstanding, overdue, recent documents, recent customers, most used items. Deliberately not an accounting dashboard.
**Done when:** the dashboard numbers match the business's documents.
- [ ] Build it: `/develop dashboard`

### 22. Roles & team · needs a decision
Owner and Staff with permissions enforced on the backend; the owner adds staff to the business.
**Done when:** staff can do their allowed work and are refused the rest, on the server, for every route.
- [ ] Design it (spec): `/architect roles & team`

### 23. Audit log · needs a decision
The activity trail per business: document created, updated, finalized, status changed, duplicated, archived, payment recorded, customer and product created.
**Done when:** those actions land in the audit record with actor and time, per business.
- [ ] Design it (spec): `/architect audit log`

## Deferred
Out of scope for this build pass, kept so the plan stays honest.
- **Analytics & error monitoring**: product analytics and error tracking, serving the documents per week metric · needs a decision
- **Billing & plans**: subscription tiers and limits; plan and usage fields are already in the model · needs a decision
- **Future document types**: credit note, debit note, delivery note, proforma invoice, sales order, contract, proposal
- **Document conversion**: quotation to invoice and similar flows
- **Delivery**: email and WhatsApp sending, payment reminders, recurring invoices
- **Customer facing extras**: customer portal, online invoice payment, e-signatures
- **Sign in providers**: OAuth sign in (the auth architecture keeps room for it)
- **Template builder**: custom colors, custom fields, drag and drop layout
- **Beyond**: native mobile apps, accounting integrations, AI features

## Legend

**The decision box.** Every feature carries exactly one, the sub task whose label ends with `(spec)`. Its wording varies (`Design it (spec)` normally, `Decide the stack (spec)` on Stack & architecture), so skills locate it by that `(spec)` suffix, never by an exact label. Every other box is an execution box and `/architect` never ticks one.

**Feature lifecycle**: the scope updates as a feature moves; each row is what it shows and who sets it:

| State | Set by | The feature shows |
|---|---|---|
| `planned` · needs a decision | `/scope` | one box: `Design it (spec): /architect <feature>` |
| `in-progress` (designed) | **`/architect` at spec capture** | `Design it` ticked; spec linked; `Build it: /develop <feature>` + **2 to 5 milestones**; the tier's closing boxes (`Verify it` Alpha+, `Test it` Beta+, `Review it` + `Document it` GA); any surfaced follow-up enrolled |
| `in-progress` (building) | `/develop` | milestone sub boxes tick one by one; code pointer filled |
| `in-progress` (verified) | `/check verify` | `Build it` + milestones ticked; `Verify it` ticked |
| `done` | **you, when you decide it is** (any skill sets it when you say so); `/sync` reconciles | boxes you ran ticked, skipped ones marked skipped; for GA, after `/test` is the suggested point to call it done |

- **Next step** = the first unticked box (always a command or a tracked milestone).
- **needs a decision** = run `/architect` first; otherwise straight to `/develop` (or `/audit` for standards & tooling). The tag drops once the spec is captured.
- **Atomic build tasks live in the spec's `## Build plan`, not here**: the scope carries only the milestone rollup.
- **Status** `planned` → `in-progress` → `done`, plus `existing` (pre workflow) and `dropped` (de scoped, kept for history).
- **Approach tag** beside a heading (e.g. `· Facade`) overrides the project default for that feature; no tag = inherits it.
- **Workflow tier tag** beside a heading (e.g. `· Beta`) sets that one feature's rigor above or below the project default; no tag inherits the default. It decides the feature's check boxes and each skill's next suggestion.
- **Workflow** (header line) is the project default, what runs after `/develop`: **Prototype** = nothing; **Alpha** = `/check verify`; **Beta** = `/check verify` then `/test`; **GA** = adds a fresh model `/check review` then `/document`. A feature built on an unratified decision (an `Assumed` spec) stays flagged, but that never blocks `done`.
- **Pointer line** (`spec <n> · code in <path>`): the spec link added by `/architect`, the code path by `/develop`.