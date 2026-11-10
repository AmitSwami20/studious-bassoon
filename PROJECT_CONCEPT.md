# 3PL BILLING AUDIT ENGINE — PROJECT CONCEPT & SPECIFICATION
**Version 1.0 | Confidential | For Internal Evaluation Only**

---

## 1. EXECUTIVE SUMMARY

### 1.1 Product Vision
**3PL Billing Audit Engine** is a B2B SaaS platform that automatically audits third-party logistics (3PL) fulfillment invoices against contracted rates and actual operational data, generating evidence-backed dispute packets that recover 8–18% of fulfillment spend for e-commerce brands.

### 1.2 The Problem
E-commerce brands ($5M–$500M GMV) outsource fulfillment to 3PLs (ShipBob, ShipMonk, Quiet Logistics, Ryder, 500+ providers). **3PL invoices systematically overcharge brands 8–18% monthly** through:
- Storage billing inflation (pallet positions vs. actual cubic feet)
- Peak surcharges applied outside contracted windows
- Carrier discount retention (3PL negotiates 20% UPS, passes 12%)
- Receiving reclassification (palletized → floor-loaded at 2× rate)
- Minimum shortfall fees on unrealistic minimums

**Why brands can't fight back:** Invoices are 50-page PDFs with cryptic codes. Contracts are 30-page PDFs with buried rate tables. No internal bandwidth to line-item audit 200+ charges × 12 months. 3PLs hold inventory hostage during disputes.

### 1.3 The Solution
**Contract-to-Code Compiler + Three-Way Reconciliation Engine**

```
3PL Contract (PDF)     Monthly Invoice (PDF/CSV)     Brand Actuals (Shopify/WMS)
        │                        │                           │
        ▼                        ▼                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    AST COMPILER (libpg_query)                       │
│  • Parses contract rate tables → executable TypeScript functions   │
│  • Handles: tiered storage, volume discounts, surcharge caps       │
│  • Output: Deterministic, auditable, version-controlled pricing    │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
    ┌─────────┐       ┌─────────┐       ┌─────────┐
    │ STORAGE │       │ PICK/   │       │ SHIPPING│
    │ ENGINE  │       │ PACK    │       │ ENGINE  │
    └────┬────┘       └────┬────┘       └────┬────┘
         │                 │                 │
         └─────────────────┼─────────────────┘
                           ▼
              ┌─────────────────────────┐
              │  THREE-WAY RECONCILIATION│
              │  Contract × Invoice ×    │
              │  Actuals (orders, inv.)  │
              └───────────┬─────────────┘
                          ▼
              ┌─────────────────────────┐
              │  DISPUTE PACKET (PDF)   │
              │  • Line-item variance   │
              │  • Contract clause cite │
              │  • Evidence (WMS data)  │
              │  • Pre-filled 3PL email │
              └─────────────────────────┘
```

### 1.4 Value Proposition
| Stakeholder | Value Delivered |
|-------------|-----------------|
| **Brand (Buyer)** | Recover $50K–$500K/year in overcharges; 9–25× ROI on subscription |
| **CFO/VP Ops** | Monthly audit without hiring; audit trail for SOX/compliance |
| **3PL** | Faster dispute resolution; structured evidence reduces back-and-forth |
| **Investor** | High-margin SaaS in $200B+ logistics tech market; sticky workflow revenue |

---

## 2. MARKET ANALYSIS

### 2.1 Total Addressable Market (TAM)
| Segment | Brands (US) | Avg 3PL Spend/Year | TAM (Audit SaaS @ 2% of spend) |
|---------|-------------|-------------------|--------------------------------|
| **Enterprise D2C** ($500M+) | 200 | $10M+ | $400M |
| **Mid-Market D2C** ($50M–$500M) | 2,000 | $1M–$10M | $1.2B |
| **Growth D2C** ($10M–$50M) | 8,000 | $200K–$1M | $800M |
| **Emerging D2C** ($5M–$10M) | 15,000 | $50K–$200K | $300M |
| **TOTAL** | **~25,000** | | **$2.7B** |

### 2.2 Serviceable Obtainable Market (SOM) — Year 3
- **Target:** 500 paying brands (2% of mid-market + growth)
- **Avg Contract:** $3,000/mo ($36K ARR)
- **SOM Revenue:** **$18M ARR**

### 2.3 Ideal Customer Profile (ICP)
| Attribute | Specification |
|-----------|---------------|
| **Revenue** | $10M–$500M GMV |
| **Fulfillment** | Outsourced to 1–3 3PLs |
| **Volume** | 5,000–500,000 orders/month |
| **Tech Stack** | Shopify Plus / BigCommerce + WMS (Deposco, Logiwa, Extensiv, custom) |
| **Pain** | CFO/VP Ops reviews invoices manually; knows overcharging but lacks proof |
| **Buyer** | VP Operations, CFO, Director Supply Chain |
| **Budget Authority** | $2K–$10K/mo without board approval |

### 2.4 Competitive Landscape
| Competitor | Type | Gap |
|------------|------|-----|
| **3PL Native Portals** (ShipBob, ShipMonk) | First-party | Conflict of interest; no contract enforcement |
| **Freight Audit** (Transporeon, project44) | Transportation only | No fulfillment/storage/pick-pack audit |
| **AP Automation** (Tipalti, Coupa, Bill.com) | Header-level match | No line-level 3-way match for fulfillment |
| **Spreadsheet/Manual** | Status quo | 80% of market; error-prone, not scalable |
| **Consulting Firms** (Supply Chain Advisory) | Project-based | $50K+/engagement; not continuous; no software |

**Our Moat:** Only solution that **reads the contract** + **reads the invoice** + **reads actuals** → **generates executable pricing code** → **produces lawyer-grade dispute packets**.

---

## 3. TECHNICAL ARCHITECTURE

### 3.1 High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CLIENT LAYER                                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  Web App    │  │  Mobile PWA │  │  API Client │  │  3PL Portal │        │
│  │  (Next.js)  │  │  (React)    │  │  (TypeScript)│ │  (Future)   │        │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘        │
└─────────┼────────────────┼────────────────┼────────────────┼───────────────┘
          │                │                │                │
          ▼                ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            API GATEWAY (Vercel Edge)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ Auth (Clerk)│  │ Rate Limit  │  │ Request     │  │ Webhook     │        │
│  │             │  │ (Upstash)   │  │ Validation  │  │ Handler     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        ▼                        ▼                        ▼
┌───────────────┐      ┌───────────────┐      ┌───────────────┐
│  WEB WORKERS  │      │  PARSER SVC   │      │  RECONCILIATION│
│  (Next.js API)│      │  (FastAPI)    │      │  ENGINE       │
│               │      │               │      │  (TypeScript) │
│ • Upload      │      │ • PDF Parse   │      │               │
│ • Dashboard   │      │ • CSV Normalize│     │ • Rate Calc   │
│ • Billing     │      │ • Contract→AST │     │ • 3-Way Match │
│ • Settings    │      │ • Evidence    │      │ • Dispute Gen │
└───────┬───────┘      └───────┬───────┘      └───────┬───────┘
        │                      │                      │
        ▼                      ▼                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DATA LAYER (Supabase/PostgreSQL)               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ Organizations│  │ Contracts   │  │ Invoices    │  │ Disputes    │        │
│  │ Users       │  │ RateRules   │  │ InvoiceLines│  │ Variances   │        │
│  │ Subscriptions│  │ Parsers     │  │ Actuals     │  │ Evidence    │        │
│  │ AuditLogs   │  │ Templates   │  │ Snapshots   │  │ Communications│       │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
                                 │
                    ┌────────────┼────────────┐
                    ▼            ▼            ▼
           ┌─────────────┐ ┌───────────┐ ┌───────────┐
           │  Storage    │ │  Queue    │ │  Cache    │
           │  (Supabase) │ │ (Upstash) │ │ (Upstash) │
           │  Files      │ │  Jobs     │ │  Sessions │
           └─────────────┘ └───────────┘ └───────────┘
```

### 3.2 Core Services

#### **3.2.1 Parser Service (Python FastAPI)**
| Component | Technology | Responsibility |
|-----------|------------|----------------|
| **Contract Parser** | pdfplumber + regex + LLM (Gemini 1.5 Pro) | Extract rate tables, tiers, surcharges, clauses from 3PL contracts |
| **Invoice Parser** | pandas + fuzzy matching | Normalize 3PL-specific CSV/Excel exports to standard schema |
| **Actuals Parser** | Shopify API, WMS API, CSV | Ingest order, inventory, return data for reconciliation |
| **Parser Registry** | Plugin architecture | Auto-detect 3PL format; add new 3PL in <1 hour via config |

**Supported 3PL Formats (Phase 1):** ShipBob, ShipMonk, Quiet Logistics, Red Stag, Delhivery, Ecom Express, Xpressbees, Shadowfax
**Extensibility:** Config-driven column mapping + regex library + LLM fallback

#### **3.2.2 Rate Calculation Engine (Pure TypeScript — Zero Dependencies)**
```typescript
// Core interface — fully tested, deterministic, auditable
interface RateEngine {
  // Storage: tiered pallet/cubic ft, free days, minimums, LTS
  calculateStorage(rules: StorageRules, actuals: StorageActuals): ChargeResult;
  
  // Pick/Pack: base + per-line, volume tiers, kitting, special handling
  calculatePickPack(rules: PickPackRules, actuals: OrderActuals): ChargeResult;
  
  // Receiving: palletized vs floor-loaded, unloading labor, per-carton
  calculateReceiving(rules: ReceivingRules, actuals: ReceivingActuals): ChargeResult;
  
  // Shipping: contract discount %, carrier rates, DIM divisor, zones, surcharges
  calculateShipping(rules: ShippingRules, actuals: ShipmentActuals): ChargeResult;
  
  // Surcharges: peak, fuel, hazmat, oversize, long-term storage
  calculateSurcharges(rules: SurchargeRules, context: SurchargeContext): ChargeResult;
}
```

**Key Properties:**
- **Pure functions** — no I/O, no side effects, fully testable
- **Decimal.js** — financial precision (no floating-point errors)
- **Property-based tests** — fast-check validates: monotonicity, boundary conditions, idempotency
- **Versioned** — contract changes = new engine version; full audit trail

#### **3.2.3 Three-Way Reconciliation Engine**
```
Input: Contract RateRules[] + InvoiceLines[] + Actuals (Orders, Inventory, Returns)
         │
         ▼
┌─────────────────────────────────────────────────────────────────┐
│  MATCHING PIPELINE                                              │
│  1. GROUP by charge_type (storage, pickpack, receiving, etc.)  │
│  2. FUZZY MATCH invoice lines → contract rules (code + desc)   │
│  3. APPLY rate engine → calculate correct charge per line      │
│  4. COMPARE calculated vs. invoiced → variance                 │
│  5. VERIFY against actuals (qty, dates, dimensions)            │
│  6. SCORE confidence (0.0–1.0) based on match quality          │
│  7. BUILD evidence bundle (contract clause, actuals, calc steps)│
└─────────────────────────────────────────────────────────────────┘
         │
         ▼
Output: Variance[] { lineId, chargeType, contractRate, invoiceRate, 
                      actualQty, billedQty, varianceAmount, variancePct, 
                      confidence, evidence[], suggestedAction }
```

#### **3.2.4 Dispute Packet Generator (React-PDF)**
| Section | Content | Automation Level |
|---------|---------|------------------|
| **Cover Letter** | Brand letterhead, 3PL address, date, reference numbers | 100% |
| **Executive Summary** | Total overcharge, top 3 categories, % of invoice, recovery estimate | 100% |
| **Variance Detail Tables** | Per charge type: line-by-line with contract/invoice/actual columns | 100% |
| **Evidence Appendix** | Contract clause screenshots, WMS screenshots, rate calc steps | 90% |
| **Contract Citations** | Exact section/subsection for each disputed charge | 100% |
| **Pre-filled Email** | To 3PL billing contact, subject, body, attachment refs | 100% |
| **Escalation Path** | 3PL contacts: billing → ops → legal → executive | Template |

---

## 4. DATA MODELS

### 4.1 Core Entities (Prisma Schema)

```prisma
// User & Organization
model User {
  id            String    @id @default(cuid())
  clerkId       String    @unique
  email         String    @unique
  name          String?
  role          Role      @default(MEMBER)
  organizations OrganizationMember[]
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

model Organization {
  id            String    @id @default(cuid())
  name          String
  slug          String    @unique
  settings      Json      @default("{}")
  members       OrganizationMember[]
  contracts     Contract[]
  invoices      Invoice[]
  disputes      Dispute[]
  subscriptions Subscription?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

model OrganizationMember {
  id             String       @id @default(cuid())
  userId         String
  user           User         @relation(fields: [userId], references: [id])
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  role           OrgRole      @default(MEMBER)
  @@unique([userId, organizationId])
}

// Contracts & Rate Rules
model Contract {
  id             String     @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  name           String
  version        String
  status         ContractStatus @default(ACTIVE)
  effectiveFrom  DateTime
  effectiveTo    DateTime?
  fileUrl        String     // Supabase Storage
  parsedData     Json       // Raw extracted tables
  rateRules      RateRule[]
  parserConfig   Json       // 3PL-specific parsing config
  createdAt      DateTime   @default(now())
  updatedAt      DateTime   @updatedAt
}

model RateRule {
  id            String   @id @default(cuid())
  contractId    String
  contract      Contract @relation(fields: [contractId], references: [id])
  chargeType    ChargeType
  tier          Int      // For tiered rules
  minThreshold  Decimal  @db.Decimal(15,4)
  maxThreshold  Decimal? @db.Decimal(15,4)
  rate          Decimal  @db.Decimal(15,6)
  unit          String   // pallet, order, line, lb, sqft, etc.
  metadata      Json     // Surcharges, minimums, free days, etc.
  sourceClause  String   // Contract section reference
  effectiveFrom DateTime
  effectiveTo   DateTime?
  @@index([contractId, chargeType])
}

// Invoices & Lines
model Invoice {
  id             String       @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  contractId     String
  contract       Contract     @relation(fields: [contractId], references: [id])
  invoiceNumber  String
  invoiceDate    DateTime
  periodStart    DateTime
  periodEnd      DateTime
  totalAmount    Decimal      @db.Decimal(15,2)
  currency       String       @default("USD")
  status         InvoiceStatus @default(PROCESSING)
  fileUrl        String       // Supabase Storage
  parsedData     Json         // Raw normalized lines
  lines          InvoiceLine[]
  auditRuns      AuditRun[]
  createdAt      DateTime     @default(now())
  updatedAt      DateTime     @updatedAt
  @@unique([organizationId, invoiceNumber])
}

model InvoiceLine {
  id           String       @id @default(cuid())
  invoiceId    String
  invoice      Invoice      @relation(fields: [invoiceId], references: [id])
  chargeCode   String
  description  String
  chargeType   ChargeType
  quantity     Decimal      @db.Decimal(15,4)
  unit         String
  rate         Decimal      @db.Decimal(15,6)
  amount       Decimal      @db.Decimal(15,2)
  metadata     Json         // Original row data
  matchedRuleId String?     // FK to RateRule
  variance     Variance?
  @@index([invoiceId, chargeType])
}

// Actuals (Brand Data)
model OrderActual {
  id             String   @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  externalId     String   // Shopify order ID
  orderDate      DateTime
  lineItems      Json     // [{sku, qty, weight, dimensions, ...}]
  shippingAddress Json
  tags           String[]
  createdAt      DateTime @default(now())
  @@index([organizationId, orderDate])
}

model InventorySnapshot {
  id             String   @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  snapshotDate   DateTime
  sku            String
  qtyOnHand      Int
  storageType    String   // pallet, shelf, bin, floor
  location       String   // 3PL warehouse code
  cubicFeet      Decimal  @db.Decimal(10,2)
  palletPositions Int
  createdAt      DateTime @default(now())
  @@index([organizationId, snapshotDate])
}

// Reconciliation & Disputes
model AuditRun {
  id             String       @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  contractId     String
  invoiceId      String
  status         AuditStatus  @default(COMPLETED)
  totalVariance  Decimal      @db.Decimal(15,2)
  varianceCount  Int
  autoResolved   Int
  needsReview    Int
  createdAt      DateTime     @default(now())
  variances      Variance[]
}

model Variance {
  id              String       @id @default(cuid())
  auditRunId      String
  auditRun        AuditRun     @relation(fields: [auditRunId], references: [id])
  invoiceLineId   String
  invoiceLine     InvoiceLine  @relation(fields: [invoiceLineId], references: [id])
  chargeType      ChargeType
  contractRate    Decimal      @db.Decimal(15,6)
  invoiceRate     Decimal      @db.Decimal(15,6)
  actualQuantity  Decimal      @db.Decimal(15,4)
  billedQuantity  Decimal      @db.Decimal(15,4)
  varianceAmount  Decimal      @db.Decimal(15,2)
  variancePct     Decimal      @db.Decimal(5,2)
  confidence      Decimal      @db.Decimal(3,2) // 0.00–1.00
  status          VarianceStatus @default(PENDING)
  evidence        Json         // Structured evidence bundle
  disputeId       String?
  dispute         Dispute?     @relation(fields: [disputeId], references: [id])
  createdAt       DateTime     @default(now())
  resolvedAt      DateTime?
}

model Dispute {
  id              String       @id @default(cuid())
  organizationId  String
  organization    Organization @relation(fields: [organizationId], references: [id])
  invoiceId       String
  status          DisputeStatus @default(DRAFT)
  packetUrl       String?      // Generated PDF in Supabase Storage
  emailSentAt     DateTime?
  responseReceivedAt DateTime?
  resolution      DisputeResolution?
  resolvedAmount  Decimal?     @db.Decimal(15,2)
  resolvedAt      DateTime?
  variances       Variance[]
  communications  Communication[]
  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt
}

model Communication {
  id          String       @id @default(cuid())
  disputeId   String
  dispute     Dispute      @relation(fields: [disputeId], references: [id])
  direction   CommDirection // OUTBOUND, INBOUND
  channel     CommChannel   // EMAIL, PORTAL, PHONE
  subject     String?
  body        String
  sentBy      String
  sentAt      DateTime
  metadata    Json
}

// Billing
model Subscription {
  id                   String   @id @default(cuid())
  organizationId       String   @unique
  organization         Organization @relation(fields: [organizationId], references: [id])
  stripeCustomerId     String   @unique
  stripeSubscriptionId String   @unique
  plan                 Plan     @default(STARTER)
  status               SubStatus @default(ACTIVE)
  currentPeriodStart   DateTime
  currentPeriodEnd     DateTime
  cancelAtPeriodEnd    Boolean  @default(false)
  invoicesAudited      Int      @default(0)
  invoicesLimit        Int
  createdAt            DateTime @default(now())
  updatedAt            DateTime @updatedAt
}

// Enums
enum Role { ADMIN MEMBER VIEWER }
enum OrgRole { OWNER ADMIN MEMBER VIEWER }
enum ChargeType { STORAGE PICK_PACK RECEIVING SHIPPING KITTING RETURNS SURCHARGE OTHER }
enum ContractStatus { DRAFT ACTIVE EXPIRED SUPERSEDED }
enum InvoiceStatus { PROCESSING PARSED AUDITED DISPUTED PAID }
enum AuditStatus { RUNNING COMPLETED FAILED }
enum VarianceStatus { PENDING AUTO_RESOLVED NEEDS_REVIEW DISPUTED RESOLVED IGNORED }
enum DisputeStatus { DRAFT SENT ACKNOWLEDGED NEGOTIATING RESOLVED DENIED ESCALATED }
enum DisputeResolution { FULL PARTIAL DENIED PENDING }
enum SubStatus { ACTIVE PAST_DUE CANCELED TRIALING }
enum Plan { STARTER GROWTH ENTERPRISE }
enum CommDirection { OUTBOUND INBOUND }
enum CommChannel { EMAIL PORTAL PHONE MEETING }
```

---

## 5. API SPECIFICATIONS

### 5.1 REST API (Next.js App Router — `/api/v1`)

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/organizations` | GET | Org Member | List user's organizations |
| `/organizations` | POST | User | Create organization |
| `/organizations/:id` | GET | Org Member | Get organization details |
| `/organizations/:id/settings` | PATCH | Org Admin | Update settings |
| `/organizations/:id/members` | GET | Org Admin | List members |
| `/organizations/:id/members` | POST | Org Admin | Invite member |
| `/organizations/:id/members/:userId` | DELETE | Org Admin | Remove member |
| `/contracts` | GET | Org Member | List contracts |
| `/contracts` | POST | Org Admin | Upload & parse contract |
| `/contracts/:id` | GET | Org Member | Get contract with rate rules |
| `/contracts/:id/rate-rules` | GET | Org Member | Get parsed rate rules |
| `/contracts/:id/rate-rules` | PATCH | Org Admin | Override rate rules (manual) |
| `/invoices` | GET | Org Member | List invoices (paginated, filterable) |
| `/invoices` | POST | Org Admin | Upload & parse invoice |
| `/invoices/:id` | GET | Org Member | Get invoice with lines |
| `/invoices/:id/lines` | GET | Org Member | Get invoice lines |
| `/invoices/:id/audit` | POST | Org Member | Trigger audit run |
| `/audits` | GET | Org Member | List audit runs |
| `/audits/:id` | GET | Org Member | Get audit results with variances |
| `/audits/:id/variances` | GET | Org Member | Get variances (filterable) |
| `/variances/:id` | PATCH | Org Member | Update variance status |
| `/disputes` | GET | Org Member | List disputes |
| `/disputes` | POST | Org Member | Create dispute from variances |
| `/disputes/:id` | GET | Org Member | Get dispute with packet |
| `/disputes/:id/packet` | POST | Org Member | Generate PDF packet |
| `/disputes/:id/send` | POST | Org Member | Send dispute email |
| `/disputes/:id/communications` | POST | Org Member | Log communication |
| `/actuals/orders` | POST | Org Admin | Sync Shopify orders |
| `/actuals/inventory` | POST | Org Admin | Upload inventory snapshots |
| `/actuals/returns` | POST | Org Admin | Upload return data |
| `/billing/subscription` | GET | Org Admin | Get subscription details |
| `/billing/portal` | POST | Org Admin | Create Stripe portal session |
| `/billing/usage` | GET | Org Member | Get current usage vs limits |

### 5.2 Webhooks

| Event | Payload | Retry Policy |
|-------|---------|--------------|
| `invoice.parsed` | `{ invoiceId, lineCount, status }` | 3x exponential backoff |
| `audit.completed` | `{ auditId, varianceCount, totalVariance }` | 3x |
| `dispute.created` | `{ disputeId, invoiceId, amount }` | 3x |
| `dispute.resolved` | `{ disputeId, resolution, amount }` | 3x |
| `subscription.updated` | `{ orgId, plan, status }` | 5x |
| `parser.error` | `{ fileId, error, 3plType }` | Alert only |

### 5.3 Parser Service API (Internal — FastAPI)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/parse/contract` | POST | Parse contract PDF → RateRule[] |
| `/parse/invoice` | POST | Parse invoice CSV/Excel → InvoiceLine[] |
| `/parse/actuals/shopify` | POST | Fetch & normalize Shopify orders |
| `/parse/actuals/wms` | POST | Parse WMS inventory export |
| `/detect/3pl` | POST | Auto-detect 3PL from file headers |
| `/registry/parsers` | GET | List available parser configs |
| `/registry/parsers` | POST | Register new parser config |

---

## 6. FEATURE SPECIFICATIONS

### 6.1 Core Features (MVP — Weeks 1-12)

| Feature | Description | Acceptance Criteria |
|---------|-------------|---------------------|
| **Contract Upload & Parse** | Drag-drop PDF → extracts rate tables → editable RateRules table | 90%+ extraction accuracy on pilot 3PLs; <30 sec processing |
| **Invoice Upload & Parse** | Drag-drop CSV/Excel → normalizes to standard schema → grouped by charge type | Handles 5+ 3PL formats; <10 sec processing |
| **Actuals Ingestion** | Shopify API sync + CSV upload for inventory/returns | Daily auto-sync; manual upload fallback |
| **Rate Engine** | Pure TS functions for all charge types; Decimal.js precision | 100% test coverage; property-based tests pass |
| **Three-Way Reconciliation** | Contract × Invoice × Actuals → Variance[] with confidence scores | Matches manual Excel audit within 2% on pilot data |
| **Dispute Packet Generator** | Professional PDF with evidence, citations, pre-filled email | 3PL accepts packet without reformatting; <5 min generation |
| **Dashboard** | Upload → Audit → Variances → Disputes → Track → Resolve | End-to-end flow in <15 min for 500-line invoice |
| **Multi-3PL Support** | Parser registry auto-detects format; config-driven | New 3PL added in <1 hour without code deploy |
| **Auth & Organizations** | Clerk-based; role-based access (Owner, Admin, Member, Viewer) | Invite flow works; RLS enforced |
| **Stripe Billing** | 3 tiers + usage-based overages; customer portal | Self-serve upgrade/downgrade/cancel |

### 6.2 Phase 2 Features (Weeks 13-20)

| Feature | Description |
|---------|-------------|
| **AI Parser Builder** | Upload contract → LLM suggests parser config → human approves → auto-generates parser |
| **3PL Portal** | 3PL logs in → sees disputes → responds with evidence → auto-updates status |
| **Benchmarking** | "Your storage cost 18% above peer median" — anonymous cohort comparison |
| **Predictive Alerts** | "Next month's storage projected 22% over contract" based on inventory trends |
| **Advanced Analytics** | Variance trends by charge type, 3PL, seasonality; root cause tagging |
| **ERP Integrations** | NetSuite, QuickBooks, Xero sync for invoice ingestion |
| **White-label Disputes** | Branded packets, custom escalation paths, legal review workflow |

### 6.3 Phase 3 Features (Weeks 21-28)

| Feature | Description |
|---------|-------------|
| **3PL Performance Marketplace** | Ratings, switching recommendations, contract negotiation benchmarks |
| **Contract Optimization AI** | "Change tier threshold from 3K to 5K pallets → save $18K/yr" |
| **Multi-Entity Consolidation** | Holding companies: roll-up across subsidiaries, currencies, 3PLs |
| **API for 3PLs** | 3PLs push invoice data directly; real-time reconciliation |
| **Audit Insurance** | Partner with insurer: guaranteed recovery or premium refund |

---

## 7. BUSINESS MODEL

### 7.1 Pricing Tiers

| Tier | Monthly | Annual | Invoices/Mo | Overages | Features |
|------|---------|--------|-------------|----------|----------|
| **Starter** | $299 | $2,990 | 10 | $50/inv | 1 3PL, 2 users, email support |
| **Growth** | $999 | $9,990 | 50 | $30/inv | 3 3PLs, 5 users, priority support, API |
| **Enterprise** | $2,999 | $29,990 | 200 | $15/inv | Unlimited 3PLs, SSO, dedicated CSM, custom SLA |

**Usage-Based Component:** All tiers include base invoices; overages billed monthly. Enterprise includes volume discounts.

### 7.2 Unit Economics (Projected Year 2)

| Metric | Value |
|--------|-------|
| **Avg Contract (Blended)** | $1,200/mo ($14,400 ARR) |
| **Gross Margin** | 85% (AI parsing costs ~$15/invoice at scale) |
| **CAC (Blended)** | $3,500 (outbound + content + referrals) |
| **Payback Period** | 3 months |
| **LTV (3-yr, 10% annual churn)** | $39,000 |
| **LTV/CAC** | 11.1x |
| **Net Revenue Retention** | 115% (usage expansion + tier upgrades) |

### 7.3 Go-to-Market Strategy

#### **Channel 1: Direct Outbound (60% of leads)**
- **Target:** 500 ICP companies/quarter
- **Sequence:** LinkedIn → Email → Call → Pilot
- **Hook:** "Free audit of 3 recent invoices — we found $X overcharges for similar brands"

#### **Channel 2: Partner Channel (25% of leads)**
- **Fractional CFOs** (500+ in US) — they audit client 3PL spend quarterly
- **E-commerce Agencies** — they manage 3PL relationships for brands
- **Supply Chain Consultants** — project-based; need continuous tool
- **Incentive:** 20% revenue share Year 1; co-branded portal

#### **Channel 3: Inbound/Content (15% of leads)**
- **SEO:** "3PL audit", "fulfillment overcharges", "warehouse invoice audit"
- **Case Studies:** "How Brand X recovered $240K/year"
- **Webinars:** Monthly "3PL Contract Teardown" with real contracts
- **Tools:** Free "3PL Rate Calculator" → lead capture

### 7.4 Sales Process
```
Lead → Qualification (ICP check) → Free Audit (3 invoices) → 
Results Review Call → Proposal → Pilot (30 days, 3 months data) → 
Conversion → Annual Contract
```
**Sales Cycle:** 30–60 days (fast for B2B SaaS — ROI is mathematical)
**Pilot Close Rate:** 60%+ (free audit proves value)

---

## 8. TECHNICAL MOATS

| Moat | Description | Defensibility |
|------|-------------|---------------|
| **Contract-to-AST Compiler** | Parses legal contract rate tables → executable TypeScript. Handles nested tiers, conditional surcharges, minimums, phase-in/phase-out schedules. | Requires: legal NLP + financial math + code gen. 18+ months to replicate. |
| **Proprietary 3PL Parser Library** | 20+ parser configs for major 3PLs. Auto-detection + config-driven. Each parser = 200+ test cases from real invoices. | Data moat: 10,000+ real invoices = training set. New entrant needs 2+ years. |
| **Three-Way Reconciliation Graph** | Contract × Invoice × Actuals as unified knowledge graph. Fuzzy matching + confidence scoring + evidence linking. | Algorithmic complexity: combinatorial matching with domain constraints. |
| **Dispute Packet Standard** | Lawyer-grade output accepted by 3PL billing depts without reformatting. Becomes de facto format. | Network effect: more brands use it → 3PLs expect it → switching cost. |
| **Feedback Loop → Rule Synthesis** | Every human resolution → proposed rule → auto-resolve rate climbs 70% → 85% → 92%. | Compounding data advantage. Org-specific rules = switching cost. |
| **Brand-Actuals Integration** | Direct Shopify/WMS/API ingestion. No manual CSV after setup. Real-time inventory snapshots. | Integration depth: 50+ WMS/ERP connectors. High switching cost once embedded. |

---

## 9. IMPLEMENTATION ROADMAP

### 9.1 Phase 0: Foundation (Weeks 1-2)
- [ ] Turborepo + Next.js 14 + FastAPI scaffold
- [ ] Supabase + Prisma + Clerk + Vercel deploy
- [ ] File upload → storage → queue → parser pipeline
- [ ] First pilot recruited; contract + invoices received

### 9.2 Phase 1: Core Engine (Weeks 3-8)
- [ ] Contract parser for Pilot 3PL (ShipBob/equivalent)
- [ ] Invoice parser for same 3PL
- [ ] Rate calculation engine (all charge types)
- [ ] Three-way reconciliation with confidence scoring
- [ ] Dispute packet generator (PDF + email)
- [ ] End-to-end pilot audit completed

### 9.3 Phase 2: Productization (Weeks 9-14)
- [ ] Multi-3PL parser registry (3+ 3PLs)
- [ ] Dashboard: audit history, variance trends, dispute tracker
- [ ] Stripe billing + subscription management
- [ ] Pilot conversion: 3 paying customers
- [ ] Self-serve onboarding flow

### 9.4 Phase 3: GTM Launch (Weeks 15-22)
- [ ] Outbound engine (50 contacts/week)
- [ ] Partner program (5 fractional CFOs)
- [ ] Content engine (2 case studies, 1 webinar/month)
- [ ] $10K MRR milestone
- [ ] AI parser builder (LLM-assisted config)

### 9.5 Phase 4: Scale (Weeks 23-28)
- [ ] 3PL response portal
- [ ] Benchmarking & predictive analytics
- [ ] ERP integrations (NetSuite, QuickBooks)
- [ ] $25K MRR / 40 customers
- [ ] YC/Seed fundraising ready

---

## 10. SUCCESS METRICS (KPIs)

### 10.1 Product Metrics
| Metric | Target (Month 6) | Target (Month 12) |
|--------|------------------|-------------------|
| **Parse Accuracy (Contract)** | 90% | 95% |
| **Parse Accuracy (Invoice)** | 95% | 98% |
| **Reconciliation Accuracy vs Manual** | 95% | 98% |
| **Auto-Resolve Rate** | 60% | 80% |
| **Dispute Packet Generation Time** | <5 min | <2 min |
| **Audit Run Time (500 lines)** | <2 min | <30 sec |
| **Uptime** | 99.5% | 99.9% |

### 10.2 Business Metrics
| Metric | Target (Month 6) | Target (Month 12) |
|--------|------------------|-------------------|
| **Paying Customers** | 10 | 40 |
| **MRR** | $15,000 | $50,000 |
| **ARR** | $180,000 | $600,000 |
| **Avg Contract Value** | $1,500/mo | $1,800/mo |
| **CAC** | $4,000 | $3,000 |
| **Payback Period** | 4 months | 3 months |
| **Net Revenue Retention** | 110% | 120% |
| **Gross Churn (Logo)** | <5% | <3% |

### 10.3 Pilot Validation Metrics (Pre-Launch)
| Metric | Minimum to Proceed |
|--------|-------------------|
| **Overcharges Found per Audit** | >$5,000 (3-month sample) |
| **Pilot Willingness to Pay** | 2/3 verbally commit at $299/mo |
| **Parse Success Rate** | >85% lines auto-parsed |
| **Dispute Packet Quality** | 3PL accepts without revision |
| **Time to Audit** | <30 min end-to-end |

---

## 11. RISK ASSESSMENT & MITIGATION

| Risk | Likelihood | Impact | Mitigation Strategy |
|------|------------|--------|---------------------|
| **3PL Invoice Format Changes** | High | Medium | Config-driven parsers; auto-detect + alert on parse failure; LLM fallback |
| **Contract Parsing Edge Cases** | High | High | Human-in-loop review; rule editor UI; versioned engine; test case library |
| **Pilot Data Access Denied** | Medium | High | 10 parallel pilots; NDA template; read-only API tokens; CSV fallback |
| **3PLs Block Dispute Process** | Low | High | Legal review of packets; 3PL portal for structured response; escalation paths |
| **Competition from 3PLs** | Low | Medium | 3PLs profit from opacity; contract enforcement is conflict of interest |
| **Competition from AP Automation** | Medium | Medium | We do line-level fulfillment audit; they do header-level AP. Different category. |
| **AI API Cost Overrun** | Medium | Low | Local Ollama for parsing; Gemini free tier; strict budgets; caching |
| **Regulatory/Compliance** | Low | Medium | SOC2 Type II by Year 2; data encryption; audit logs; GDPR/CCPA ready |
| **Key Person Dependency** | High | High | Documentation-first; ADRs; open-source parser framework; hire by Month 9 |

---

## 12. TECHNICAL DEBT & QUALITY STANDARDS

### 12.1 Code Quality Gates (Enforced in CI)
- **TypeScript:** Strict mode, no `any`, no `@ts-ignore`
- **Test Coverage:** 90%+ on rate engine, reconciliation, parsers
- **Property-Based Tests:** fast-check on all financial calculations
- **Linting:** ESLint + Prettier + import sorting
- **Security:** `npm audit` + Snyk; no secrets in code
- **Performance:** Bundle analysis; API response <200ms p95

### 12.2 Documentation Standards
- **ADRs:** Every architectural decision recorded (`docs/ADR/`)
- **API Docs:** OpenAPI 3.0 spec auto-generated from code
- **Parser Specs:** Per-3PL parsing rules documented with examples
- **Runbooks:** Incident response for each service
- **Onboarding:** New dev productive in <1 day

### 12.3 Observability
- **Errors:** Sentry (all environments)
- **Metrics:** Prometheus + Grafana (custom business metrics)
- **Logs:** Structured JSON (Pino) → Loki
- **Traces:** OpenTelemetry (Vercel + FastAPI)
- **Alerts:** PagerDuty (critical: parse failures, audit errors, billing webhooks)

---

## 13. LEGAL & COMPLIANCE

### 13.1 Data Handling
- **PII:** Minimal (org admin email, 3PL contacts). No customer PII.
- **Financial Data:** Encrypted at rest (Supabase) + in transit (TLS 1.3).
- **Retention:** Contracts/invoices: 7 years (tax). Disputes: 7 years. Analytics: aggregated/anonymized after 2 years.
- **Access:** Role-based; audit logs on all data access.

### 13.2 Contractual
- **MSA:** Standard SaaS agreement with data processing addendum
- **SLA:** 99.5% uptime (Growth+); 99.9% (Enterprise)
- **Liability:** Cap at 12 months fees; no consequential damages
- **IP:** Customer owns their data; we own parser configs & algorithms

### 13.3 Regulatory
- **SOX:** Audit trail supports client compliance
- **GDPR/CCPA:** Data export/deletion API; DPA with subprocessors
- **PCI:** No card data (Stripe handles); SAQ-A eligible

---

## 14. APPENDICES

### 14.1 Sample Variance Output (JSON)
```json
{
  "varianceId": "var_abc123",
  "auditRunId": "audit_xyz789",
  "invoiceLineId": "line_4421",
  "chargeType": "STORAGE",
  "contractRate": 12.00,
  "invoiceRate": 18.00,
  "actualQuantity": 3120,
  "billedQuantity": 4850,
  "varianceAmount": 14200.00,
  "variancePct": 44.4,
  "confidence": 0.92,
  "status": "PENDING",
  "evidence": {
    "contractClause": "§4.2(b): 'Storage >3,000 pallets @ $12/pallet/month'",
    "contractPage": 8,
    "invoiceLine": { "code": "STOR-01", "desc": "Pallet Storage - Monthly", "qty": 4850, "rate": 18 },
    "actualsSnapshot": { "date": "2024-11-30", "palletPositions": 3120, "source": "WMS" },
    "rateCalculation": {
      "tiers": [
        { "min": 0, "max": 3000, "rate": 15 },
        { "min": 3001, "max": null, "rate": 12 }
      ],
      "calculated": 3000 * 15 + 120 * 12 = 46440,
      "billed": 4850 * 18 = 87300
    }
  },
  "suggestedAction": "DISPUTE: Overcharge $14,200. Contract §4.2(b) caps rate at $12 for positions >3,000. WMS confirms 3,120 positions."
}
```

### 14.2 Sample Dispute Packet Structure
```
Page 1: Cover Letter (Brand letterhead, 3PL billing address, reference)
Page 2: Executive Summary (Total variance: $18,522 / 14.5% of invoice)
Page 3: Variance by Charge Type (Table: Storage $14,200, Pick/Pack $2,800, Shipping $1,522)
Page 4-6: Storage Detail (Line-by-line with contract/invoice/actual columns)
Page 7-8: Pick/Pack Detail
Page 9: Shipping Detail
Page 10: Evidence Appendix A - Contract §4.2(b) screenshot
Page 11: Evidence Appendix B - WMS Inventory Snapshot Nov 30
Page 12: Evidence Appendix C - Rate Calculation Steps
Page 13: Pre-filled Email to 3PL Billing Contact
Page 14: Escalation Contacts & Timeline
```

### 14.3 3PL Parser Config Schema (Example)
```yaml
# parsers/configs/shipbob.yaml
name: "ShipBob"
detection:
  invoice_headers: ["ShipBob", "shipbob.com", "fulfillment.shipbob"]
  contract_keywords: ["ShipBob, Inc.", "Master Services Agreement"]
invoice:
  column_mapping:
    charge_code: "Charge Code"
    description: "Description"
    quantity: "Qty"
    unit: "Unit"
    rate: "Rate"
    amount: "Amount"
    date_range: "Service Period"
  charge_type_rules:
    - pattern: "STOR*"
      charge_type: "STORAGE"
    - pattern: "PICK*|PACK*"
      charge_type: "PICK_PACK"
    - pattern: "RECV*|RECEIV*"
      charge_type: "RECEIVING"
    - pattern: "SHIP*|FREIGHT*|CARRIER*"
      charge_type: "SHIPPING"
contract:
  table_detection:
    storage: "Storage Rates|Pallet Storage"
    pick_pack: "Pick & Pack|Order Fulfillment"
    receiving: "Receiving|Inbound"
    shipping: "Shipping|Carrier Rates"
  tier_patterns:
    - regex: "(\\d+)[-\\s](\\d+)\\s*(pallet|position).*\\$(\\d+\\.?\\d*)"
      groups: [min, max, unit, rate]
```

---

## 15. VERSION HISTORY

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-11-10 | Solo Founder | Initial comprehensive specification |

---

**END OF DOCUMENT**

---

*This document represents the complete product concept and technical specification for the 3PL Billing Audit Engine. It serves as the single source of truth for architecture, data models, APIs, features, and business strategy. All implementation decisions should reference this document. Updates require ADR entry in `docs/ADR/`.*