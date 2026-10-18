# Product Requirements Document (PRD)

## CheapBazzar — Multi-Vendor E-commerce Marketplace

| Document field | Value |
|---|---|
| Product | CheapBazzar (repository spelling: **CheapBazzar**) |
| Document | Product Requirements Document |
| Version | 1.0 — Planning Draft |
| Status | Proposed; requirements require stakeholder review |
| Last updated | 18 October 2026 |
| Product stage | Pre-implementation / discovery |
| Primary market assumption | India |
| Reference product | Flipkart, for familiar e-commerce interaction patterns only |
| Source of truth | Approved product decisions in this PRD; detailed UI, system and architecture behaviour is elaborated in the companion documents |

> **Planning-only notice:** This PRD defines product intent and MVP boundaries. It does not authorise implementation, select unconfirmed providers, promise legal compliance, or imply that integrations have been built. Unknown business rules are explicitly marked **TBD** and must be resolved before the dependent feature is released.

---

## 1. Executive Summary

CheapBazzar is planned as a responsive, multi-vendor, multi-category online marketplace for customers in India. Customers will be able to discover products from approved independent sellers, compare relevant offers, place orders through a unified checkout, choose an eligible online payment method or Cash on Delivery (COD), and track fulfilment. Sellers will manage their own catalogue, inventory and fulfilment tasks through a seller dashboard. CheapBazzar administrators will control seller approval, catalogue moderation, marketplace policies, orders, payment/refund operations, support and reporting.

The product is inspired by familiar marketplace usability patterns found in services such as Flipkart. CheapBazzar must maintain its own brand, original UI implementation, content and assets; it must not copy another marketplace's proprietary branding or exact design.

The MVP must prove that the marketplace can safely complete its core transaction loop:

**Approved seller → accurate listing and stock → customer discovery → validated checkout → verified payment or COD order → seller fulfilment → shipment updates → delivery/exception handling → support and financial reconciliation.**

The MVP is intentionally focused on operational correctness, clear pricing, secure role separation and a dependable purchase journey. Personalised recommendations, loyalty systems, advanced experimentation and microservices are not launch requirements.

## 2. Problem Statement

Customers shopping across multiple online stores may need to compare prices, product details, seller trust, delivery estimates and return conditions across separate experiences. They can also lose confidence when final charges are unclear, stock is inaccurate, delivery updates are stale, or refunds are difficult to follow.

Small and independent sellers need a way to publish products and manage orders without building their own complete e-commerce infrastructure. A marketplace, however, must protect customers from misleading listings and protect sellers and the platform from invalid orders, inventory conflicts, payment ambiguity and disputes.

CheapBazzar aims to address these problems with one marketplace experience and shared operational controls. The product must make prices and charges transparent, make seller ownership and order responsibilities clear, and maintain traceable records for stock, payments, fulfilment, cancellations, returns and refunds.

### 2.1 Problem areas to solve

1. **Product discovery:** Customers need useful search, categories, filters and sorting to find suitable products.
2. **Price clarity:** Customers need a trustworthy breakdown of item prices, discounts, delivery charges, COD fees, applicable taxes and final payable amount before placing an order.
3. **Seller confidence:** Customers need seller identity and credible review information; the platform needs seller approval and moderation controls.
4. **Inventory correctness:** The system must not confirm unavailable stock or allow concurrent purchases to oversell inventory.
5. **Marketplace order complexity:** One cart can contain items from multiple sellers, potentially requiring separate seller fulfilment and shipments.
6. **Payment reliability:** An order must not be marked paid merely because the browser displays a success message. Payment state must be verified server-side and reconciled.
7. **Delivery visibility:** Customers need a realistic estimated delivery window and traceable shipment milestones.
8. **Post-purchase support:** Cancellation, returns, refunds and complaints need clear status and ownership.
9. **Operational control:** Admins need role-restricted tools to approve sellers, moderate listings and investigate exceptions.
10. **Performance and safety:** Core shopping flows must be responsive, accessible, monitored and protected against common application/API risks.

## 3. Product Vision and Value Proposition

### Vision

Build a dependable, affordable and easy-to-use Indian online marketplace where customers can compare products and buy with confidence, and approved sellers can reach customers without operating their own complete storefront infrastructure.

### Value proposition

- **For customers:** Clear prices, convenient discovery, transparent delivery estimates, choice of eligible payment methods and visible order/refund status.
- **For sellers:** A managed marketplace, catalogue tools, inventory controls, seller-specific order queues and transparent earnings/settlement records.
- **For platform operations:** Centralised seller approval, moderation, commission rules, order visibility, financial reconciliation and support workflows.

### Product principles

1. **Trust over misleading discounts:** Never fabricate a reference price, review count, seller rating or scarcity claim.
2. **Transparent checkout:** Disclose fees and the final payable amount before order placement.
3. **Correctness over cosmetic success:** Stock, payment and order state must be verified by the backend.
4. **Least privilege:** Every user sees and changes only the resources permitted by their role and ownership.
5. **Mobile-first usability:** Core journeys must work at narrow viewport widths and on slower connections.
6. **Operational traceability:** Sensitive changes and financial/order state transitions need an audit trail.
7. **Focused MVP:** Ship a complete, reliable transaction loop before advanced growth features.

## 4. Target Users and Stakeholders

### 4.1 Primary users

| User | Needs | MVP outcome |
|---|---|---|
| Guest visitor | Browse products, search, view product details and understand delivery/return information | Can discover products without being forced to register merely to browse |
| Customer/buyer | Account, addresses, cart, clear checkout, orders, tracking and support | Can place and follow an eligible order and find its current state |
| Seller applicant | Submit required business/contact details and receive an approval decision | Can see application status and next steps |
| Approved seller | Create/manage listings, stock, seller orders and fulfilment updates | Can manage only their own permitted resources and orders |
| Platform administrator | Approve/suspend sellers, moderate catalogue, oversee orders and resolve exceptions | Can manage marketplace operations with role-based permissions and auditability |
| Support/operations staff (permission-limited admin role) | Investigate order, delivery, return and refund issues | Can access only the customer/order information needed for assigned duties |

### 4.2 External stakeholders and dependencies

- Payment service provider supporting the intended marketplace flow.
- Courier/logistics partners and their tracking/label APIs, where selected.
- Email/SMS or other approved transactional notification provider.
- Hosting, database, file/image storage, CDN and monitoring providers.
- Legal/accounting advisers and relevant compliance stakeholders.

Provider selection, contracts, eligibility and technical capabilities are not yet confirmed. Features that depend on a provider must not be represented as available until tested end-to-end.

## 5. Goals and Non-Goals

### 5.1 Product goals

**G-01 — Complete core shopping:** A customer can discover an active product, add it to cart, complete checkout and view the resulting order.

**G-02 — Support a real marketplace:** Approved sellers can manage their own listings and fulfilment without receiving access to another seller's data.

**G-03 — Make checkout transparent:** The customer sees a server-calculated breakdown of items, discounts, shipping, COD charge and applicable taxes before confirmation.

**G-04 — Support online payment and COD:** Eligible orders can use a supported online payment method or COD, with payment/order states handled safely.

**G-05 — Track fulfilment:** Customers can see seller/shipment-level progress and an estimated delivery window, with automated updates where integrations support them.

**G-06 — Support post-purchase operations:** Cancellation, returns, refunds and support cases have defined status, ownership and traceability.

**G-07 — Protect platform integrity:** Authentication, authorisation, validation, audit logging, backups and monitoring are part of release readiness.

**G-08 — Establish a measurable performance baseline:** Core Web Vitals, API latency, error rates and critical-flow outcomes are measured under documented test conditions.

### 5.2 Non-goals for the MVP

The following are intentionally excluded from the initial release unless separately approved:

- Native Android/iOS apps; the MVP is a responsive web experience.
- Personalised recommendation models, AI shopping assistants or a personalised home feed.
- Loyalty points, memberships, gamification or referral programmes.
- Live seller/customer chat or real-time video support.
- International shipping, multi-currency checkout or multi-language rollout beyond the initial agreed language scope.
- Seller self-service advertising marketplace or complex sponsored-ranking auctions.
- Microservices, Kubernetes, event-streaming infrastructure or multi-region active-active architecture without demonstrated need.
- Automated warehouse robotics, own delivery fleet or guaranteed real-time GPS tracking where the courier does not supply it.
- Unverified promises of the lowest price in the market.
- Every possible product category or every courier/payment provider at launch.

## 6. MVP Scope and Prioritisation

Priorities use the following meanings: **P0 = release-blocking must-have**, **P1 = include if needed for a complete launch and provider/legal constraints allow**, **P2 = post-MVP**. A P0 item cannot be omitted merely to meet a date; if it is not ready, launch scope or launch date must be revisited.

### 6.1 P0 — MVP release essentials

1. Responsive customer storefront and category/catalogue browsing.
2. Product detail pages with multiple images, descriptions, prices, stock, seller identity and relevant policy/delivery information.
3. Search by relevant product fields; category and price filters; price sorting; pagination.
4. Customer registration/login, account profile and saved shipping addresses.
5. Cart quantity changes and removal, with stock and active-listing revalidation.
6. Server-side checkout pricing and final order summary.
7. Online payment integration with server-side verification, idempotency and reconciliation; COD only for eligible orders.
8. Seller application, admin approval/rejection/suspension and approved seller dashboard.
9. Seller product listing, edit/deactivate, inventory and seller-specific order queue.
10. Parent customer order plus seller-level fulfilment/order records for mixed-seller carts.
11. Order history and status timeline; delivery estimate and shipment tracking records.
12. Basic admin console for sellers, listings, categories, orders, pricing/promotion settings, exceptions and audit history.
13. Cancellation, return/refund request and status workflows consistent with the published policy.
14. Essential transactional notifications for order/payment/shipping/cancellation/refund events through the selected supported provider.
15. Security controls, structured error handling, logging, monitoring, database backup and restore verification.
16. Automated tests for critical purchase, permission, inventory and payment/order-state behaviour.
17. Required legal, consumer disclosures, privacy notice, terms, grievance/contact details and applicable compliance checks completed before public launch.

### 6.2 P1 — Strong marketplace experience

These are desirable for launch if capacity and policy decisions allow; they must not destabilise P0 transaction correctness:

- Wishlist.
- Product comparison where attributes are comparable.
- Verified-purchase reviews and review moderation.
- Configurable coupons and promotions with usage limits and audit history.
- Pincode-based delivery estimates using a documented source/rule.
- Customer support tickets and staff assignment.
- Seller ratings/performance reports based on defined metrics.
- Low-stock alerts and operational dashboards beyond the minimum.
- Additional notification channels and richer reporting.

**Scope decision:** Coupon/promotion logic needed to implement the stated online-payment discount belongs in P0. A general-purpose self-service coupon campaign builder can remain P1. Reviews may be P1, but if shown at launch, rating and review counts must be genuine and their source must be clear.

### 6.3 P2 — Growth phase

- Advanced recommendations and personalised ranking.
- Personalised home feed and experimentation framework.
- Multi-courier optimisation and automated courier selection across a broad network.
- Advanced seller advertising and sponsored placements.
- Advanced analytics, cohort analysis and experimentation.
- Loyalty, referral, membership and subscription programmes.
- Additional regions, languages, currencies and native apps.
- Dedicated services or infrastructure expansion when measured scale requires it.

## 7. Core Features and Product Requirements

### 7.1 Storefront and catalogue

- Homepage displays configurable category navigation, selected campaigns and active product collections.
- Product cards show a stable image area, product title, current price, valid reference/MRP information where permitted, genuine discount information and rating only when supported by valid data.
- Product detail pages support a primary image plus multiple additional images, description, specifications, seller, price, stock, variants when applicable and relevant delivery/return policy.
- Only active, eligible and visible listings appear in customer search.
- Category/subcategory and product attributes must be configurable without changing the core application for each new category.
- Listing content and image upload must be validated; prohibited or misleading content can be moderated or removed by authorised admins.
- The initial supported category list is TBD and must be agreed before catalogue launch.

### 7.2 Search, filters and sorting

- Search returns paginated results from supported product fields.
- MVP filters include category and price range; other filters depend on available catalogue attributes.
- MVP sorting includes price low-to-high and high-to-low. Relevance is the default when a search query is supplied.
- Empty results explain that no products match and offer a way to clear filters or modify the search.
- Invalid ranges, unsupported sort values and malformed queries are handled safely.
- Search must not expose inactive, suspended, prohibited or unauthorised seller listings.

### 7.3 Customer identity and addresses

- Customers can register, sign in, sign out, recover access and manage basic profile/contact details.
- Customers can save, edit and delete their own addresses.
- Protected actions require a valid authenticated session/token.
- Contact verification method and exact password/session policy must be documented in SRS and architecture before implementation.
- Browsing product pages should not require an account. Checkout authentication policy is TBD; recommended baseline is to require an account or verified identity before placing an order, while preserving the cart where possible.
- Personal data is visible only to roles with a legitimate operational need.

### 7.4 Cart and checkout

- Cart supports adding an eligible product/variant, updating quantity and removing items.
- Cart displays seller grouping when multiple sellers are present.
- At checkout, the backend revalidates product status, seller status, variant, stock, quantity, price, discounts, shipping serviceability and payment-method eligibility.
- The client cannot set authoritative unit price, discount, commission, tax or final total.
- Inventory reservation/decrement must be concurrency-safe and rollback/release behaviour must be defined for failed checkout/payment.
- The final summary shows item subtotal, each applicable discount, shipping fee, COD fee, applicable taxes and final payable total.
- Customer must explicitly confirm the final amount before placing the order.
- Duplicate submission/retry must not create duplicate orders or charges.
- A mixed-seller cart may use one customer-facing checkout, but fulfilment, shipment, cancellation/return and settlement must be traceable by seller/order item.

### 7.5 Pricing, discounts and fees

The platform must use one authoritative server-side pricing policy for product/variant price, eligible discounts, delivery fees, COD charges, applicable taxes and final payable amount.

Initial business direction supplied by the product owner:

- Online payment discount: **up to ₹100** on eligible orders.
- COD additional charge: **up to ₹100** on eligible COD orders.
- Online-payment delivery: intended to be free under the platform policy.
- COD orders: applicable delivery fee plus any disclosed COD charge.
- Product pricing should be competitive, but CheapBazzar must not make unsupported claims that every product is cheaper than all competitors.

The following policy details are **TBD and are release-blocking for the affected pricing flow**:

1. Minimum order value and maximum discount/charge per order.
2. Whether the online discount is a percentage, fixed amount, promotion-specific or other rule.
3. Whether discount applies to product subtotal, eligible items, each seller sub-order or the parent order.
4. Whether discounts can combine with seller discounts/coupons.
5. Delivery fee rules for mixed-seller carts, remote areas, failed delivery and returns.
6. COD eligibility limits, product restrictions and fraud controls.
7. Tax treatment and invoice representation of each fee/discount.
8. What happens to discounts, delivery charges and COD fees on partial cancellation/return/refund.

No fee can be hidden until after the customer confirms the order. Promotion eligibility and final amounts must be revalidated server-side at checkout.

### 7.6 Seller onboarding and listing management

- A seller submits required account/business details and any required verification information.
- Admin can approve, reject, request more information or suspend the seller; status and reason are recorded.
- Only approved and active sellers can publish eligible listings.
- After approval, sellers may publish eligible products without individual manual approval, as requested. Automated validation and ongoing admin moderation still apply.
- Seller can create/edit/deactivate only their own listings and manage their own stock.
- Product fields include name, description, category, images, price, stock, SKU and supported variant attributes.
- Seller must not be able to change platform-controlled order totals, payment state, commission records or another seller's data.
- Suspended sellers and their listings must follow a defined handling policy for existing orders and customer obligations.

### 7.7 Inventory

- Stock quantity must be a non-negative integer for countable products.
- The system must prevent purchase when available stock is insufficient.
- Concurrent checkout must not oversell the same inventory.
- Cart contents do not guarantee stock until the defined reservation/checkout rule applies.
- Inventory changes from orders, cancellations, returns and seller adjustments must be traceable.
- Low-stock alerts are P1 unless needed for safe launch operations.

### 7.8 Order and fulfilment management

- Store a customer-facing parent order and seller-specific fulfilment records for multi-seller carts.
- Preserve order-time snapshots of product title/variant, seller, unit price, discounts, applicable taxes and other amounts needed to explain the transaction.
- Customer can view order history and per-item/per-shipment status.
- Seller can see and update only authorised fulfilment statuses for their own order items.
- Admin can resolve exceptions with an audit log.
- Invalid status transitions must be rejected; status history records old/new status, actor, timestamp and reason where relevant.
- Parent order status must be derived from or reconciled with child fulfilment/payment states rather than blindly overwriting them.

### 7.9 Delivery and tracking

- The initial customer-facing estimate is intended to be **7–10 days after order confirmation**, but it is an estimate, not a guarantee.
- The estimate should be adjusted using serviceability, seller dispatch expectations, available courier estimates, holidays and other validated inputs. If no reliable estimate is available, the UI must say so rather than invent a date.
- Track meaningful milestones such as confirmed, processing, packed, shipped, in transit, out for delivery, delivered, delivery exception and returned-to-origin where supported.
- Automatic status updates use verified courier API/webhook data where the selected provider supports it.
- Manual admin/support corrections require authorisation, reason, actor and timestamp. A manual update must not be represented as verified GPS proof.
- Failed webhook/API updates need retry/reconciliation and operational alerts.
- Multiple seller shipments may have different delivery dates and tracking references under one customer order.
- Courier provider, serviceability coverage, label/AWB process, pickup model and return logistics are TBD.

### 7.10 Payments, COD, refunds and settlements

- Integrate a payment provider only after verifying suitability for the intended marketplace model, including seller onboarding/KYC, supported transfer/settlement flow, refunds and reconciliation.
- Verify payment results server-side; validate webhook signatures and safely process retries/duplicate events.
- Persist payment attempts and state transitions so pending, failed, successful and refunded states can be distinguished.
- Do not expose raw card credentials or store sensitive payment data unnecessarily.
- COD orders must record the selected method, eligibility decision, charge breakdown and collection/reconciliation status.
- Refund requests must be linked to the original payment/order/item and support full or partial refunds where policy/provider allows.
- Seller earnings, platform commission, adjustments, refunds and payouts require an auditable financial ledger or equivalent traceable records.
- A customer order must not be marked paid based only on a client-side redirect.
- Settlement schedule, reserve/hold policy, commission rate, payment provider, COD collection flow and financial approval thresholds are TBD.

### 7.11 Admin operations and moderation

- Admin can view/manage seller applications, seller status, categories, product listings, orders, pricing settings and operational exceptions according to permissions.
- Admin can remove or suspend prohibited, misleading or reported listings and suspend sellers under documented policy.
- Admin actions that affect money, access, seller status or customer obligations must be auditable.
- Use least-privilege permissions; support staff should not automatically receive finance or super-admin access.
- Refund approval thresholds, multi-person approval and exact admin role matrix are TBD.
- Administrative dashboards should expose actionable states, not merely decorative charts.

### 7.12 Cancellation, returns, reviews and support

- Customer can request cancellation/return for eligible orders/items under the published policy.
- Eligibility, reason, request timestamp, decision, pickup/inspection state where applicable and refund status must be traceable.
- Partial returns in multi-seller orders must not incorrectly cancel unrelated items or shipments.
- Reviews, if enabled at launch, must be linked to an eligible purchase and moderated for abuse; rating/count must not be fabricated.
- Customer support must be able to find the relevant order and record case status, owner, response and resolution.
- Exact return windows, category exceptions, replacement eligibility, warranty handling and support response targets are TBD and must be finalised before public launch.

### 7.13 Notifications

At minimum, send appropriate transactional updates for order confirmation, payment outcome, dispatch/tracking, cancellation and refund status when supported by the selected channel.

- Notifications must reflect backend-confirmed states.
- Failed delivery of a notification must not corrupt order/payment state.
- Avoid duplicate messages on webhook retries.
- Marketing communications must be separate from essential transactional messages and follow applicable consent/opt-out rules.
- Notification vendor, SMS/email templates, language and delivery guarantees are TBD.

## 8. User Stories

### 8.1 Guest and customer

- **US-01 — Browse:** As a guest, I want to browse categories and products so I can discover items before registering.
- **US-02 — Search:** As a customer, I want to search, filter and sort products so I can find relevant options quickly.
- **US-03 — Evaluate product:** As a customer, I want to see multiple images, specifications, seller information, stock, price and delivery/return information so I can make an informed decision.
- **US-04 — Manage account:** As a customer, I want to manage my profile and saved addresses so checkout is convenient.
- **US-05 — Manage cart:** As a customer, I want to change quantities and remove items so my cart reflects what I intend to buy.
- **US-06 — Understand total:** As a customer, I want to see each fee and discount before confirming so there are no surprise charges.
- **US-07 — Choose payment:** As a customer, I want to select an eligible online payment method or COD so I can use an available payment option.
- **US-08 — Avoid failed stock purchases:** As a customer, I want to be informed if price or stock changes before checkout so I can decide whether to continue.
- **US-09 — Track order:** As a customer, I want to see order and shipment progress so I know what has happened and what remains.
- **US-10 — Resolve an issue:** As a customer, I want to request eligible cancellation/return/refund and see status so I understand the resolution.
- **US-11 — Get help:** As a customer, I want to contact support about a specific order so the team can investigate it efficiently.

### 8.2 Seller

- **US-12 — Apply:** As a seller, I want to submit my details and see approval status so I know when I can start selling.
- **US-13 — Manage listing:** As an approved seller, I want to add/edit/deactivate my product listings so my catalogue stays accurate.
- **US-14 — Manage stock:** As a seller, I want to update stock and see low-stock conditions so I can reduce failed orders.
- **US-15 — Fulfil orders:** As a seller, I want to see only my order items and update permitted fulfilment states so I can process them.
- **US-16 — Understand earnings:** As a seller, I want to see sales, commission, adjustments, refunds and settlement status so I can reconcile earnings.

### 8.3 Admin and operations

- **US-17 — Approve sellers:** As an authorised admin, I want to review and approve/reject seller applications so only eligible sellers can publish.
- **US-18 — Moderate catalogue:** As an authorised admin, I want to investigate and moderate listings so prohibited or misleading content can be addressed.
- **US-19 — Resolve order exception:** As an authorised operator, I want to inspect order/payment/shipment events and record a justified correction so exceptions are traceable.
- **US-20 — Reconcile money:** As an authorised finance operator, I want to inspect payments, refunds, commissions and settlements so discrepancies can be resolved.
- **US-21 — Monitor health:** As an operator, I want alerts for critical failures so customer-impacting issues can be detected promptly.

## 9. Acceptance Criteria

Acceptance criteria are release gates and must be converted into automated/manual test cases. They are not proof of implementation until tests are executed and results recorded.

### 9.1 Discovery and catalogue

- **AC-01:** A guest can open active product/category pages without authentication.
- **AC-02:** Search, category/price filters and price sorting return matching, paginated results.
- **AC-03:** Inactive, suspended or prohibited listings do not appear as purchasable results.
- **AC-04:** Product details show the current supported price, stock state, seller and available product information without fabricated ratings/review counts.
- **AC-05:** Empty, loading, error and retry states are defined and usable on mobile and desktop.

### 9.2 Identity and permissions

- **AC-06:** Registration/login/logout/recovery behave according to the approved identity policy; secrets are not stored as plaintext.
- **AC-07:** A seller cannot read or modify another seller's listing, inventory, orders or settlement data by changing an identifier in a request.
- **AC-08:** A customer cannot read or modify another customer's private address/order data.
- **AC-09:** Non-admin users cannot invoke administrative or financial operations; forbidden access is denied server-side.
- **AC-10:** Sensitive admin actions record actor, timestamp, action and relevant reason/target.

### 9.3 Cart, price and inventory

- **AC-11:** Cart quantity updates reject non-positive or unsupported quantities and give clear feedback.
- **AC-12:** Checkout recalculates prices, discounts, fees, applicable taxes, stock and eligibility on the server.
- **AC-13:** The final price breakdown is visible before the customer confirms the order.
- **AC-14:** If price, stock, seller status or eligibility changes between cart and checkout, the system explains the change and requires a valid next action.
- **AC-15:** Concurrent purchases of the last available units cannot oversell stock.
- **AC-16:** Repeated checkout submission/retry cannot create duplicate orders or charge attempts for the same idempotency key.
- **AC-17:** Online-payment discount and COD fee respect the approved ₹100 cap and the final approved eligibility rules. This criterion remains blocked until those rules are specified.
- **AC-18:** A mixed-seller cart creates traceable seller-level fulfilment records while preserving a coherent customer-facing order.

### 9.4 Payment and order lifecycle

- **AC-19:** An order is not marked paid solely from client-side redirect/query parameters.
- **AC-20:** Invalid payment webhook signatures are rejected; duplicate valid events are processed idempotently.
- **AC-21:** Failed, pending and successful payment states are distinguishable and recoverable through defined flows.
- **AC-22:** COD is offered only when approved eligibility rules pass, and all fees are disclosed before order placement.
- **AC-23:** Order status transitions follow the approved state machine; invalid jumps are rejected and logged.
- **AC-24:** Full/partial cancellation and refund states remain linked to the relevant order items and original transaction.
- **AC-25:** Seller commission, refunds, adjustments and settlement records can be reconciled against order/payment records.

### 9.5 Seller, delivery and support

- **AC-26:** Only approved active sellers can publish eligible products.
- **AC-27:** An approved seller can publish eligible listings without per-listing manual approval, while automated validation and admin moderation remain available.
- **AC-28:** Sellers see only their own order items and can perform only permitted status transitions.
- **AC-29:** Delivery estimate is presented as an estimate and is not fabricated when reliable inputs are unavailable.
- **AC-30:** Automatic shipment updates are linked to their provider/event; manual overrides record operator, timestamp and reason.
- **AC-31:** Different shipments in a mixed-seller order can have different status and estimated delivery information.
- **AC-32:** Eligible return/refund/support requests receive a visible state and traceable resolution record.

### 9.6 Reliability, security and usability

- **AC-33:** Critical checkout, payment, inventory and access-control paths have automated tests.
- **AC-34:** API validation errors have a consistent, non-sensitive error format and request/correlation identifier.
- **AC-35:** Production secrets are not committed to the repository; sensitive data is not written into ordinary logs.
- **AC-36:** Backup restoration is tested before launch and recovery responsibilities are documented.
- **AC-37:** Critical service failures trigger monitoring/alerts and do not silently corrupt order/payment state.
- **AC-38:** The storefront, cart and checkout are tested on supported mobile/tablet/desktop viewport sizes with keyboard and form accessibility checks.
- **AC-39:** Performance tests report test setup, traffic profile, percentiles and results; targets are not declared met without evidence.
- **AC-40:** Required consumer disclosures, terms, privacy notice, refund/return policies and grievance/contact details are reviewed for the actual operating model before public launch.

## 10. Success Metrics

Metrics need baselines and target values agreed before launch. Targets below are initial measurement goals or engineering thresholds, not claims of current performance.

| Metric | Definition | Initial success criterion / target |
|---|---|---|
| Purchase flow completion | Share of started checkout sessions that result in a valid order | Establish baseline in pilot; set target after observing traffic and payment mix |
| Payment reconciliation | Successful provider transactions matched to correct internal payment/order records | 100% of successful transactions must be reconcilable; unresolved exceptions alert operations |
| Duplicate order/charge incidents | Duplicate orders or charges caused by retries/events | Zero known duplicates in critical-path test suite; monitor in production |
| Inventory overselling | Orders accepted beyond available sellable stock | Zero overselling in concurrency tests; monitor production exceptions |
| Price transparency | Checkout displays full final amount before confirmation | 100% of tested checkout scenarios show the complete applicable breakdown |
| Seller onboarding | Time from complete application to decision | Establish operational SLA after staffing and verification workflow are defined |
| Dispatch performance | Share of eligible seller orders dispatched within defined SLA | SLA and target TBD per category/seller model |
| Delivery performance | Share of delivered shipments arriving within the communicated estimate/window | Establish baseline by courier/service area; avoid a universal promise before evidence |
| Refund resolution | Time from approved refund to final provider/ledger state | Set target with payment-provider capability and policy |
| API performance | p95 latency for defined critical endpoints under documented load | Initial targets in SRS must be validated by load tests before adoption |
| Web performance | Core Web Vitals at the 75th percentile, segmented by mobile/desktop | LCP ≤ 2.5 s; INP ≤ 200 ms; CLS ≤ 0.1, measured with field data where available |
| Reliability | Availability and rate of critical errors | Define SLO after hosting, dependencies and pilot traffic are known |
| Support | First response and resolution time for customer cases | Define targets before public launch |
| Seller quality | Cancellation, stock mismatch, late dispatch and complaint rates by seller | Define fair metrics and thresholds before enforcement |
| Security | High/critical unresolved findings in launch review | No known unresolved critical/high findings at public launch; document accepted residual risks |

### Measurement principles

- Define each event and metric consistently before instrumenting analytics.
- Separate client-side events from server-confirmed business outcomes.
- Do not log unnecessary personal/payment data.
- Segment performance by device class and relevant route.
- Use small-pilot data as a baseline, not as proof of long-term marketplace health.

## 11. Assumptions and Decisions to Confirm

The following are working assumptions based on the current project description. They are not silently finalised business decisions.

| ID | Assumption / open decision | Required action |
|---|---|---|
| A-01 | India is the initial market and INR is the initial currency | Confirm market and currency |
| A-02 | CheapBazzar is a marketplace operator, not a single seller | Confirm operating/legal entity and marketplace model |
| A-03 | Seller approval is required; approved sellers may publish eligible listings without individual manual approval | Confirm seller verification and moderation policy |
| A-04 | One customer checkout may contain multiple sellers | Confirm parent order and seller-suborder customer experience |
| A-05 | Online payment discount is capped at ₹100 and COD additional charge is capped at ₹100 | Define calculation, eligibility and refund rules |
| A-06 | Online delivery is intended to be free; COD can have a delivery fee and COD charge | Define whether exceptions exist and how mixed-seller shipping is calculated |
| A-07 | 7–10 days is the initial delivery estimate after confirmation | Confirm estimate inputs, service areas and how the UI handles uncertain dates |
| A-08 | Platform coordinates delivery partners while sellers may pack/dispatch | Confirm pickup, packing, labels, courier booking and liability boundaries |
| A-09 | Marketplace-managed payment and seller settlement is intended | Select provider and verify its actual marketplace capabilities/compliance requirements |
| A-10 | Refunds, returns and cancellations are needed in MVP | Finalise category rules, windows, eligibility, logistics and settlement effects |
| A-11 | React + TypeScript, Node.js + Express + TypeScript and PostgreSQL are proposed in Architecture.md | Confirm after evaluating team capability, deployment, transactions and operational cost; PRD does not lock stack |
| A-12 | Product categories are configurable | Decide launch categories and restricted-category policy |
| A-13 | Email/SMS notifications are required for essential order events | Select providers, templates, consent rules and delivery-failure behaviour |
| A-14 | A responsive web application is the first release | Confirm supported browsers, accessibility scope and languages |
| A-15 | Seller KYC, tax, invoicing and regulatory obligations depend on the actual operating model | Obtain professional review before launch |

## 12. Risks and Mitigations

| Risk | Impact | Mitigation / owner |
|---|---|---|
| Unclear discount/fee rules | Incorrect totals, disputes and refund errors | Product/finance: approve a written pricing matrix and test cases before checkout release |
| Payment provider does not support the intended marketplace flow | Seller onboarding or settlements cannot operate as designed | Product/finance/engineering: validate provider capabilities, contracts, KYC and settlement model before committing |
| Overselling or stale stock | Cancellations and customer trust loss | Engineering: atomic inventory operations, concurrency tests and stock reconciliation |
| Client-trusted prices or payment status | Fraud and incorrect financial state | Engineering: authoritative server-side pricing and provider verification |
| Seller data leakage | Privacy and business harm | Engineering/security: resource-level and function-level authorization tests |
| Multi-seller order complexity | Wrong shipment, cancellation, commission or refund allocation | Product/engineering: explicit parent/suborder model and item-level records |
| Courier API downtime or missing events | Stale tracking and support burden | Operations/engineering: retry, reconciliation, alerting and audited manual exception workflow |
| Misleading delivery estimate | Customer complaints and policy exposure | Product/operations: estimate from validated inputs and display uncertainty honestly |
| Fake listings/reviews/discounts | Customer harm and marketplace reputation loss | Trust/operations: seller checks, moderation, verified-purchase evidence and audit logs |
| Security or privacy incident | Data loss, abuse and legal exposure | Security/engineering: secure design, minimal data access, monitoring, backups and incident plan |
| Poor performance on mobile/slow networks | Abandonment and poor accessibility | Frontend/engineering: image optimisation, profiling, real-device tests and field metrics |
| Regulatory requirements change or are misunderstood | Launch delay or non-compliance | Legal/operations: review currently applicable and upcoming rules against the actual launch date |
| MVP scope expands without capacity | Delayed launch and unfinished core flows | Product owner: use explicit P0/P1/P2 scope and require approval for changes |
| Single-provider dependency | Checkout or delivery disruption | Engineering/operations: define failure modes and recovery; consider alternatives only where justified |

## 13. Dependencies

Release-critical dependencies include:

1. Approved seller agreement, seller onboarding and moderation policy.
2. Approved pricing/discount/COD/delivery/return policy.
3. Payment provider confirmed for the marketplace model and required seller settlement flow.
4. Courier/logistics provider or an explicitly limited initial manual fulfilment process.
5. Hosting, managed database, file/image storage and backup capability.
6. Transactional email/SMS provider and verified templates.
7. Legal review of consumer disclosures, privacy, tax/invoicing and applicable marketplace/payment obligations.
8. Product category taxonomy and restricted/prohibited product policy.
9. Operational ownership for seller approval, support, refund exceptions and incident response.
10. QA environments and test credentials/sandbox for integrations.

A dependency is not considered complete merely because an account exists; the relevant end-to-end workflow must be tested.

## 14. MVP Release Gates

A public launch must not proceed until all gates below are met or an explicitly documented, authorised exception is approved.

- [ ] Product owner signs off MVP scope and all release-blocking TBD policies.
- [ ] Customer purchase journey works from discovery to order confirmation.
- [ ] Online payment verification, failure/retry and reconciliation pass sandbox and production-readiness tests.
- [ ] COD eligibility, fees, collection/reconciliation and exception workflow are defined and tested if COD is enabled.
- [ ] Seller approval, listing moderation and seller ownership permissions pass tests.
- [ ] Multi-seller order splitting, shipment tracking and item-level cancellation/return behaviour pass tests.
- [ ] Pricing and stock are recalculated server-side; concurrency and duplicate-request tests pass.
- [ ] Refund and settlement records can be reconciled.
- [ ] Critical security checks and role/ownership tests pass.
- [ ] Required policies, disclosures, privacy and grievance/contact information are published and reviewed.
- [ ] Backup restoration, monitoring, alerts and incident/rollback procedures are verified.
- [ ] Responsive, accessibility, performance and critical browser tests are recorded.
- [ ] No known unresolved critical/high security findings remain without formally accepted risk.
- [ ] Operations staff know how to handle payment, delivery, refund and support exceptions.
- [ ] Release owner approves go-live based on evidence, not assumptions.

## 15. Out of Scope

For clarity, these features are not required to satisfy the MVP:

- AI recommendations, personalised feeds and predictive search ranking.
- Loyalty points, referrals, memberships and gamified rewards.
- Live chat, seller livestreams and social commerce.
- Native mobile apps.
- International expansion and multi-currency payments.
- Automated warehouse management or CheapBazzar-owned delivery fleet.
- Full multi-courier optimisation engine.
- Seller advertising auction platform.
- Advanced data science/experimentation platform.
- Microservices/Kubernetes by default.
- Guaranteed lowest prices across all competitors.
- Guaranteed 7–10 day delivery in every pincode.
- Automatic approval of sellers without the required review.
- Fully automated returns/refunds for every category regardless of policy/provider capability.

## 16. UX and Accessibility Requirements

- Use familiar, predictable marketplace navigation and purchase conventions, with original CheapBazzar branding and UI assets.
- Make search, price, delivery estimate, stock, cart and primary purchase actions easy to locate.
- Support mobile, tablet and desktop layouts without horizontal overflow in core journeys.
- Provide loading, empty, error, success, disabled and retry states for important components.
- Use clear form labels, inline validation, keyboard navigation, visible focus and meaningful status announcements.
- Aim for at least WCAG AA text contrast (4.5:1 for normal text and 3:1 for large text, subject to WCAG definitions).
- Reserve image dimensions and avoid unexpected content shifts.
- Do not rely on colour alone to communicate errors, order status or payment outcomes.
- Confirm destructive or high-impact actions such as deactivating a listing or approving a refund.
- Respect reduced-motion preferences where animations are used.

## 17. Performance and Reliability Requirements

These are proposed product targets and must be validated against the final architecture and realistic test environment.

- **Web experience:** Target good Core Web Vitals at the 75th percentile: LCP ≤ 2.5 seconds, INP ≤ 200 milliseconds and CLS ≤ 0.1, segmented by mobile and desktop when sufficient field data exists.
- **API responsiveness:** Measure p50/p95 latency for catalogue, search, product detail, cart and checkout endpoints. Numeric API targets should be approved in SRS after the deployment and load profile are defined.
- **Checkout correctness:** Payment-provider latency must be reported separately from internal processing time.
- **Resilience:** External integration timeouts must not leave orders in ambiguous, untraceable states.
- **Observability:** Use structured logs, request/correlation IDs, error monitoring and alerts for critical failures.
- **Recovery:** Define backup frequency, recovery point objective (RPO) and recovery time objective (RTO) before launch; test restoration.
- **Scalability:** Begin with a maintainable modular architecture and scale from measured bottlenecks; avoid infrastructure complexity without evidence.

## 18. Security and Privacy Requirements

- Enforce authentication and role/resource-level authorisation on the server.
- Deny access by default to protected operations.
- Validate and normalise user input; use safe database access patterns and output encoding.
- Rate-limit authentication, checkout, coupon and other abuse-prone operations.
- Store passwords only as salted, appropriate password hashes.
- Protect sessions/tokens and keep secrets outside source control.
- Verify external webhook signatures and avoid trusting arbitrary callback payloads.
- Do not store raw card credentials or unnecessary sensitive information.
- Restrict access to customer addresses, seller verification data and financial records.
- Audit sensitive seller/admin/financial actions.
- Protect file uploads and use controlled storage paths.
- Establish retention, deletion/correction and incident-response procedures consistent with applicable law.
- Use OWASP API Security Top 10 as a review baseline, particularly object/function-level authorisation, authentication, resource consumption, security misconfiguration and unsafe external API consumption.

## 19. Legal and Compliance Readiness

CheapBazzar is planned for India, but this PRD is not legal or tax advice. Before public launch, qualified advisers and the selected providers must review the actual legal entity, marketplace model, seller relationship, payment flow and launch date.

Review at least:

- Applicable Consumer Protection (E-Commerce) Rules and amendments, including consumer disclosures, grievance handling, seller information, pricing/discount claims and search/sponsored-result transparency where applicable.
- Applicable GST, invoicing, tax collection/reporting and marketplace-operator obligations.
- Payment-provider requirements, seller KYC and settlement rules.
- Privacy/data-protection and information-security obligations.
- Product-category restrictions, licences, product safety and intellectual-property complaints.
- Published terms of service, privacy notice, shipping, cancellation, returns, refunds, warranty and grievance/contact process.

As of this document date, official Government of India material lists the Consumer Protection (E-Commerce) Rules, 2020 and 2021 amendments. A 10 September 2026 government announcement states that 2026 amendments are intended to come into force on 1 January 2027. The exact notified text and applicability must be checked against the actual launch date; do not rely on this PRD as a substitute for that review.

## 20. Open Questions — Decisions Required Before Implementation

The following decisions should be tracked to closure in a product decision log:

1. What exact categories and restricted categories are included at launch?
2. Is launch nationwide or limited to selected pincodes/cities?
3. What are the exact rules for the ₹100 online discount and up-to-₹100 COD charge?
4. Is free online delivery unconditional, or subject to serviceability/order/category exceptions?
5. How are shipping charges calculated for mixed-seller carts and multiple parcels?
6. What is the return window and eligibility by category, and who bears return shipping?
7. What seller commission model/rate and settlement schedule will apply?
8. Which payment provider supports the required marketplace settlement model and COD reconciliation?
9. Which courier/aggregator will provide serviceability, labels, tracking and return logistics?
10. What is the seller packing/dispatch SLA and what happens when it is missed?
11. Is customer account creation mandatory at checkout, and what verification is required?
12. Which language(s), browsers and accessibility support level are required at launch?
13. What are the initial traffic, seller, catalogue and order-volume assumptions?
14. What support hours, escalation paths and refund approval limits apply?
15. Which technology/hosting options are approved, and what is the operating budget?
16. What are the data retention, backup RPO/RTO and incident-response requirements?
17. Which policies and legal disclosures are approved for the actual business model and launch date?

No unconfirmed answer should be silently encoded as a product rule.

## 21. Alignment with Companion Documents

This PRD defines the product requirements and MVP boundary. The companion documents should be reconciled to it before implementation:

- **UI-UX.md:** Screen inventory, responsive states, components and user flows must cover the agreed MVP, including mixed-seller cart, fee breakdown, payment states, order tracking and return/refund status.
- **SRS.md:** Convert each P0 requirement into uniquely identified, testable functional/non-functional requirements, validations, permission rules, state transitions and error cases. Current simplified requirements need expansion before development.
- **Architecture.md:** Validate the proposed stack and define seller suborders, inventory concurrency, pricing, payment verification, financial ledger, shipment events, notifications, observability and backup/recovery boundaries.
- **Development-Plan.md:** Sequence work by dependencies; payment, COD, multi-seller fulfilment, refunds, security and operational readiness must not be omitted from the launch plan.

If a companion document conflicts with an approved product decision, record the discrepancy and update the relevant document; do not quietly implement contradictory behaviour.

## 22. Research References

The following authoritative references informed the product's security, performance and compliance planning. They are reference material, not a legal opinion or a claim that the application is compliant.

1. **Government of India — Department of Consumer Affairs, Consumer Protection Acts and Rules**  
   https://consumeraffairs.gov.in/pages/consumer-protection-acts  
   Reference for the Consumer Protection (E-Commerce) Rules, 2020 and listed amendments.

2. **Press Information Bureau — Government of India, 10 September 2026, “Government amends E-Commerce Rules to strengthen consumer protection and promote transparency”**  
   https://www.pib.gov.in/newsite/erelcontent.aspx?lang=2&reg=48&relid=294532  
   Announcement describes 2026 amendments and intended 1 January 2027 commencement; verify final notified text and applicability before launch.

3. **OWASP — API Security Top 10 (2023)**  
   https://api-security.owasp.org/editions/2023/en/0x11-t10/  
   Reference for object-level/function-level authorisation, authentication, resource-consumption and other API risks.

4. **Google web.dev — Web Vitals**  
   https://web.dev/articles/vitals  
   Reference for LCP, INP and CLS and the recommended good thresholds measured at the 75th percentile.

5. **Google web.dev — Defining Core Web Vitals thresholds**  
   https://web.dev/articles/defining-core-web-vitals-thresholds  
   Background on performance thresholds and measurement methodology.

## 23. Document Approval

| Role | Name | Status |
|---|---|---|
| Product owner | TBD | Pending review |
| Engineering/architecture | TBD | Pending review |
| Operations/finance | TBD | Pending review |
| Legal/compliance reviewer | TBD | Pending review |

**Approval rule:** This PRD becomes the approved implementation baseline only after the product owner reviews the scope, pricing/delivery/payment decisions and open questions. Until then, it remains a detailed planning draft.
