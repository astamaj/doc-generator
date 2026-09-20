# Business Document Generator

## 1. Product Overview

Build a modern, responsive SaaS web application that allows small businesses, freelancers, agencies, consultants, online sellers, and service providers to quickly create professional business documents.

The primary goal is:

> **Enter business and customer information once, generate a professional document in minutes, and download, print, or share it.**

The application should be significantly simpler than a full accounting or ERP system.

The MVP should focus on document creation, templates, customer/product reuse, PDF-ready output, and document history.

---

# 2. Target Users

Primary users:

- Small business owners
- Freelancers
- Consultants
- Agencies
- Online/F-commerce sellers
- Service providers
- Contractors
- Tutors
- Home-based businesses
- Small shops

Example use cases:

```text
A freelancer needs an invoice for a client.
A Facebook seller needs a quotation.
A small shop needs a receipt.
A contractor needs an estimate.
A business needs a purchase order.
A consultant needs a professional invoice.
```

---

# 3. Problem

Many small businesses create documents using:

- Microsoft Word
- Google Docs
- Excel
- Canva
- Manually edited PDF files
- Paper forms

Common problems include:

- Re-entering the same information
- Inconsistent formatting
- Calculation mistakes
- Unprofessional-looking documents
- Difficulty maintaining document history
- Difficulty finding previous invoices
- No reusable customer/product information
- Time wasted creating simple documents

This application should solve those problems through reusable business data and structured templates.

---

# 4. MVP Goal

A user should be able to:

```text
Create account
↓
Set up business
↓
Choose document type
↓
Select/create customer
↓
Add products/services
↓
Automatically calculate totals
↓
Choose template
↓
Preview document
↓
Download / Print / Share
↓
Save document
```

The MVP should make creating a professional document possible in approximately **1–3 minutes**.

---

# 5. MVP Document Types

Implement these document types first:

## 5.1 Invoice

Used to request payment from a customer.

Fields:

```text
Invoice Number
Issue Date
Due Date
Business
Customer
Items
Quantity
Unit Price
Discount
Tax
Subtotal
Delivery/Shipping
Other Charges
Grand Total
Amount Paid
Amount Due
Payment Terms
Notes
```

---

## 5.2 Quotation

Used to provide an estimated price before a customer places an order.

Fields:

```text
Quotation Number
Issue Date
Valid Until
Business
Customer
Items
Quantity
Unit Price
Discount
Tax
Subtotal
Other Charges
Grand Total
Terms & Conditions
Notes
```

---

## 5.3 Estimate

Similar to a quotation, but optimized for service/project estimates.

Fields:

```text
Estimate Number
Issue Date
Valid Until
Business
Customer
Project/Service
Description
Quantity
Rate
Subtotal
Discount
Tax
Total
Terms
Notes
```

---

## 5.4 Receipt

Used to confirm that payment was received.

Fields:

```text
Receipt Number
Payment Date
Business
Customer
Reference
Description
Amount
Payment Method
Transaction Reference
Notes
```

---

## 5.5 Purchase Order

Used by a business to order products/services from a supplier.

Fields:

```text
PO Number
Issue Date
Expected Delivery Date
Business
Supplier
Items
Quantity
Unit Price
Subtotal
Tax
Shipping
Other Charges
Grand Total
Delivery Address
Payment Terms
Notes
```

---

# 6. Future Document Types

Design the architecture so additional document types can be added without rewriting the entire application.

Future types:

```text
Credit Note
Debit Note
Delivery Note
Proforma Invoice
Sales Order
Work Order
Expense Voucher
Payment Voucher
Contract
Service Agreement
Proposal
Wedding/Event Quotation
Project Proposal
```

Do NOT implement these in the MVP unless the architecture naturally supports them.

---

# 7. Authentication

Implement:

```text
Register
Login
Logout
Forgot Password
Reset Password
Change Password
Profile
```

User fields:

```text
id
name
email
phone
password
avatar
createdAt
updatedAt
```

Use secure password hashing.

Authentication architecture should allow OAuth providers to be added later.

---

# 8. Business Profile

A user can create and manage their business profile.

Fields:

```text
Business Name
Business Logo
Business Email
Business Phone
Business Address
Website
Tax ID
Registration Number
Default Currency
Default Tax Rate
Payment Instructions
Footer Text
```

Optional:

```text
Facebook Page
WhatsApp Number
Bank Account Information
Mobile Payment Information
```

The business profile should be reusable across all documents.

---

# 9. Multi-Business Support

Architect the application as multi-tenant from the beginning.

A user may eventually own multiple businesses.

Business model:

```text
id
name
logo
email
phone
address
website
taxId
registrationNumber
currency
defaultTaxRate
paymentInstructions
footerText
createdAt
updatedAt
```

Every business-owned entity must contain:

```text
businessId
```

A user must only be able to access businesses they are authorized to access.

---

# 10. Customers

Create a reusable customer database.

Customer fields:

```text
id
businessId
name
companyName
email
phone
secondaryPhone
address
city
state
postalCode
country
taxId
notes
createdAt
updatedAt
```

Features:

```text
Create
Edit
Archive
Delete
Search
View
```

Customer profile should show:

```text
Customer information
Total documents
Total invoiced
Total paid
Outstanding amount
Recent documents
```

When creating a document, the seller should be able to search for and select an existing customer.

---

# 11. Suppliers

Purchase orders require suppliers.

Supplier fields:

```text
id
businessId
name
companyName
email
phone
address
city
state
postalCode
country
taxId
notes
createdAt
updatedAt
```

Features:

```text
Create
Edit
Archive
Delete
Search
```

---

# 12. Products & Services

Create a reusable catalog for items commonly used in documents.

Fields:

```text
id
businessId
name
sku
type
description
unit
price
cost
taxRate
isActive
createdAt
updatedAt
```

Type:

```text
Product
Service
```

Examples:

```text
Website Development
Logo Design
T-Shirt
Crochet Pattern
Consultation
Packaging
Delivery Service
```

When an item is selected, automatically populate its default price and tax rate.

The user must still be able to override the price for an individual document.

---

# 13. Document Numbering

Each document type should support automatic numbering.

Examples:

```text
INV-000001
INV-000002

QUO-000001
QUO-000002

EST-000001

REC-000001

PO-000001
```

Users should eventually be able to customize prefixes.

MVP should at least support automatic unique numbering.

Document numbers must never collide within a business.

---

# 14. Document Creation

The document creation workflow should be optimized for speed.

Example:

```text
Create Invoice
↓
Select Customer
↓
Add Items
↓
Adjust Quantity / Price
↓
Apply Discount
↓
Apply Tax
↓
Add Charges
↓
Add Payment Information
↓
Add Notes
↓
Select Template
↓
Preview
↓
Save
```

Do not force unnecessary fields.

---

# 15. Line Items

Each document can contain multiple line items.

Line-item fields:

```text
itemId
name
description
quantity
unit
unitPrice
discount
taxRate
lineSubtotal
lineTax
lineTotal
```

Automatically calculate:

```text
lineSubtotal = quantity × unitPrice

lineTotal =
lineSubtotal
- discount
+ tax
```

Allow the user to:

```text
Add item
Edit item
Delete item
Reorder item
```

---

# 16. Financial Calculations

Use a consistent calculation engine across all documents.

Example:

```text
Subtotal
- Discount
+ Tax
+ Shipping
+ Other Charges
= Grand Total
```

For invoices:

```text
Grand Total
- Amount Paid
= Amount Due
```

Do not duplicate calculation logic across frontend components.

Create a reusable calculation service/module.

---

# 17. Taxes

Support basic tax configuration.

Tax can be:

```text
No Tax
Percentage
Fixed Amount
```

Allow:

```text
Default business tax
Document-specific tax
Line-item tax
```

MVP should support one or more tax rates without attempting to become a full tax-accounting system.

Tax labels should be configurable.

Examples:

```text
VAT
Tax
Sales Tax
GST
```

---

# 18. Discounts

Support:

```text
Percentage Discount
Fixed Amount Discount
```

Allow:

```text
Document-level discount
Line-item discount
```

Validate that discounts cannot produce invalid totals.

---

# 19. Payment Information

Invoices and receipts should support:

```text
Payment Method
Transaction Reference
Amount Paid
Payment Date
Payment Terms
```

Payment methods:

```text
Cash
Bank Transfer
Card
bKash
Nagad
Rocket
Cash on Delivery
Other
```

Do not integrate payment gateways in MVP.

---

# 20. Currency

Default currency should support BDT (৳).

Architect the currency system to support:

```text
BDT
USD
EUR
GBP
INR
CAD
AUD
and other currencies later
```

Store currency with the document so historical documents do not change when business settings change.

Example:

```text
currencyCode: "BDT"
currencySymbol: "৳"
```

---

# 21. Document Status

Different document types may have different statuses.

Invoice:

```text
Draft
Sent
Partially Paid
Paid
Overdue
Cancelled
```

Quotation:

```text
Draft
Sent
Accepted
Rejected
Expired
Cancelled
```

Estimate:

```text
Draft
Sent
Accepted
Rejected
Expired
```

Receipt:

```text
Draft
Issued
Cancelled
```

Purchase Order:

```text
Draft
Sent
Confirmed
Received
Cancelled
```

Status history should be stored.

---

# 22. Document History

Create a **Documents** page containing all saved documents.

Columns/cards should include:

```text
Document Number
Type
Customer/Supplier
Date
Due Date
Total
Status
Created By
Actions
```

Actions:

```text
View
Edit
Duplicate
Download
Print
Share
Archive
Delete
```

---

# 23. Dashboard

Create a simple business dashboard.

Show:

```text
Documents Created
Invoices
Quotations
Receipts
Purchase Orders
```

For invoices:

```text
Total Invoiced
Total Paid
Outstanding
Overdue
```

Also show:

```text
Recent Documents
Recent Customers
Most Used Items
```

The dashboard should remain lightweight.

Do not turn the MVP into an accounting dashboard.

---

# 24. Templates

One of the most important features is document templates.

MVP should include at least:

```text
Template 1 — Clean
Template 2 — Professional
Template 3 — Minimal
```

Templates should support:

```text
Business Logo
Business Information
Customer Information
Document Title
Document Number
Date
Line Items
Totals
Payment Information
Terms
Notes
Footer
```

Template selection should happen before final preview.

---

# 25. Template Architecture

Do NOT hard-code every document page separately.

Create a reusable document rendering system.

Conceptually:

```text
Document Data
      ↓
Document Renderer
      ↓
Template
      ↓
Rendered Document
```

For example:

```text
Invoice Data
+
Professional Template
=
Professional Invoice
```

This allows new templates to be added later.

---

# 26. Customization

MVP customization:

```text
Logo
Business details
Primary theme/accent
Font preference
Footer
Notes
Terms
```

Potential future customization:

```text
Custom colors
Custom fields
Custom columns
Drag-and-drop layout
Custom template builder
```

Do not build a complex drag-and-drop editor in MVP.

---

# 27. Live Preview

While creating a document, provide a live preview.

Desktop:

```text
Form/editor        Preview
-----------        -------
Customer           Invoice
Items              ...
Totals             ...
```

Mobile:

```text
Editor
↓
Preview
```

The preview must update automatically when document data changes.

---

# 28. PDF Generation

The user must be able to generate a professional PDF document.

Required:

```text
Download PDF
Print PDF
```

The generated document must:

- Have correct spacing
- Handle long business/customer names
- Support multiple line items
- Break across pages correctly
- Preserve totals
- Preserve document numbering
- Render logos correctly
- Render on A4 paper

Do not rely solely on browser screenshots.

Use a reliable HTML-to-PDF or PDF rendering strategy.

Abstract the PDF generation behind a service.

---

# 29. Print Support

Provide a print-friendly view.

Requirements:

```text
A4 layout
Print margins
No dashboard navigation
No buttons in printed document
Correct page breaks
```

The printable document should look like the generated PDF.

---

# 30. Sharing

Provide:

```text
Share
Copy Document Link
Copy Document Summary
Web Share API
```

On supported devices, use the Web Share API.

Fallback:

```text
Copy link
Copy document information
Download PDF
```

---

# 31. Public Document Sharing

Architecture should support public document links later.

Example:

```text
/business/invoice/public/abc123xyz
```

A public document should show only customer-facing information.

Do not expose:

```text
Internal notes
Internal costs
User data
Private metadata
```

MVP may keep public links optional, but the document model should be designed for them.

---

# 32. Duplicate Documents

Provide a **Duplicate** action.

Example:

```text
Invoice INV-000123
↓
Duplicate
↓
Create new draft
↓
New document number
```

The copied document must never overwrite the original.

Useful for recurring invoices and repeated quotations.

---

# 33. Draft Documents

Users should be able to save incomplete documents.

Drafts must:

```text
Be editable
Not be treated as finalized
Not automatically be considered sent
Have no accidental document-number collision
```

The user should be able to:

```text
Save Draft
Continue Editing
Delete Draft
Finalize
```

---

# 34. Auto-Save

Consider autosaving draft documents.

Recommended behavior:

```text
User edits
↓
Debounced save
↓
Draft updated
```

Do not autosave finalized documents without explicit user action.

---

# 35. Search

Search documents using:

```text
Document Number
Customer Name
Customer Phone
Supplier Name
```

Search products/services using:

```text
Name
SKU
```

Search should be debounced.

---

# 36. Filters

Documents:

```text
Document Type
Status
Customer
Date Range
Amount Range
```

Customers:

```text
Name
Phone
Email
```

Products:

```text
Type
Category
Active
```

---

# 37. Pagination

Use server-side pagination.

Default:

```text
20 documents/page
```

API should return:

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNextPage": true,
    "hasPreviousPage": false
  }
}
```

---

# 38. Archive Instead of Hard Delete

Important business records should generally be archived rather than immediately deleted.

For example:

```text
isArchived
archivedAt
archivedBy
```

Deletion should require confirmation.

Future versions may implement a recycle bin.

---

# 39. Audit Log

Track important operations:

```text
Document Created
Document Updated
Document Finalized
Document Status Changed
Document Duplicated
Document Archived
Payment Added
Customer Created
Product Created
```

Audit fields:

```text
id
businessId
userId
action
entityType
entityId
metadata
createdAt
```

---

# 40. User Roles

MVP:

```text
Owner
Staff
```

Owner:

```text
Full access
Business settings
Templates
Documents
Customers
Products
Reports
Team management
```

Staff:

```text
Create documents
View documents
Manage customers
Manage products
```

Permissions must be enforced on the backend.

---

# 41. Settings

## Business Settings

```text
Business information
Logo
Address
Currency
Tax
Payment details
```

## Document Settings

```text
Document prefixes
Numbering
Default notes
Default terms
Default payment method
```

## Template Settings

```text
Default invoice template
Default quotation template
Default receipt template
Default purchase order template
```

---

# 42. Localization

MVP should be English-first.

Architect the frontend so localization can be added later.

Future:

```text
Bangla
English
Hindi
Arabic
```

Do not hard-code UI text throughout business logic.

Use centralized translation keys.

---

# 43. Accessibility

Follow basic accessibility standards.

Requirements:

- Semantic HTML
- Keyboard navigation
- Proper labels
- Accessible form validation
- Sufficient contrast
- Focus states
- Accessible modals
- Screen-reader-friendly controls

---

# 44. Responsive Design

The product must be fully responsive.

Desktop:

```text
Sidebar + Main Content
```

Tablet:

```text
Compact Sidebar + Main Content
```

Mobile:

```text
Top Header
Main Content
Bottom Navigation / Mobile Menu
```

Document creation should remain comfortable on mobile.

---

# 45. UI Design

Visual style:

```text
Modern
Professional
Minimal
Fast
Trustworthy
```

Avoid making it look like accounting software from the 1990s.

Use:

```text
Cards
Tables
Badges
Dialogs
Dropdowns
Tabs
Forms
Toasts
Skeleton loaders
Empty states
```

Use consistent spacing and typography.

---

# 46. Frontend Technology

Recommended:

```text
Nextjs
TypeScript
Tailwind CSS/shadcn UI
TanStack Query
React Hook Form
Zod
```

Use reusable components.

Recommended structure:

```text
client/
├── src/
│   ├── components/
│   ├── layouts/
│   ├── pages/
│   ├── features/
│   ├── hooks/
│   ├── api/
│   ├── services/
│   ├── schemas/
│   ├── types/
│   ├── utils/
│   └── router/
```

---

# 47. Backend Technology

Recommended:

```text
Node.js
TypeScript
Express.js
PostgreSQL
drizzle ORM
```

Architecture:

```text
Routes
↓
Controllers
↓
Services
↓
Repositories / Models
```

Business logic belongs in services.

Controllers should remain thin.

---

# 48. Suggested Backend Structure

```text
server/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── services/
│   ├── repositories/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── validators/
│   ├── utils/
│   ├── types/
│   └── app.ts
```

Potential modules:

```text
auth
businesses
customers
suppliers
products
documents
payments
templates
files
auditLogs
```

---

# 49. Document Data Model

Create a flexible document model.

Conceptually:

```text
Document
├── businessId
├── type
├── number
├── status
├── customerId
├── supplierId
├── issueDate
├── dueDate
├── validUntil
├── currency
├── items[]
├── subtotal
├── discount
├── tax
├── shipping
├── otherCharges
├── total
├── amountPaid
├── amountDue
├── paymentMethod
├── paymentReference
├── notes
├── terms
├── templateId
├── metadata
├── createdBy
├── createdAt
└── updatedAt
```

Not every field is required for every document type.

Use document-type validation.

---

# 50. Historical Data Integrity

A document must contain snapshots of important values.

For example, if a product price changes later, an old invoice must remain unchanged.

Store:

```text
productName
description
sku
unitPrice
taxRate
```

inside the document line item.

Similarly, store customer information necessary for historical document rendering.

Do not depend entirely on current customer/product records.

---

# 51. File Storage

Support business logo uploads.

Abstract file storage.

Possible providers:

```text
Cloudflare R2
```

Do not tightly couple the application to a single provider.

---

# 52. API Design

Recommended REST endpoints:

```text
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/me

GET    /api/businesses
POST   /api/businesses
GET    /api/businesses/:id
PATCH  /api/businesses/:id

GET    /api/customers
POST   /api/customers
GET    /api/customers/:id
PATCH  /api/customers/:id
DELETE /api/customers/:id

GET    /api/suppliers
POST   /api/suppliers
GET    /api/suppliers/:id
PATCH  /api/suppliers/:id
DELETE /api/suppliers/:id

GET    /api/products
POST   /api/products
GET    /api/products/:id
PATCH  /api/products/:id
DELETE /api/products/:id

GET    /api/documents
POST   /api/documents
GET    /api/documents/:id
PATCH  /api/documents/:id
DELETE /api/documents/:id

POST   /api/documents/:id/duplicate
POST   /api/documents/:id/finalize
POST   /api/documents/:id/archive
PATCH  /api/documents/:id/status

GET    /api/templates
GET    /api/templates/:id

GET    /api/dashboard
```

---

# 53. API Response Format

Success:

```json
{
  "success": true,
  "data": {},
  "message": "Document created successfully"
}
```

Error:

```json
{
  "success": false,
  "message": "Validation failed",
  "errors": []
}
```

Use consistent API responses.

---

# 54. Security

Implement:

- Password hashing
- Authentication middleware
- Authorization middleware
- Tenant isolation
- Server-side validation
- Rate limiting where appropriate
- CORS
- Secure headers
- Request size limits
- Safe file upload validation
- Proper error handling

Never trust client-supplied:

```text
businessId
userId
role
permissions
document ownership
```

Determine these on the server.

---

# 55. Tenant Isolation

This is critical.

Example:

```text
Business A
  ├── Customer A
  ├── Product A
  └── Invoice A

Business B
  ├── Customer B
  ├── Product B
  └── Invoice B
```

Business A must never be able to:

```text
GET Business B invoice
EDIT Business B customer
DELETE Business B product
ACCESS Business B template
```

Test tenant isolation explicitly.

---

# 56. Validation

Use client and server validation.

Examples:

```text
Document number unique
Valid dates
Due date not invalid
Positive quantities
Valid prices
Valid discount
Valid tax
Valid customer
Valid supplier
Valid currency
```

For document types, validate required fields according to the specific type.

---

# 57. Error Handling

Centralized backend error middleware.

Frontend must handle:

```text
Loading
Success
Empty
Validation Error
Server Error
Network Error
Unauthorized
Forbidden
Not Found
```

Never expose internal stack traces to users.

---

# 58. Performance

Optimize for thousands of documents.

Use:

```text
Database indexes
Server-side pagination
Server-side filtering
Server-side sorting
Debounced search
Lazy loading
Efficient aggregation queries
```

Suggested indexes:

```text
businessId
businessId + documentNumber
businessId + type
businessId + status
businessId + customerId
businessId + createdAt
businessId + issueDate
```

---

# 59. Testing

Test the most important business logic.

## Authentication

```text
Register
Login
Invalid login
Protected routes
```

## Documents

```text
Create invoice
Calculate totals
Create quotation
Create receipt
Create purchase order
Duplicate document
Finalize document
Archive document
```

## Calculations

```text
Subtotal
Discount
Tax
Shipping
Grand total
Amount paid
Amount due
```

## Tenant Isolation

Verify:

```text
Business A cannot access Business B data.
```

## PDF

Test:

```text
Correct totals
Long item names
Multiple pages
Logo rendering
Correct document number
```

---

# 60. Demo Data

Create development seed data.

Include:

```text
Demo Business
Demo User
Demo Customers
Demo Supplier
Demo Products
Demo Services
Demo Invoices
Demo Quotations
Demo Receipts
Demo Purchase Orders
```

Provide:

```bash
npm run seed
```

---

# 61. Environment Variables

Create `.env.example`.

Example:

```text
NODE_ENV=
PORT=
MONGODB_URI=
JWT_SECRET=
JWT_EXPIRES_IN=
CLIENT_URL=

STORAGE_PROVIDER=
STORAGE_API_KEY=
STORAGE_API_SECRET=

PDF_PROVIDER=
```

Never commit actual secrets.

---

# 62. Suggested Routes

```text
/auth/login
/auth/register

/dashboard

/documents
/documents/new
/documents/:id
/documents/:id/edit

/customers
/customers/:id

/suppliers
/suppliers/:id

/products
/products/:id

/templates

/settings/business
/settings/documents
/settings/templates
/settings/account
```

---

# 63. Document Creation UI

The creation page should be divided into logical sections.

Example:

```text
Document Type
────────────────────
Invoice

Customer
────────────────────
[ Search Customer ]

Document Details
────────────────────
Number
Issue Date
Due Date

Items
────────────────────
Item | Qty | Price | Discount | Tax | Total
+ Add Item

Totals
────────────────────
Subtotal
Discount
Tax
Shipping
Grand Total

Payment
────────────────────
Payment Method
Amount Paid
Reference

Notes
────────────────────
Notes
Terms

Template
────────────────────
Clean | Professional | Minimal

[ Save Draft ] [ Preview ] [ Finalize ]
```

---

# 64. Preview UI

Preview should look exactly like the printable document.

Show:

```text
Business Logo
Business Information

INVOICE
INV-000123

Customer Information

Items Table

Subtotal
Discount
Tax
Total
Paid
Due

Payment Information

Notes
Terms
Footer
```

Provide:

```text
Back to Edit
Download PDF
Print
Share
```

---

# 65. Template Design Requirements

Every template should support:

```text
Header
Logo
Business Information
Customer Information
Document Information
Items
Totals
Payment Information
Notes
Terms
Footer
```

Long content should wrap naturally.

Do not allow text overflow to break the layout.

---

# 66. Document Templates and Branding

Business branding should be reusable.

Example:

```text
Business Settings
      ↓
Logo + Name + Contact + Theme
      ↓
All Documents
```

The user should not need to re-enter business information for each document.

---

# 67. SaaS Considerations

The application should be designed for eventual subscription plans.

Potential plans:

```text
Free
Starter
Business
Professional
```

Potential limits:

```text
Documents/month
Customers
Products
Templates
Team Members
Storage
```

Do not build full subscription billing in the MVP.

Add extensible fields such as:

```text
plan
subscriptionStatus
subscriptionStart
subscriptionEnd
usageLimits
```

---

# 68. Freemium Strategy

A possible future model:

### Free

```text
Limited documents/month
Basic templates
Watermark
Single business
```

### Starter

```text
More documents
All templates
No watermark
Custom branding
```

### Business

```text
Unlimited documents
Team members
Advanced templates
Public links
```

### Professional

```text
Multiple businesses
Advanced customization
API
Automation
Integrations
```

Billing is NOT part of the MVP.

---

# 69. MVP Exclusions

Do NOT build these initially:

- Full accounting
- Expense management
- Inventory management
- Payroll
- Bank reconciliation
- Tax filing
- Payment gateway processing
- Automatic bank transaction imports
- Full CRM
- Full ERP
- Complex workflow automation
- AI document generation
- OCR
- E-signature
- Advanced template editor
- Mobile native apps
- Third-party accounting integrations

These can become future products/features.

---

# 70. Future Features

Phase 2 possibilities:

```text
Recurring invoices
Automatic payment reminders
Email document delivery
WhatsApp document sharing
Public document links
Online invoice payment
Customer portal
E-signatures
Digital signatures
Advanced reporting
Multiple businesses
Team management
Custom document fields
Custom template builder
```

Phase 3:

```text
AI invoice creation
AI quotation generation
AI proposal generation
OCR receipt scanning
Accounting integrations
Payment gateway integrations
Automatic reminders
CRM
Inventory
Expense management
```

---

# 71. Example Workflow — Freelancer

A freelancer receives a project request.

They create:

```text
Quotation
```

Customer:

```text
ABC Company
```

Items:

```text
Website Design       1 × ৳30,000
Development          1 × ৳50,000
Hosting Setup        1 × ৳5,000
```

Application calculates:

```text
Subtotal = ৳85,000
Discount = ৳5,000
Tax = ৳0
Total = ৳80,000
```

Freelancer selects:

```text
Professional Template
```

Then:

```text
Preview
↓
Save
↓
Download PDF
↓
Send to customer
```

Later, after the quotation is accepted:

```text
Duplicate / Convert workflow
↓
Invoice
```

Conversion can be implemented in a future phase.

---

# 72. Example Workflow — Small Online Seller

Seller needs to send a quotation for bulk products.

They select:

```text
Quotation
```

Customer:

```text
Retail Shop
```

Items:

```text
Product A × 50
Product B × 100
Product C × 25
```

The application calculates all totals and generates a professional PDF.

The seller downloads the document and shares it with the customer.

---

# 73. Example Workflow — Payment Receipt

Customer pays ৳10,000.

User creates:

```text
Receipt
```

Information:

```text
Customer: Rahim Traders
Amount: ৳10,000
Method: Bank Transfer
Reference: TXN123456
Purpose: Website Development — First Payment
```

The application generates:

```text
RECEIPT
REC-000023
```

with a professional layout.

---

# 74. Definition of Done — MVP

The MVP is complete when a user can:

1. Create an account.
2. Create a business profile.
3. Upload a business logo.
4. Create customers.
5. Create suppliers.
6. Create products/services.
7. Create invoices.
8. Create quotations.
9. Create estimates.
10. Create receipts.
11. Create purchase orders.
12. Add multiple items to documents.
13. Automatically calculate totals.
14. Apply discounts and taxes.
15. Record payment information.
16. Save documents as drafts.
17. Finalize documents.
18. Search and filter documents.
19. Duplicate documents.
20. Archive documents.
21. Select document templates.
22. Preview documents.
23. Download PDF documents.
24. Print documents.
25. Share document information.
26. View document history.
27. Use the application comfortably on mobile.
28. Keep each business's data completely isolated.
29. Recover from validation and server errors gracefully.

---

# 75. Claude Code Implementation Instructions

Do not attempt to generate the entire application blindly in one pass.

Implement the project in phases.

## Phase 1 — Foundation

Create:

```text
Frontend
Backend
TypeScript
ESLint
Prettier
Environment configuration
Git configuration
README
Basic routing
Basic API setup
```

---

## Phase 2 — Authentication & Business

Implement:

```text
User
Business
Authentication
Authorization
Tenant isolation
Business settings
```

---

## Phase 3 — Reusable Data

Implement:

```text
Customers
Suppliers
Products
Services
```

---

## Phase 4 — Document Engine

Implement:

```text
Document model
Document types
Document numbering
Line items
Financial calculations
Validation
Drafts
Finalization
Status management
```

---

## Phase 5 — Templates

Implement:

```text
Template system
Template selection
Live preview
Printable rendering
```

Create at least three professional templates.

---

## Phase 6 — PDF

Implement:

```text
PDF rendering service
Download
Print
Correct A4 layout
Page breaks
```

Test documents with:

```text
Short content
Long content
Many items
Long names
Multiple pages
```

---

## Phase 7 — Document Management

Implement:

```text
Search
Filtering
Pagination
Duplicate
Archive
History
Status
```

---

## Phase 8 — Dashboard

Implement:

```text
Document counts
Invoice totals
Paid
Outstanding
Recent documents
Recent customers
```

---

## Phase 9 — Responsive UX

Optimize:

```text
Desktop
Tablet
Mobile
Document creation
Preview
Tables
Forms
Navigation
```

---

## Phase 10 — Testing

Test:

```text
Authentication
Tenant isolation
Document creation
Calculations
Validation
Numbering
PDF generation
Status management
Permissions
```

---

## Phase 11 — Documentation

Complete:

```text
README.md
Architecture documentation
API documentation
Database documentation
Environment setup
Deployment instructions
```

---

# 76. Coding Rules for Claude Code

Follow these rules throughout development:

- Use TypeScript strict mode.
- Avoid `any`.
- Keep business logic outside controllers/components.
- Create reusable calculation functions.
- Create reusable document rendering components.
- Avoid duplicated validation logic.
- Keep API responses consistent.
- Use meaningful names.
- Keep files reasonably small.
- Prefer composition over giant components.
- Handle loading, empty, and error states.
- Validate on both frontend and backend.
- Never trust client-supplied ownership information.
- Never expose secrets.
- Never allow cross-tenant access.
- Do not introduce unnecessary dependencies.
- Do not build features outside the defined MVP without a clear architectural reason.

---

# 77. Critical Architectural Principle

The most important part of this product is the **document engine**.

Avoid implementing:

```text
Invoice
Quotation
Receipt
Purchase Order
```

as four completely unrelated systems.

Instead, build:

```text
Shared Document Core
        ↓
Document Type Configuration
        ↓
Template Renderer
        ↓
PDF / Print Renderer
```

Conceptually:

```text
Document
├── type = invoice
├── type = quotation
├── type = receipt
└── type = purchase_order
```

Each type defines:

```text
Required fields
Available fields
Statuses
Calculation rules
Labels
Template behavior
```

This architecture makes future document types much easier to add.

---

# 78. Final Product Principle

This application is NOT intended to replace accounting software.

Its core promise is:

> **Create professional business documents quickly, accurately, and consistently.**

Every feature should be evaluated against that goal.

Prioritize:

```text
Speed
Simplicity
Professional appearance
Reusable data
Accurate calculations
Reliable PDF output
Mobile usability
Security
Tenant isolation
```

Avoid unnecessary complexity until real users demonstrate that the feature is needed.
