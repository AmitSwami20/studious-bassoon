# 3PL BILLING AUDIT ENGINE — MASTER BUILD PLAN
**Solo Build | BCA AI/ML Student | ₹10K Budget | AI-Only (Claude Code + ChatGPT) | 28 Weeks to Revenue**

---

## 🎯 NORTH STAR
**Build a SaaS that reads 3PL contracts + invoices + actuals → finds overcharges → generates dispute packets brands pay $2-5K/mo for.**

**Success = 3 paying pilots by Week 20 | ₹3L+ ARR by Week 28**

---

## 💰 BUDGET ALLOCATION (₹10,000 TOTAL)

| Item | Cost | When | Notes |
|------|------|------|-------|
| **Domain (.com/.in)** | ₹1,200 | Week 1 | Namecheap/Porkbun - only recurring cost |
| **AI API Credits** | ₹6,000 | Weeks 3-20 | OpenAI $50 + Anthropic $20 = ~₹6K for dev only |
| **Vercel Pro** | ₹0 | Never | Hobby tier sufficient until scale |
| **Supabase Pro** | ₹0 | Never | Free tier (500MB DB, 1GB storage, 50K MAU) |
| **Resend/Email** | ₹0 | Never | 3K emails/mo free |
| **Sentry/Monitoring** | ₹0 | Never | Free tier |
| **Buffer/Emergency** | ₹2,800 | Anytime | Extra AI credits if needed |
| **TOTAL** | **₹10,000** | | **No other spend. Ever.** |

**Production AI = Gemini 1.5 Pro Free Tier (1.5M tokens/day) + Local Ollama for parsing.**

---

## 🛠 TECHNOLOGY STACK (ALL FREE TIERS)

### **Frontend & Hosting**
```
Next.js 14 (App Router) + TypeScript + Tailwind CSS
├── Vercel (Hobby) — Hosting, Serverless Functions, Cron
├── Clerk (Free) — Auth (10K MAU), User Management
└── shadcn/ui + Radix — Component Library (copy-paste, no deps)
```

### **Backend & Database**
```
Supabase (Free Tier)
├── PostgreSQL — Primary DB (Prisma ORM)
├── Auth — Backup/SSO (Clerk primary)
├── Storage — File uploads (1GB free)
├── Realtime — Live progress updates
└── Edge Functions — Background jobs (Vercel Cron preferred)
```

### **Parsing & Processing**
```
Python (FastAPI) on Vercel Serverless — Heavy lifting
├── pdfplumber — PDF table extraction
├── pandas — CSV normalization
├── decimal.js — Financial math precision
├── Zod — Schema validation (shared TS/Python via JSON Schema)
└── Ollama (Local) — Llama 3.1 8B for parsing (free, offline)
```

### **AI Integration (Development Only)**
```
Claude Code (Primary) — Architecture, Code, Debug, Tests
ChatGPT/Claude Web — Research, Prompt Engineering, Documentation
Gemini 1.5 Pro API (Free Tier) — Production reasoning (1.5M tokens/day)
OpenAI/Anthropic API (₹6K budget) — Dev debugging, complex reasoning
```

### **Observability & Ops**
```
Sentry (Free) — Error Tracking
PostHog (Free) — Product Analytics
Upstash Redis (Free) — Job Queues (10K req/day)
Resend (Free) — Transactional Emails
GitHub Actions — CI/CD (Free)
```

---

## 📁 PROJECT STRUCTURE

```
3pl-audit/
├── .github/
│   └── workflows/           # CI/CD pipelines
├── apps/
│   ├── web/                 # Next.js 14 Frontend
│   │   ├── src/
│   │   │   ├── app/         # App Router pages
│   │   │   │   ├── (auth)/  # Login, Register, Callback
│   │   │   │   ├── (dashboard)/
│   │   │   │   │   ├── upload/
│   │   │   │   │   ├── contracts/
│   │   │   │   │   ├── invoices/
│   │   │   │   │   ├── disputes/
│   │   │   │   │   └── settings/
│   │   │   │   └── api/     # API Routes
│   │   │   ├── components/  # React Components
│   │   │   │   ├── ui/      # shadcn/ui components
│   │   │   │   ├── forms/   # Form Components
│   │   │   │   └── charts/  # Data Viz
│   │   │   ├── lib/         # Client Utilities
│   │   │   ├── hooks/       # Custom Hooks
│   │   │   └── types/       # TypeScript Types
│   │   ├── prisma/          # Prisma Schema
│   │   └── package.json
│   │
│   └── parser/              # Python FastAPI Parser Service
│       ├── src/
│       │   ├── parsers/     # 3PL-Specific Parsers
│       │   │   ├── contracts/
│       │   │   │   ├── base.py
│       │   │   │   ├── shipbob.py
│       │   │   │   ├── shipmonk.py
│       │   │   │   ├── quiets.py
│       │   │   │   └── registry.py
│       │   │   ├── invoices/
│       │   │   │   ├── base.py
│       │   │   │   ├── shipbob.py
│       │   │   │   └── registry.py
│       │   │   └── actuals/
│       │   │       ├── shopify.py
│       │   │       └── wms.py
│       │   ├── engine/      # Core Reconciliation Logic
│       │   │   ├── rates.py
│       │   │   ├── reconcile.py
│       │   │   ├── disputes.py
│       │   │   └── evidence.py
│       │   ├── schemas/     # Pydantic Models (mirror Prisma)
│       │   └── main.py      # FastAPI Entry
│       ├── tests/           # Pytest + Property-Based Tests
│       ├── requirements.txt
│       └── Dockerfile       # For local dev
│
├── packages/
│   ├── shared-types/        # Shared Zod/Pydantic Schemas (JSON Schema source)
│   ├── rate-engine/         # Pure TS Rate Calculation (npm package)
│   └── dispute-generator/   # React-PDF Dispute Packets
│
├── docs/
│   ├── SPEC.md              # Master Specification
│   ├── ADR/                 # Architecture Decision Records
│   ├── pilot-playbook.md    # Pilot Onboarding Process
│   └── api-contracts.md     # API Documentation
│
├── scripts/
│   ├── dev-setup.sh         # One-Command Setup
│   ├── seed-pilot-data.ts   # Pilot Data Seeding
│   └── deploy.sh            # Deployment Script
│
├── docker-compose.yml       # Local Dev Stack
├── turbo.json               # Turborepo Config
├── package.json             # Root Package.json
└── README.md
```

---

## 📋 WEEK-BY-WEEK EXECUTION PLAN

### **PHASE 0: FOUNDATION (Weeks 1-2) — ZERO CODE KNOWLEDGE REQUIRED**

#### **WEEK 1: Environment & First Deploy**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Install tools: Node 20, Python 3.11, Docker, Vercel CLI, Supabase CLI, Git | `"List exact commands to install Node 20, Python 3.11, Docker, Vercel CLI, Supabase CLI on Ubuntu/WSL. Verify versions."` | All tools installed |
| Tue | Init Turborepo + Next.js + Python FastAPI | `"Create Turborepo with apps/web (Next.js 14 TS Tailwind) and apps/parser (FastAPI). Include shared packages. Give me exact file structure."` | `git init` + structure |
| Wed | Supabase Project + Prisma Schema (Core Models) | `"Write Prisma schema for: User, Organization, Contract, Invoice, RateRule, Dispute, AuditLog. Include relations, indexes, RLS policies."` | `prisma/schema.prisma` |
| Thu | Clerk Auth + Vercel Deploy | `"Configure Clerk in Next.js 14 App Router. Middleware for auth protection. Deploy to Vercel with env vars."` | Live at `*.vercel.app` |
| Fri | **SHIP: Auth + Empty Dashboard** | `"Build protected dashboard layout with sidebar, header, user menu. shadcn/ui components only."` | **Friday Deploy ✅** |
| Sat | **PILOT HUNT: LinkedIn 20 messages** | `"Write 5 LinkedIn DM templates for D2C founders: 'I'm building 3PL audit tool. Free audit of 3 invoices. Interested?'` | 3+ Replies |
| Sun | **WRITE: SPEC.md v1** | `"Create SPEC.md template: Problem, Users, Input/Output Schemas, Edge Cases, Success Metrics. I'll fill it."` | `docs/SPEC.md` |

#### **WEEK 2: File Upload & Parsing Pipeline**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | File Upload UI → API → Supabase Storage | `"Build drag-drop upload zone (react-dropzone). API route: validate file type/size → upload to Supabase Storage → create DB record → return file ID."` | Upload works |
| Tue | Background Job Queue (Upstash + Vercel Cron) | `"Set up Upstash Redis queue. Vercel Cron every 1 min processes jobs. Job types: parse_contract, parse_invoice, reconcile. TypeScript types."` | Queue works |
| Wed | PDF Text Extraction (pdfplumber) | `"Python FastAPI endpoint: download file from Supabase → pdfplumber extract tables + text → return raw JSON. Handle multi-page, rotated, scanned."` | Parser extracts text |
| Thu | CSV Parsing (pandas) | `"FastAPI endpoint: parse CSV → detect 3PL format (ShipBob, ShipMonk, etc.) → normalize to standard schema → return JSON."` | CSV → Standard JSON |
| Fri | **SHIP: Upload → Parse → View Raw JSON** | `"Dashboard: Upload file → shows processing status → displays raw parsed JSON in collapsible tree view."` | **Friday Deploy ✅** |
| Sat | **PILOT HUNT: Get 1st Contract + Invoice** | `"Follow up LinkedIn replies. Ask for: 1 contract PDF, 3 invoice CSVs, Shopify export (orders). Offer NDA."` | Pilot #1 Data |
| Sun | **WRITE: Parser Specs for Pilot's 3PL** | `"Document exact column names, table structures, rate tiers from Pilot #1's files. Screenshots + annotations."` | `docs/parsers/pilot1.md` |

---

### **PHASE 1: CONTRACT & INVOICE PARSERS (Weeks 3-6)**

#### **WEEK 3: Contract Parser v1 (Single 3PL Format)**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Design RateRule Schema (Zod + Pydantic) | `"Define Zod schema for RateRule: chargeType, tiers[], unit, minimums, surcharges, effectiveDate, sourceClause. Mirror in Pydantic."` | `packages/shared-types/` |
| Tue | Write 10 Test Cases (Excel → JSON) | `"From Pilot #1 contract, write 10 test cases: input (PDF page text) → expected RateRule[]. Include edge: tiered storage, peak surcharge, minimums."` | `tests/contract_test_cases.json` |
| Wed | Build Contract Parser (Python) | `"Implement parse_contract(pdf_text, 3pl_type) → RateRule[]. Use pdfplumber table extraction + regex for tier patterns. Handle Pilot #1 format."` | `parsers/contracts/pilot1.py` |
| Thu | Unit Tests (Pytest + Property-Based) | `"Write pytest tests for all 10 cases. Add fast-check property tests: tiers sorted, no overlaps, rates positive."` | 100% Pass |
| Fri | **SHIP: Contract Upload → RateRules JSON** | `"Dashboard: Upload contract → shows extracted RateRules in editable table → 'Confirm & Save' button."` | **Friday Deploy ✅** |
| Sat | **PILOT HUNT: Pilot #2 & #3** | `"Message 20 more founders. Target: different 3PLs (ShipMonk, Quiet, Delhivery)."` | 2 More Pilots |
| Sun | **WRITE: Parser Registry Design** | `"Design plugin registry: auto-detect 3PL from PDF metadata/headers. Config-driven parser selection."` | `parsers/registry.py` |

#### **WEEK 4: Invoice Parser v1**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Standard Invoice Line Schema | `"Zod/Pydantic: InvoiceLine { chargeCode, description, qty, unit, rate, amount, dateRange, metadata }. Map 5 3PL formats."` | Schema Done |
| Tue | 10 Invoice Test Cases | `"From Pilot #1 invoices, write 10 test cases: raw CSV rows → normalized InvoiceLine[]. Include: storage, pickpack, receiving, shipping, surcharges."` | Test Cases |
| Wed | Build Invoice Parser | `"Implement parse_invoice(csv_bytes, 3pl_type) → InvoiceLine[]. Auto-detect columns via header fuzzy match. Handle multi-sheet Excel."` | Parser Works |
| Thu | Tests + Edge Cases | `"Test: missing columns, merged cells, negative amounts, tax lines, credits, adjustments."` | 100% Pass |
| Fri | **SHIP: Invoice Upload → Standardized Lines** | `"Dashboard: Upload invoice → shows normalized lines grouped by charge type → totals per type."` | **Friday Deploy ✅** |
| Sat | **PILOT DATA: Get Actuals (Shopify/WMS)** | `"Ask pilots for: Shopify orders export (last 3 mo), WMS inventory snapshots, return reports."` | Actuals Data |
| Sun | **WRITE: Actuals Schema** | `"Define schemas for: OrderActual, InventorySnapshot, ReturnActual. Map Shopify/WMS fields."` | Schema Done |

#### **WEEK 5: Rate Calculation Engine (Pure Logic — Most Critical)**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | **DESIGN: Pure Functions Spec** | `"Write SPEC for rate-engine package: calculateStorage(tiers, actualPallets, days), calculatePickPack(tiers, orders, linesPerOrder), calculateReceiving(tiers, pallets, floorLoaded), calculateShipping(discount, zone, weight, dim). No I/O. Decimal.js only."` | `packages/rate-engine/SPEC.md` |
| Tue | Implement Storage Engine | `"Implement calculateStorage: tiered rates, free days, minimum pallets, long-term storage surcharge. 20 test cases from Pilot data."` | `rate-engine/src/storage.ts` |
| Wed | Implement Pick/Pack + Receiving | `"calculatePickPack: base + per-line, volume tiers, kitting fees. calculateReceiving: palletized vs floor-loaded, unloading labor."` | `rate-engine/src/pickpack.ts` |
| Thu | Implement Shipping Engine | `"calculateShipping: contract discount %, carrier published rate, DIM divisor, zone, weight, surcharges. Compare to invoice."` | `rate-engine/src/shipping.ts` |
| Fri | **SHIP: Rate Engine npm Package + Tests** | `"Publish to local npm. 100% coverage. Property tests: monotonicity, boundary values. Dashboard: 'Test Calculator' page."` | **Friday Deploy ✅** |
| Sat | **PILOT: Manual Reconciliation in Excel** | `"For Pilot #1: manually reconcile 3 invoices in Excel using your rate engine logic. Document every discrepancy found."` | Excel Proof |
| Sun | **WRITE: Reconciliation Algorithm Spec** | `"Design three-way match: Contract RateRules × InvoiceLines × Actuals → Variance[]. Confidence scoring. Evidence linking."` | `docs/reconciliation-spec.md` |

#### **WEEK 6: Three-Way Reconciliation Engine**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Variance Data Model | `"Zod: Variance { lineId, chargeType, contractRate, invoiceRate, actualQty, billedQty, varianceAmount, variancePct, confidence, evidence[] }"` | Schema |
| Tue | Matching Algorithm (Fuzzy) | `"Implement matchLines(contractRules, invoiceLines, actuals): 1) Group by chargeType 2) Fuzzy match by description/code 3) Apply rate engine 4) Compare to invoice 5) Verify against actuals."` | `engine/reconcile.py` |
| Wed | Confidence Scoring | `"Confidence factors: exact code match (+0.3), description similarity (+0.2), qty within 5% (+0.3), rate within 2% (+0.2). Min 0.6 to auto-flag."` | Scoring Works |
| Thu | Evidence Pack Generation | `"For each variance: collect contract clause, invoice line, actuals screenshot, rate calc steps. Output structured evidence bundle."` | Evidence Bundle |
| Fri | **SHIP: Full Reconciliation Pipeline** | `"Dashboard: Upload Contract + Invoices + Actuals → 'Run Audit' → Shows Variance List with Confidence → Click to View Evidence."` | **Friday Deploy ✅** |
| Sat | **PILOT #1: Run Real Audit** | `"Process Pilot #1's 3 months data. Generate dispute packet. Review with them on call."` | Real Results |
| Sun | **ITERATE: Fix Parser/Engine Gaps** | `"List every mismatch from Pilot #1. Fix parsers. Add edge case tests. Commit."` | Fixes Done |

---

### **PHASE 2: DISPUTE GENERATION & PILOT VALIDATION (Weeks 7-12)**

#### **WEEK 7: Dispute Packet Generator**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Design Packet Template | `"React-PDF template: Cover Letter, Executive Summary, Variance Detail Tables (per charge type), Evidence Appendix, Contract Clause Citations, Pre-filled Email to 3PL."` | `dispute-generator/template.tsx` |
| Tue | Build PDF Generator | `"Implement generateDisputePacket(variances, contract, brandInfo) → PDF Blob. Professional formatting, page numbers, brand logo."` | PDF Output |
| Wed | Pre-filled Email Generator | `"Generate email: subject, body with variance summary, attachment reference, 3PL contact placeholder, escalation path."` | Email Template |
| Thu | Brand Customization | `"Allow brand to upload logo, set signatory, customize tone (firm/collaborative), add custom clauses."` | Customizable |
| Fri | **SHIP: One-Click Dispute Packet** | `"Dashboard: Variance List → checkboxes → 'Generate Packet' → Download PDF + Copy Email → 'Mark Sent' tracking."` | **Friday Deploy ✅** |
| Sat | **PILOT #1: Send Real Dispute** | `"Help Pilot #1 send dispute to 3PL. Track response. Document outcome."` | Real World Test |
| Sun | **WRITE: Dispute Tracking Spec** | `"Design: Sent Date, 3PL Response, Resolution (Full/Partial/Denied), Recovered Amount, Days to Resolve."` | Spec Done |

#### **WEEK 8: Pilot #2 & #3 Onboarding**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon-Tue | Build Parsers for Pilot #2 & #3 3PLs | `"Copy Pilot #1 parser → adapt for ShipMonk/QuietLogistics/Delhivery format. 2 days each."` | 2 New Parsers |
| Wed-Thu | Run Full Audits for Both | `"Process their 3 months data. Generate packets. Review on calls."` | 2 More Audits |
| Fri | **SHIP: Multi-3PL Support** | `"Dashboard: 'Add 3PL' wizard → auto-detect format → test parse → save config."` | **Friday Deploy ✅** |
| Sat-Sun | **ITERATE: Parser Framework Hardening** | `"Abstract common patterns. Config-driven column mapping. Regex library for rate extraction."` | Framework v1 |

#### **WEEK 9: Dashboard Polish & Pilot Feedback Loop**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Audit History & Trends | `"Dashboard: Past audits table → trend charts (variance % over time, by charge type, by 3PL). Recharts."` | Trends View |
| Tue | Dispute Tracker | `"Kanban board: Draft → Sent → Responded → Resolved. Per-dispute timeline. Email threading."` | Tracker |
| Wed | Settings & Team | `"Organization settings: 3PL configs, team invites (Clerk), billing, notification preferences."` | Settings |
| Thu | Pilot Feedback Session | `"30-min call each pilot: 'What's missing? What's wrong? What would make you pay?'"` | Feature List |
| Fri | **SHIP: Pilot Feedback Features** | `"Implement top 3 requests from each pilot."` | **Friday Deploy ✅** |
| Sat-Sun | **PRICING: Finalize with Pilots** | `"Ask: 'If this saves you $X/mo, what's fair monthly price?' Get 3 verbal commitments."` | Pricing Set |

#### **WEEK 10: Stripe Billing Integration**
| Day | Task | AI Prompt | Deliverable |
|-----|------|-----------|-------------|
| Mon | Stripe Products & Prices | `"Create Stripe: 3 tiers (Starter $299, Growth $999, Enterprise $2999). Usage-based: $50/invoice audited over limit."` | Stripe Config |
| Tue | Checkout Portal | `"Clerk + Stripe: Checkout session → success page → webhook → provision subscription → email receipt."` | Checkout Works |
| Wed | Customer Portal | `"Stripe Billing Portal: manage subscription, update payment, download invoices, cancel."` | Portal Works |
| Thu | Usage Tracking & Limits | `"Track invoices audited/month. Hard limit on tier. Email at 80%, 100%. Grace period."` | Limits Enforced |
| Fri | **SHIP: Paid Plans Live** | `"Dashboard: 'Upgrade' button → Checkout → Portal. Free trial: 1 audit free."` | **Friday Deploy ✅** |
| Sat | **PILOT CONVERSION: Ask for Card** | `"Convert 3 pilots to paid. 'Your trial ends Friday. $299/mo continues.'"` | **First $** |
| Sun | **METRICS: Define North Star Dashboard** | `"PostHog: MRR, Audits Run, Variance Found, Dispute Sent, Conversion Rate, Churn."` | Analytics Live |

#### **WEEK 11-12: Hardening & Scale Prep**
| Week | Focus | Key Tasks |
|------|-------|-----------|
| **11** | **Reliability** | Error boundaries, retry logic, rate limiting, audit logs, backup/restore, load test (k6), cost optimization |
| **12** | **Documentation** | API docs, user guide, parser development guide, ADRs, pilot playbook, runbooks |

---

### **PHASE 3: GTM & SCALE (Weeks 13-28)**

#### **WEEKS 13-16: OUTBOUND ENGINE**
| Week | Focus | Target |
|------|-------|--------|
| 13 | **Cold Email System** | 50/day → 5 replies/week |
| 14 | **Content Marketing** | 2 LinkedIn posts/week + 1 case study |
| 15 | **Partner Channel** | 3PL consultants, ecomm agencies, fractional CFOs |
| 16 | **SEO Foundation** | "3PL audit", "fulfillment overcharges", "warehouse invoice audit" |

#### **WEEKS 17-20: PRODUCT-MARKET FIT**
| Week | Focus | Target |
|------|-------|--------|
| 17 | **Self-Serve Onboarding** | Zero-touch trial → audit → packet |
| 18 | **AI Parser Builder** | Upload contract → AI suggests parser → human approves |
| 19 | **3PL Portal** | 3PLs respond to disputes in-app → faster resolution |
| 20 | **$10K MRR** | 10-15 paying customers |

#### **WEEKS 21-28: SCALE & MOAT**
| Week | Focus | Target |
|------|-------|--------|
| 21-22 | **Predictive Analytics** | "Next month's overcharges" based on trends |
| 23-24 | **Benchmarking** | "Your storage cost 18% above peer median" |
| 25-26 | **Marketplace** | 3PL performance ratings, switching recommendations |
| 27-28 | **$25K MRR / YC Application** | 40+ customers, $300K ARR, ready for seed |

---

## 🤖 AI PROMPT TEMPLATES (COPY-PASTE FOR EVERY TASK)

### **For New Feature (Use Every Monday)**
```markdown
## CONTEXT
- Project: 3PL Billing Audit Engine
- Stack: Next.js 14, Supabase, Prisma, Python FastAPI, Tailwind
- Current State: [DESCRIBE WHAT WORKS]
- User: [PILOT FEEDBACK OR YOUR NEED]

## TASK
Build: [SPECIFIC FEATURE]
Input: [EXACT DATA FORMAT]
Output: [EXACT DATA FORMAT]
Edge Cases: [LIST FROM SPEC.md]

## CONSTRAINTS
- No new dependencies without approval
- Must pass existing tests
- Follow patterns in [REFERENCE FILE]
- TypeScript strict / Python type hints
- Decimal.js for money

## DELIVERABLE
1. Implementation files
2. Test cases (happy + edge)
3. Updated SPEC.md if changed
4. One-sentence summary for CHANGELOG
```

### **For Debugging (When Stuck)**
```markdown
## ERROR
[PASTE FULL STACK TRACE OR ERROR MESSAGE]

## CODE
[PASTE RELEVANT FILES - use `@file` in Claude Code]

## WHAT I TRIED
[LIST ATTEMPTS]

## EXPECTED BEHAVIOR
[WHAT SHOULD HAPPEN]

## MINIMAL REPRODUCTION
[STEPS TO REPRODUCE]
```

### **For Parser Development (New 3PL)**
```markdown
## NEW 3PL PARSER: [3PL NAME]
## SAMPLE FILES
[ATTACH 2-3 CONTRACT PAGES + 2-3 INVOICE CSVs]

## EXTRACT THESE RATE PATTERNS
1. Storage: [TIER STRUCTURE FROM SAMPLE]
2. Pick/Pack: [RATE STRUCTURE]
3. Receiving: [RATE STRUCTURE]
4. Shipping: [DISCOUNT PASS-THROUGH]
5. Surcharges: [LIST FROM CONTRACT]

## OUTPUT FORMAT
Match RateRule schema exactly. Include sourceClause references.
Write 10 test cases from samples.
```

---

## 📊 SUCCESS METRICS (TRACK WEEKLY)

| Metric | Week 4 | Week 8 | Week 12 | Week 20 | Week 28 |
|--------|--------|--------|---------|---------|---------|
| **Parsers Built** | 1 | 3 | 5 | 10 | 20 |
| **Pilots Active** | 1 | 3 | 5 | 15 | 40 |
| **Audits Run** | 3 | 20 | 50 | 200 | 1000 |
| **Variance Found/Audit** | $5K | $8K | $12K | $15K | $20K |
| **Disputes Sent** | 0 | 5 | 20 | 100 | 500 |
| **Recovery Rate** | - | 40% | 50% | 60% | 70% |
| **Paying Customers** | 0 | 0 | 3 | 15 | 40 |
| **MRR** | $0 | $0 | $1K | $10K | $25K |
| **AI Spend/Month** | ₹500 | ₹1,500 | ₹2,000 | ₹0 (Gemini) | ₹0 |

---

## ⚠️ RISK REGISTER & MITIGATION

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| **Can't parse 3PL PDF formats** | High | High | Start with CSV exports (all WMS have them). PDF = Phase 2. Use Ollama vision locally. |
| **Pilot ghosts / won't share data** | Medium | High | 10 parallel conversations. NDA template ready. Free audit = low friction. |
| **Reconciliation logic errors** | High | Critical | **Property-based testing (fast-check)**. Manual Excel verification for every pilot. |
| **AI API costs exceed ₹6K** | Medium | Medium | Strict budgets. Local Ollama for parsing. Gemini free tier for reasoning. Cache aggressively. |
| **Vercel/Supabase free limits hit** | Low | Medium | Monitor usage. Optimize queries. Batch jobs. Upgrade only at revenue. |
| **3PLs change invoice formats** | High | Medium | Config-driven parsers. Alert on parse failures. Auto-detect + flag for review. |
| **You get stuck debugging** | Very High | High | **Write test case first**. Minimal repro. Ask AI with full context. Sleep on it. |
| **Motivation dip (Weeks 8-12)** | Very High | High | **Ship every Friday**. Public commitment. Pilot calls = accountability. Track metrics visually. |

---

## 🎓 LEARNING CHECKPOINTS (YOUR DEGREE)

| Week | Concept Mastered | Portfolio Evidence |
|------|------------------|-------------------|
| 2 | Full-stack deployment, Auth, CI/CD | Live app + GitHub Actions |
| 4 | File processing, Queue workers, PDF/CSV parsing | `packages/parser/` library |
| 6 | Domain modeling, Financial math, Algorithm design | `packages/rate-engine/` (npm) |
| 8 | Plugin architecture, Multi-tenant data | `parsers/registry.py` |
| 10 | SaaS billing, Webhooks, Subscription logic | Stripe integration |
| 12 | Observability, Testing, Documentation | `docs/` + Sentry + PostHog |
| 16 | GTM, Cold outreach, Content, Partnerships | `pilot-playbook.md` + Case Studies |
| 20 | Product-market fit, Self-serve, AI-assisted features | $10K MRR + Live Demo |
| 28 | Scale, Moats, Fundraising readiness | YC Application + $300K ARR |

---

## 📅 DAILY RITUAL (NON-NEGOTIABLE)

```
MORNING (30 min)
├── Check GitHub Actions (green?)
├── Check Sentry (new errors?)
├── Check Pilot Slack/Email (blockers?)
└── Write Today's SPEC (what ships Friday)

DEEP WORK (4-6 hours)
├── Mon-Tue: New Feature Implementation
├── Wed: Tests + Edge Cases
├── Thu: Integration + Pilot Validation
├── Fri: DEPLOY + Demo to Pilot
└── Sat: Pilot Outreach + Learning
    Sun: SPEC Writing + Planning

EVENING (15 min)
├── Commit + Push (clean history)
├── Update SPEC.md (what changed)
├── Log Metrics (Notion/Sheet)
└── Plan Tomorrow's First Task
```

---

## 🚫 NEVER DO LIST

- ❌ Add dependency without writing test first
- ❌ Skip tests "to move faster"
- ❌ Build feature without pilot asking for it
- ❌ Spend >₹500 without updating budget tracker
- ❌ Work on Sunday deep work (rest + SPEC only)
- ❌ Compare to funded startups (you're learning + earning)
- ❌ Rewrite working code (refactor only with failing test)
- ❌ Use paid AI for production (Gemini free + Ollama only)

---

## 📞 EMERGENCY CONTACTS (WHEN STUCK)

| Problem | Resource |
|---------|----------|
| **TypeScript Errors** | `claude-code` → `@file` paste error + file |
| **Python/Parser Bugs** | `claude-code` → full traceback + minimal repo |
| **Supabase/Prisma Issues** | Supabase Discord #help + Prisma GitHub Discussions |
| **Vercel Deploy Fails** | Vercel Docs + `vercel logs` |
| **Stripe Webhook Issues** | Stripe CLI `stripe listen --forward-to localhost:3000/api/webhooks` |
| **Parser Logic Wrong** | **Write failing test case in Excel first** → then ask AI |
| **Motivation Crash** | Call Pilot. See their pain. Ship one small fix. |

---

## 🏁 DEFINITION OF DONE (EACH FRIDAY)

- [ ] Feature deployed to `*.vercel.app`
- [ ] All tests pass (`pnpm test` + `pytest`)
- [ ] Pilot can use it without you explaining
- [ ] SPEC.md updated
- [ ] CHANGELOG.md entry
- [ ] Metrics logged
- [ ] Next week's SPEC drafted

---

## 🎯 YOUR FIRST COMMAND (RUN MONDAY 9 AM)

```bash
# In /workspaces/studious-bassoon
git init
npx create-turbo@latest --name=3pl-audit --package-manager=pnpm
cd 3pl-audit
# Follow WEEK 1 Day-by-Day above
```

---

**THIS FILE IS YOUR BOSS. READ IT EVERY MONDAY. UPDATE IT EVERY FRIDAY.**
**YOU HAVE ₹10K, 28 WEEKS, AND UNLIMITED AI. BUILD.**