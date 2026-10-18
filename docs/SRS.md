# Software Requirements Specification (SRS)

## CheapBazzar — Multi-Vendor Marketplace

| Field | Value |
|---|---|
| Version | 1.0 — Planning Draft |
| Status | Proposed; pending review |
| Updated | 18 October 2026 |
| Market/currency assumption | India / INR |
| UX reference | Flipkart interaction conventions only; original CheapBazzar brand, assets and implementation |
| Related docs | [PRD](PRD.md) · [UI/UX](UI-UX.md) · [Architecture](Architecture.md) · [Development Plan](Development-Plan.md) |

> Planning-only: this specifies intended behaviour, not implemented features. TBD decisions must be approved before dependent release. It is not legal, tax or payment-compliance advice.

## 1. Purpose and scope

CheapBazzar is a responsive, multi-vendor, multi-category marketplace. Customers discover and buy products from approved sellers; sellers manage their own listings, stock and fulfilment; admins govern sellers, catalogue, pricing, orders, refunds, support and operations. One checkout may contain several sellers, but seller fulfilment, shipment, return and financial records remain traceable by seller and order item.

MVP: responsive storefront/catalogue; product detail/search/filter/sort; customer account and addresses; cart, server-side pricing, checkout and order history; seller application/approval/dashboard/listings/inventory; admin dashboard/moderation; online payment and eligible COD; parent orders and seller-level fulfilment; delivery estimates/tracking; cancellation/returns/refunds; essential transactional notifications; security, monitoring, backups and critical-flow tests; required policies and disclosures reviewed before launch.

P1 candidates: wishlist, comparison, verified-purchase reviews, general coupon campaign tools and advanced seller reports. Out of scope unless separately approved: native apps, AI recommendations, personalised feeds, loyalty/referrals, international shipping, live chat, seller ad auctions, own delivery fleet, guaranteed GPS without courier data, and microservices/Kubernetes without demonstrated need.

Use familiar marketplace UX conventions inspired by Flipkart without copying its proprietary branding, exact design or assets. Prioritise accurate stock/pricing, transparent charges, least privilege, traceability, accessibility and responsive performance.

## 2. Terms

| Term | Meaning |
|---|---|
| Customer/guest | Buyer / unauthenticated visitor |
| Seller applicant/approved seller | Seller awaiting approval / seller permitted to list |
| Product/listing | Shared catalogue identity / seller-specific offer, price and stock |
| Variant/SKU | Product option / stock-keeping identifier |
| Parent order/suborder | Customer-facing checkout order / seller-specific fulfilment grouping |
| Order item | Purchase-time snapshot of product, seller, quantity and amounts |
| Payment/refund | Collection transaction / return of captured funds |
| Commission/settlement | Platform fee / seller payout after adjustments |
| Shipment/COD | Package tracking record / Cash on Delivery |
| Idempotency | Repeated request handling without duplicate side effects |
| PII/TBD | Personally identifiable information / unresolved decision |
| P0/P1/P2 | MVP essential / strong experience / later growth |

## 3. Roles and permissions

All protected operations MUST enforce permissions server-side; hiding UI controls is not authorisation. Default is deny.

| Role | Allowed | Restricted by default |
|---|---|---|
| Guest | Browse/search public active listings | Private data, protected checkout and dashboards |
| Customer | Own profile, addresses, cart, orders, eligible returns and support | Other customers' data; price/payment/commission changes |
| Seller applicant | Submit/view own application | Publish listings or access seller orders/finance before approval |
| Approved seller | Own listings, stock, suborders, fulfilment and earnings | Other sellers' records; approve self; set commission or fake payment/refund/payout state |
| Moderator | Assigned catalogue moderation | Unrelated finance/account actions |
| Support agent | Minimum data for assigned cases and tickets | Unauthorised refund/payout approval |
| Operations operator | Permitted shipment/courier exceptions | Financial ledger/payment changes |
| Finance operator | Reconciliation and permitted financial actions | Catalogue/privilege changes unless granted |
| Administrator | Seller approval, catalogue, orders and settings within permissions | Unlogged critical actions or raw secrets |
| Super administrator | Admin roles/critical settings with strong authentication and audit | Bypassing audit |
| External provider | Authenticated events within integration contract | General user data/admin operations |

- **AUTHZ-001:** Every protected endpoint authenticates the caller.
- **AUTHZ-002:** Every object read/write/delete verifies ownership or explicit permission.
- **AUTHZ-003:** Sellers only access their own listings, stock, suborders, returns and settlements.
- **AUTHZ-004:** Customers only access their own profile, addresses, cart, orders, payments, refunds and tickets.
- **AUTHZ-005:** Client input cannot set role, seller approval, payment status, commission, refund completion or payout status.
- **AUTHZ-006:** Sellers cannot approve themselves, set platform commission or declare provider payment success.
- **AUTHZ-007:** Privilege changes and sensitive admin actions are audited.
- **AUTHZ-008:** KYC, payout and sensitive customer data are restricted to personnel/providers with a legitimate need.
- **AUTHZ-009:** Denied requests must not leak private object contents.
- **AUTHZ-010:** Tests must prove that changing object IDs cannot bypass ownership checks.

## 4. Functional requirements

IDs are stable and must be referenced by tasks and tests. Requirements supporting purchase, finance, security or legal operations are P0 unless marked P1.

### 4.1 Catalogue and search

- **CAT-001 (P0):** Only active eligible listings from approved active sellers are purchasable.
- **CAT-002 (P0):** Product details show title, multiple images, description, seller, price, stock, variants and relevant delivery/return information.
- **CAT-003 (P0):** Categories and product attributes are configurable.
- **CAT-004 (P0):** Required listing fields, prices, variants and uploaded images are validated server-side.
- **CAT-005 (P0):** Approved sellers may publish eligible listings without per-listing manual approval; validation/moderation remains active.
- **CAT-006 (P0):** Listing states include draft, active, inactive and moderated/rejected with authorised transitions.
- **CAT-007 (P0):** Removed/inactive listings and suspended sellers cannot sell; historical order snapshots remain available.
- **CAT-008 (P0):** Price, stock, discount and review counts come from authoritative records, never fabricated values.
- **CAT-009 (P0):** An unavailable variant cannot be purchased.
- **CAT-010 (P0):** Uploads validate actual content/type, size and safe storage; executable files are never served as images.
- **CAT-011 (P1):** Review counts/ratings derive from persisted eligible reviews.
- **SRCH-001 (P0):** Search supports agreed product fields and excludes inactive/prohibited listings.
- **SRCH-002 (P0):** Results are paginated/bounded; MVP filters include category and price range.
- **SRCH-003 (P0):** MVP sorting includes price ascending/descending and documented default ordering.
- **SRCH-004 (P0):** Malformed ranges, unsupported sorts, oversized queries and invalid page sizes are rejected or safely normalised.
- **SRCH-005 (P0):** Empty results return a valid empty state, not a server error.
- **SRCH-006 (P1):** Additional filters and suggestions may be enabled when data and policy support them.

### 4.2 Identity and addresses

- **ID-001 (P0):** Support registration, sign-in/out and account recovery using the approved auth design.
- **ID-002 (P0):** Verify the chosen identity channel before marking it verified; email/mobile/both is TBD.
- **ID-003 (P0):** Handle duplicate identifiers without disclosing private account existence to unauthenticated callers.
- **ID-004 (P0):** Passwords, if used, are stored only as salted hashes; plaintext passwords are never logged.
- **ID-005 (P0):** Sessions/tokens expire and can be revoked; refresh/logout policy is documented in Architecture.md.
- **ID-006 (P0):** Login, OTP and recovery attempts are rate-limited and monitored.
- **ID-007 (P0):** Customers manage only their own profile and addresses.
- **ID-008 (P0):** Browsing does not require registration; checkout identity policy must be decided before implementation.
- **ID-009 (P0):** Account suspension blocks new protected actions while preserving open-order/dispute/lawful-retention records.
- **ADDR-001 (P0):** Validate required Indian delivery-address fields and pincode under the approved model.
- **ADDR-002 (P0):** Serviceability uses a configured source or documented manual process; unserviceable delivery is not promised.
- **ADDR-003 (P0):** Order address snapshots remain unchanged after saved-address edits/deletion.
- **ADDR-004 (P0):** Address/contact data is disclosed only to the customer and authorised fulfilment parties.

### 4.3 Cart, inventory and checkout

- **CART-001 (P0):** Add/update/remove eligible cart items.
- **CART-002 (P0):** Reject zero, negative, non-integer or policy-exceeding quantities.
- **CART-003 (P0):** Cart retains product/variant/seller identity and indicates unavailable listings.
- **CART-004 (P0):** Checkout revalidates seller/listing, variant, price, stock and quantity.
- **CART-005 (P0):** Client-supplied price, discount, tax, shipping, COD fee, commission and total are never authoritative.
- **CART-006 (P0):** Inventory reservation/decrement is concurrency-safe and prevents overselling.
- **CART-007 (P0):** Reservation expiry/release after failed payment or cancellation follows explicit rules.
- **CART-008 (P0):** Sellable stock cannot be negative unless an approved backorder policy exists.
- **CART-009 (P0):** Changed price/stock is shown and changed total requires customer reconfirmation.
- **CART-010 (P0):** Cart is owner/session isolated; guest-cart merge, if supported, revalidates items and quantities.
- **CART-011 (P0):** Duplicate checkout requests do not create duplicate orders/charges.
- **CHK-001 (P0):** Checkout shows address, seller grouping, quantities, estimate, payment options and full amount breakdown.
- **CHK-002 (P0):** Backend revalidates stock, price, promotion, serviceability and payment eligibility before order creation.
- **CHK-003 (P0):** Mixed-seller checkout creates a parent order and seller-level fulfilment records.
- **CHK-004 (P0):** Order items snapshot product, variant, seller, quantity, price and allocated discount/tax/fee.
- **CHK-005 (P0):** Successful order returns a reference and appears in customer history.
- **CHK-006 (P0):** UI distinguishes order placed, payment pending/success/failure.
- **CHK-007 (P0):** Failed checkout leaves traceable, recoverable order/payment/inventory state.

### 4.4 Pricing and promotions

- **PRICE-001 (P0):** One authoritative server-side service calculates checkout pricing.
- **PRICE-002 (P0):** Show subtotal, discounts, delivery fee, COD fee, applicable tax and final payable amount.
- **PRICE-003 (P0):** Backend recalculates total and customer confirms it before order creation.
- **PRICE-004 (P0):** Money uses exact decimal/minor units and documented rounding; binary floating-point is not authoritative.
- **PRICE-005 (P0):** Initial currency is INR.
- **PRICE-006 (P0):** Eligible online-payment discount is configurable and capped at ₹100 within approved scope.
- **PRICE-007 (P0):** Eligible COD charge is configurable, disclosed and capped at ₹100 within approved scope.
- **PRICE-008 (P0):** Online delivery is intended free; exceptions/serviceability/mixed-seller rules are TBD and release-blocking.
- **PRICE-009 (P0):** COD delivery fee and COD charge are separately disclosed unless approved combined disclosure is clear.
- **PRICE-010 (P0):** Reference prices/discount claims must be valid.
- **PRICE-011 (P0):** Promotions define dates, eligibility, caps, usage, stacking and refund treatment.
- **PRICE-012 (P0):** Expired/future/exhausted/ineligible promotions are rejected server-side.
- **PRICE-013 (P0):** Tax/invoice treatment follows approved finance advice; system does not invent tax rates.
- **PRICE-014 (P0):** Purchase-time prices and discount/tax allocation are snapshotted for refunds/audit.
- **PRICE-015 (P0):** Partial refund and mixed-seller fee allocation follow deterministic approved rules.
- **PRICE-016 (P1):** General coupon campaign builder may follow after core pricing is stable.

Release-blocking pricing decisions: minimum order; discount formula; whether ₹100 cap is per parent order/suborder/item; stacking; mixed-seller shipping; COD eligibility; tax treatment; partial-refund allocation. Unknowns must not be silently hard-coded.

### 4.5 Payments and COD

- **PAY-001 (P0):** Selected provider supports intended marketplace model; provider is TBD.
- **PAY-002 (P0):** Payment initiation uses server-generated amount/order context and unique provider reference.
- **PAY-003 (P0):** Backend verifies payment status/signature using provider's server-side method.
- **PAY-004 (P0):** Webhook authenticity is verified before processing.
- **PAY-005 (P0):** Duplicate/out-of-order webhooks are idempotent and cannot incorrectly regress terminal states.
- **PAY-006 (P0):** Browser redirect/client success alone never marks payment paid.
- **PAY-007 (P0):** Record provider reference, amount/currency, status and timestamps without raw card/CVV.
- **PAY-008 (P0):** Provider amount/currency must match authoritative order before success.
- **PAY-009 (P0):** Pending/failed/cancelled/timeout payments have retry and reconciliation paths.
- **PAY-010 (P0):** Retry cannot blindly create duplicate successful capture.
- **PAY-011 (P0):** Provider outage leaves a recoverable state and clear message.
- **PAY-012 (P0):** Payments reconcile against provider reports.
- **PAY-013 (P0):** Verify marketplace seller KYC, transfers/settlements, refunds and reporting before production.
- **COD-001 (P0 if enabled):** Offer COD only when address/order/product/risk rules pass.
- **COD-002 (P0 if enabled):** Disclose COD and delivery charges before confirmation.
- **COD-003 (P0 if enabled):** Calculate COD fee server-side and distinguish COD from prepaid.
- **COD-004 (P0 if enabled):** Define collection, remittance, refusal and reconciliation states.
- **COD-005 (P0 if enabled):** Do not settle seller solely because courier says delivered; reconcile collection/remittance.
- **COD-006 (P0 if enabled):** Explain unavailable COD without exposing internal fraud signals.

### 4.6 Seller/admin

- **SELL-001 (P0):** Applicant submits required business/contact data and views application status.
- **SELL-002 (P0):** Seller states include pending, more-info-required, approved, rejected and suspended.
- **SELL-003 (P0):** Admin decisions record actor, time, status and reason where appropriate.
- **SELL-004 (P0):** Only approved/active sellers publish eligible listings or receive new sales.
- **SELL-005 (P0):** Sellers manage only their own listings, variants, SKU and stock.
- **SELL-006 (P0):** Sellers see only their own suborders and fulfilment tasks.
- **SELL-007 (P0):** Sellers cannot falsely mark delivery or edit historical purchase prices/ledger.
- **SELL-008 (P0):** Suspension blocks new sales but preserves permitted historical order/return/finance access.
- **SELL-009 (P0):** KYC/bank/payout data is restricted and protected.
- **SELL-010 (P0):** Earnings distinguish gross sales, commission, refunds, adjustments, holds and settled amounts where available.
- **SELL-011 (P1):** Low-stock alerts and performance reports may be added with defined metrics.
- **ADM-001 (P0):** Admin can approve/reject/request information/suspend sellers within permissions.
- **ADM-002 (P0):** Admin can manage categories and moderate listings.
- **ADM-003 (P0):** Admin can investigate order/shipment/payment exceptions.
- **ADM-004 (P0):** Approved configurable pricing rules are managed without source edits.
- **ADM-005 (P0):** Refund approval, payout holds, seller suspension and role changes are gated and audited.
- **ADM-006 (P0):** High-impact actions require confirmation and explain consequences.
- **ADM-007 (P0):** Ordinary admin controls cannot erase audit history.
- **ADM-008 (P0):** Admin views pending applications, moderation, order exceptions and reconciliation issues.
- **ADM-009 (P0):** Admin uses stronger authentication; MFA policy is a release decision and strongly recommended.
- **ADM-010 (P0):** Sensitive exports are access-controlled and logged.

### 4.7 Shipment, cancellation, returns and refunds

- **SHIP-001 (P0):** Mixed-seller orders have independently trackable seller fulfilment records.
- **SHIP-002 (P0):** Initial delivery estimate is 7–10 days adjusted to serviceability and seller/courier data; not a universal guarantee.
- **SHIP-003 (P0):** Courier events are authenticated, deduplicated and stored with source/status/time.
- **SHIP-004 (P0):** Manual status override requires permission, reason, actor and timestamp; it is not GPS proof.
- **SHIP-005 (P0):** Represent failed delivery, refusal, return-to-origin, lost/damaged parcel and courier exceptions.
- **SHIP-006 (P0):** Seller dispatch SLA and escalation owner are defined before launch; exact SLA TBD.
- **SHIP-007 (P0):** Different shipment dates/statuses are shown per shipment.
- **RET-001 (P0):** Cancellation is allowed only in eligible states under published policy.
- **RET-002 (P0):** Eligible return/replacement request records reason and required evidence.
- **RET-003 (P0):** Eligibility is calculated server-side from order state, item/category policy and time window.
- **RET-004 (P0):** Requests record status, actor, reason and timestamps.
- **RET-005 (P0):** Partial return affects only eligible items/quantities.
- **RET-006 (P0):** Stock restoration follows fulfilment/inspection policy.
- **RET-007 (P0):** Refund is initiated only by authorised provider workflow/approved alternative.
- **RET-008 (P0):** Requested, approved, processing, completed and failed are distinct refund states.
- **RET-009 (P0):** Refund is complete only on provider confirmation/reconciliation.
- **RET-010 (P0):** Total refunds cannot exceed refundable captured amount after prior refunds/adjustments.
- **RET-011 (P0):** Refund allocation uses purchase snapshot and approved discount/tax/fee rules.
- **RET-012 (P0):** COD refunds use approved verified method; do not assume a prepaid transaction.
- **RET-013 (P0):** Commission/settlement ledger adjusts consistently after cancellation/return/refund.
- **RET-014 (P0):** Return windows, exclusions, shipping responsibility, inspection and timelines are TBD and must be approved/published before launch.

### 4.8 Finance, support, notifications and audit

- **FIN-001 (P0):** Support an approved commission model; rate/base TBD.
- **FIN-002 (P0):** Record commission base, rule snapshot, amount and rounding.
- **FIN-003 (P0):** Distinguish gross sales, discounts, commission, refunds, adjustments, holds and payouts.
- **FIN-004 (P0):** Settlement states support pending, eligible, processing, settled, failed/on-hold as applicable.
- **FIN-005 (P0):** Settlement completion requires provider/bank evidence or reconciliation.
- **FIN-006 (P0):** Financial corrections use auditable adjustments/reversals, not silent overwrites.
- **FIN-007 (P0):** Reconcile orders, captures, refunds, COD collections, commissions and payouts.
- **FIN-008 (P0):** Settlement follows provider capability, seller agreement and applicable obligations.
- **FIN-009 (P0):** High-value refunds/payout changes should support dual approval; threshold TBD.
- **FIN-010 (P0):** Finance reports/exports are restricted and audited.
- **SUP-001 (P0):** Customer has a support route for order/delivery/payment/return issues.
- **SUP-002 (P0):** Support case has reference, status, timestamps and authorised assignee.
- **SUP-003 (P0):** Support sees only permitted cases/data.
- **SUP-004 (P0):** Relevant order/payment/shipment/cancellation/refund events generate transactional notifications through selected provider where supported.
- **SUP-005 (P0):** Notification failures are logged/retried safely and do not roll back valid orders.
- **SUP-006 (P0):** Notifications contain no passwords, live OTPs, raw card data or unnecessary sensitive data.
- **SUP-007 (P0):** Provider, sender, templates, retries and consent/opt-out rules are TBD.
- **SUP-008 (P0):** Marketing messages are separate from transactional messages and follow consent/opt-out requirements.
- **ENG-001 (P1):** Wishlist is customer-scoped.
- **ENG-002 (P1):** Verified-purchase reviews tie to eligible order items; sellers cannot post on behalf of customers.
- **ENG-003 (P1):** Review range, duplicate, moderation and appeal rules are defined.
- **AUD-001 (P0):** Sensitive changes record actor, action, target, time, outcome and reason where appropriate.
- **AUD-002 (P0):** Audit covers seller approval, moderation, roles, pricing, manual tracking, refunds, payout holds and finance adjustments.
- **AUD-003 (P0):** Logs exclude secrets, passwords, OTPs and raw card data.
- **AUD-004 (P0):** Ordinary users cannot edit/delete audit records.
- **AUD-005 (P0):** Reports are role-restricted and metrics consistently defined.

## 5. Business rules

- **BR-001:** Only approved/active sellers publish eligible products; approved sellers can publish without per-listing approval subject to moderation.
- **BR-002:** Customer/seller data is owner-isolated; all checkout amounts are server-calculated.
- **BR-003:** Inventory cannot oversell except under approved backorder policy; order items preserve purchase-time values.
- **BR-004:** Mixed carts create seller-level fulfilment/shipment/finance records.
- **BR-005:** Online discount is capped at ₹100 under approved eligibility scope; COD charge is disclosed and capped at ₹100 under approved scope.
- **BR-006:** Online delivery is intended free; COD delivery fee applies under approved policy; exceptions/mixed-seller allocation TBD.
- **BR-007:** 7–10 days is an estimate, not a universal guarantee.
- **BR-008:** Payment success/refund completion requires verified provider evidence.
- **BR-009:** COD collection/remittance is separate from prepaid payment and must be reconciled.
- **BR-010:** Commission follows approved seller agreement; rate/base TBD.
- **BR-011:** Charges, estimates, policies and final total are disclosed before confirmation.
- **BR-012:** Reviews, discounts and tracking status cannot be fabricated.
- **BR-013:** Provider capability must be verified and end-to-end tested.
- **BR-014:** Legal/tax/privacy requirements must be reviewed for actual model and launch date.

## 6. Data requirements

| Entity | Required data and constraints |
|---|---|
| User/profile | ID, role/status, login identifiers, verification, permitted profile fields; unique identity, no plaintext password |
| Address | Owner, recipient/contact, address lines, city/state/pincode; validated, order snapshot preserved |
| Seller/verification | Business/user IDs, status, required verification refs, timestamps; restricted access |
| Category/product/variant | IDs, hierarchy, title, attributes, options, SKU/status; valid hierarchy and SKU scope |
| Seller offer/inventory | Seller, product/variant, price, stock/reserved quantities; ownership and non-negative stock |
| Images | Storage key, alt text, order/status; safe type/size/storage |
| Cart/promotion | Owner, items/quantity; rules/dates/caps/usage; revalidation and atomic limits |
| Parent order/suborder/item | Customer/seller, address and purchase snapshots, state, quantity and amounts; valid relationships |
| Payment/refund | Provider refs, amount/currency, status, timestamps/allocation; idempotent, no raw card data |
| Commission/settlement | Seller, rule/base/amount, state, evidence; auditable/reconciled |
| Shipment/tracking | Suborder, courier/ref, events/source/status/time; deduplicated, manual actor/reason |
| Return/support/review | Order item, reason/state, ticket, eligible review; policy/owner checks |
| Notification/audit/integration | Recipient/provider, event ID, state, safe metadata; no secrets, duplicate-safe |

- **DATA-001:** All entities have stable IDs and valid relationships.
- **DATA-002:** Money uses exact decimal/minor units and defined rounding; timestamps use a consistent storage timezone (recommended UTC).
- **DATA-003:** Order/payment/refund/shipment/settlement transitions are traceable.
- **DATA-004:** Sensitive data is protected in transit and at rest.
- **DATA-005:** Retention, correction, deletion/anonymisation and lawful exceptions are documented.
- **DATA-006:** Product removal does not delete order/finance/dispute records.
- **DATA-007:** Migrations are versioned and recovery-tested.
- **DATA-008:** Exports are minimised/access-controlled; test environments do not expose unprotected production PII.
- **DATA-009:** Backups are protected and restoration tested.
- **DATA-010:** Financial corrections use auditable adjustments, not silent overwrites.

## 7. Validation and state transitions

Server-side validation is mandatory. Validate auth fields/expiry/attempts; seller details/documents/status; product required fields/prices/images/variants; SKU uniqueness; bounded search/filter/sort; address and serviceability; positive integer quantity; promotion dates/caps/eligibility; checkout price/stock/payment eligibility; provider signature/amount/currency; shipment transitions; return windows/refundable balance; admin permissions; file content/size; and API schemas/rate limits. Exact numeric limits (OTP TTL, upload/page/quantity limits and rate limits) must be centrally defined before implementation.

Permitted state flows must be enforced:
- Seller: pending → more-info-required → pending → approved/rejected; suspension/reinstatement require authorised audit.
- Listing: draft → active → inactive; moderation may reject/remove; restoration requires authorised policy.
- Payment: initiated → pending → succeeded/failed/cancelled; stale duplicate events cannot reverse terminal state.
- Fulfilment: placed → confirmed → processing → packed → shipped → in-transit → out-for-delivery → delivered; exceptions include cancelled, failed delivery, return-to-origin and courier exception.
- Refund: requested → review → approved → processing → completed; rejected/failed includes reason and recovery.
- Settlement: pending → eligible → processing → settled, with on-hold/failed paths.

Payment and fulfilment state are separate; parent order may be partially fulfilled while suborders have different statuses.

## 8. Error handling

Use consistent API errors: machine code, safe message, field errors where appropriate and request/correlation ID. Never return stack traces, SQL, tokens, secrets or private records. Suggested HTTP conventions: 400 malformed request; 401 unauthenticated; 403 not permitted; 404 absent/concealed; 409 state conflict; 422 business validation; 429 rate limit; 500 internal; 502/503/504 dependency/service/timeout.

- **ERR-001:** Invalid input causes no partial business mutation.
- **ERR-002:** Auth/permission errors do not leak account/resource details.
- **ERR-003:** Out-of-stock checkout gives clear error and no duplicate order/charge.
- **ERR-004:** Changed total requires renewed confirmation.
- **ERR-005:** Payment timeout remains pending/unknown until reconciled.
- **ERR-006:** Duplicate webhooks/retries are idempotent.
- **ERR-007:** Courier outage shows last known status/time and alerts/retries.
- **ERR-008:** Notification failure does not roll back a valid order.
- **ERR-009:** Critical database/integration failure produces safe error and alert.
- **ERR-010:** Invalid transitions are rejected/logged.
- **ERR-011:** Failed refund/settlement is not falsely completed.
- **ERR-012:** Reconciliation detects missing webhooks/provider mismatches.
- **ERR-013:** Logs use correlation IDs and redact sensitive data.

## 9. Authentication, security and privacy

- **AUTH-001:** Protected operations require valid authentication.
- **AUTH-002:** Define session/token expiry, refresh, revocation, logout and recovery.
- **AUTH-003:** Cookies, if used, use Secure, HttpOnly and appropriate SameSite settings.
- **AUTH-004:** Tokens never appear in URLs/logs; CSRF protection is applied where required.
- **AUTH-005:** Reset/OTP tokens are time-limited, attempt-limited and single-use where appropriate.
- **AUTH-006:** Secrets stay outside source control and have rotation procedures.
- **AUTH-007:** Production uses HTTPS/TLS; admin MFA is strongly recommended and policy TBD.
- **SEC-001:** Default deny; enforce object/function permissions and writable-field allowlists.
- **SEC-002:** Validate/normalise input, use safe database access and output encoding.
- **SEC-003:** Rate-limit login, OTP, search, checkout, coupon, review and upload abuse.
- **SEC-004:** Verify webhook signatures/replay controls; protect provider credentials.
- **SEC-005:** Never store raw card/CVV; minimise sensitive data and protect it at rest/in transit.
- **SEC-006:** Redact passwords, tokens, OTPs, documents and unnecessary PII from logs.
- **SEC-007:** Validate uploads; check dependencies and production configuration before release.
- **SEC-008:** Audit sensitive actions and require extra approval where configured.
- **SEC-009:** Define incident response, access revocation, secret rotation, retention and deletion/correction processes.
- **SEC-010:** Security tests cover ownership/function access, injection, resource exhaustion, uploads and webhook replay.
- **SEC-011:** No critical/high security issue at launch without documented authorised risk acceptance.

## 10. Non-functional requirements

- **NFR-PERF-001:** Target good Core Web Vitals at the 75th percentile when field data is sufficient: LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1.
- **NFR-PERF-002:** Approve API p95 targets for catalogue/search/product/cart/checkout after hosting/load assumptions are set.
- **NFR-PERF-003:** Bound/paginate lists; index based on query plans; optimise image dimensions/compression.
- **NFR-PERF-004:** Load-test checkout, inventory and payment under concurrency/retry.
- **NFR-PERF-005:** Measure provider latency separately; cache is not authoritative for checkout price/stock/payment/order state.
- **NFR-REL-001:** Monitor application, database and critical integrations; alert on payment/refund/reconciliation failures.
- **NFR-REL-002:** External calls have bounded timeouts/retries and idempotency.
- **NFR-REL-003:** Define backup frequency, RPO/RTO; test restore before launch and document rollback/forward-fix.
- **NFR-UX-001:** Core journeys work on supported mobile/tablet/desktop without horizontal overflow.
- **NFR-UX-002:** Important components have loading/empty/error/success/disabled states.
- **NFR-UX-003:** Forms have labels, specific validation and keyboard access.
- **NFR-UX-004:** Target WCAG 2.2 AA for applicable flows; do not rely on colour alone; respect reduced motion where practical.
- **NFR-MAINT-001:** Document modules/API contracts; automate tests for pricing, permissions and state transitions; version/review migrations.
- **NFR-OBS-001:** Requests/events use correlation IDs; logs are structured and redacted.
- **NFR-OBS-002:** Monitor latency, errors, payment mismatches, failed webhooks, stuck refunds and shipment-sync failures.

## 11. External integrations

- **Payment provider:** Verify supported methods, marketplace seller onboarding/KYC, transfers/settlements, refunds, signed webhooks, reconciliation reports and sandbox/production support. Provider TBD; an account alone does not prove capability.
- **Courier:** Evaluate serviceability, rates, pickup, labels/AWB, tracking, returns, COD remittance and exceptions. If manual initially, document limits and audit process.
- **Email/SMS:** Select provider, verify sender, define templates/retries/consent and opt-out. Notification failure must not block order consistency.
- **Storage/CDN/monitoring:** Define access control, safe image delivery, retention, backups and incident ownership; provider TBD.

## 12. Edge cases

| ID | Scenario | Expected behaviour |
|---|---|---|
| EDGE-001 | Item out of stock in cart | Revalidate, inform customer, no oversell |
| EDGE-002 | Price changes | Recalculate and request confirmation |
| EDGE-003 | Seller suspended | Block new sale; preserve historical records |
| EDGE-004 | Multi-seller cart | Parent order plus seller fulfilment records |
| EDGE-005 | One suborder delayed | Track suborders independently |
| EDGE-006 | Payment timeout | Pending/unknown; reconcile, no blind recapture |
| EDGE-007 | Browser success, provider pending | Do not mark paid |
| EDGE-008 | Duplicate webhook | Idempotent processing |
| EDGE-009 | Payment succeeds/order write fails | Recoverable reconciliation preserves provider reference |
| EDGE-010 | Repeated order submit | No duplicate order/charge |
| EDGE-011 | Coupon exhausted concurrently | Atomic usage limit |
| EDGE-012 | Concurrent last-unit purchase | No oversell |
| EDGE-013 | Out-of-order courier event | Validate, log and reconcile |
| EDGE-014 | Courier unavailable | Show last known status/time; retry/alert |
| EDGE-015 | Manual tracking correction | Permission/reason/actor/time audit |
| EDGE-016 | Partial return | Only eligible items; approved allocation |
| EDGE-017 | Refund provider fails | Retryable/exception, not completed |
| EDGE-018 | COD collection unreconciled | Do not settle seller |
| EDGE-019 | Address edited after order | Preserve order snapshot |
| EDGE-020 | Seller accesses another seller ID | Deny without data leakage |
| EDGE-021 | Client submits role/payment fields | Reject/ignore privileged fields |
| EDGE-022 | Unsafe disguised upload | Validate content and reject |
| EDGE-023 | Notification failure | Order valid; log/retry |
| EDGE-024 | Session expires during checkout | Re-authenticate/revalidate |
| EDGE-025 | Product removed after purchase | Preserve snapshot and apply policy |
| EDGE-026 | Duplicate refund | Deduplicate; prevent over-refund |
| EDGE-027 | Duplicate capture anomaly | Detect/reconcile/escalate |
| EDGE-028 | Pincode unserviceable | Do not promise/allow ineligible checkout |
| EDGE-029 | Discount rounding boundary | Deterministic rounding/cap |
| EDGE-030 | Commission changes after order | Preserve applicable rule snapshot |
| EDGE-031 | Seller suspended with open orders | Preserve customer visibility; operations handles exception |
| EDGE-032 | Deployment/migration failure | Tested recovery and alert |
| EDGE-033 | No search matches | Valid empty state |
| EDGE-034 | Review lacks eligible purchase | Do not mark verified |
| EDGE-035 | Invalid webhook | Reject and log safe metadata |
| EDGE-036 | Account deletion with open orders | Apply retention policy without breaking required records |

## 13. Acceptance criteria

Acceptance requires repeatable test evidence; screenshots alone do not prove financial, inventory or security behaviour.

- **AC-001:** Active listing search/detail shows current product and availability.
- **AC-002:** Adding a valid variant/quantity updates cart correctly.
- **AC-003:** Stock/price changes are detected and confirmed before checkout continues.
- **AC-004:** Checkout displays all applicable charges and final amount before confirmation.
- **AC-005:** Invalid/unserviceable address prevents order confirmation with recovery message.
- **AC-006:** Successful order has a reference and appears in customer history.
- **AC-007:** Mixed-seller checkout creates parent and seller fulfilment records.
- **AC-008:** Customer cannot retrieve another customer's order by changing ID.
- **AC-009:** Seller approval records status/actor/time; unapproved seller cannot publish.
- **AC-010:** Approved seller can publish a valid listing without per-listing manual approval.
- **AC-011:** Seller A cannot access Seller B's listing/order/settlement.
- **AC-012:** Moderated listing is not purchasable and action is audited.
- **AC-013:** Unauthorised support role cannot approve refund.
- **AC-014:** Sensitive admin change records actor, target, time and required reason.
- **AC-015:** Online discount obeys approved rule and ₹100 cap.
- **AC-016:** COD fee/delivery fee are disclosed and cap enforced.
- **AC-017:** Expired/ineligible promotion is rejected server-side.
- **AC-018:** Concurrent last-unit checkout does not oversell.
- **AC-019:** Client redirect without verified provider success does not mark paid.
- **AC-020:** Duplicate webhook creates one intended financial/state effect.
- **AC-021:** Payment timeout is traceable and does not trigger blind recapture.
- **AC-022:** Refund exceeding balance is rejected; processing refund is not shown completed.
- **AC-023:** COD without reconciled collection does not mark seller settled.
- **AC-024:** Courier event updates shipment history with source/time.
- **AC-025:** Manual shipment update records operator/reason/time.
- **AC-026:** Duplicate/out-of-order courier events do not corrupt status.
- **AC-027:** Different shipments show their own status/date.
- **AC-028:** Ineligible return is rejected or routed to authorised exception.
- **AC-029:** Partial return affects only eligible items and uses approved allocation.
- **AC-030:** Notification failure is logged/retried without rolling back order.
- **AC-031:** Support agent sees only permitted case data.
- **AC-032:** Automated tests prove customer/seller ownership isolation.
- **AC-033:** Malformed input/oversized payload/unsupported enums are safely rejected.
- **AC-034:** Logs contain no passwords/tokens/OTP/secrets/raw card credentials.
- **AC-035:** Invalid payment/courier webhook signature is rejected.
- **AC-036:** Critical checkout/stock/payment/refund tests pass concurrency/retry cases.
- **AC-037:** Core journeys have no horizontal overflow on supported viewports.
- **AC-038:** Accessibility review covers labels, keyboard, focus and contrast.
- **AC-039:** Core Web Vitals are measured against targets when field data is sufficient.
- **AC-040:** Backup restoration is demonstrated; RPO/RTO documented.
- **AC-041:** Critical monitoring alerts are tested.
- **AC-042:** Required policies/privacy/grievance details receive launch-date review.
- **AC-043:** No critical/high security issue remains without authorised written risk acceptance.

## 14. Traceability

| Workstream | Requirement groups | Acceptance evidence |
|---|---|---|
| Catalogue/search | CAT, SRCH | AC-001, AC-012, AC-033 |
| Identity/permissions | ID, ADDR, AUTH, AUTHZ | AC-008, AC-032, AC-034 |
| Cart/inventory | CART, CHK, DATA | AC-002, AC-003, AC-018, AC-036 |
| Pricing | PRICE, BR | AC-004, AC-015–AC-017 |
| Payments/COD | PAY, COD, FIN | AC-019–AC-023 |
| Seller/admin | SELL, ADM | AC-009–AC-014 |
| Delivery | SHIP | AC-007, AC-024–AC-027 |
| Returns/refunds | RET, FIN | AC-022, AC-028–AC-029 |
| Support | SUP | AC-030–AC-031 |
| Security/operations | SEC, NFR, AUD | AC-032–AC-043 |

## 15. Assumptions and release-blocking decisions

Assumptions: India/INR; multi-seller marketplace; seller approval required; approved sellers publish eligible listings without per-listing manual approval; mixed-seller carts; platform coordinates delivery while packing/dispatch boundaries need confirmation; online discount up to ₹100; COD charge up to ₹100 plus applicable delivery fee; online delivery intended free; 7–10 days is an estimate; providers, launch categories/geography/volume/budget are not final.

Decisions required before implementation: launch/restricted categories and geography; checkout identity policy; exact discount formula/minimum order/cap scope; COD eligibility/limits/refusal policy; free-delivery exceptions/mixed-seller fees; return windows/shipping/refund timelines; commission base/rate and payout schedule; payment provider marketplace capability; courier/manual fallback and dispatch SLA; tax/invoice/rounding; session/MFA/rate/upload limits; retention/deletion/backup/incident plan; hosting/load/API targets; notification/consent rules; legal review for actual model and launch date. Unknowns must not be silently hard-coded.

## 16. Legal and research references

This SRS is not legal/tax advice. Before launch, qualified advisers and providers must review the actual entity, marketplace model, seller relationship, payment flow and launch date. Review applicable Consumer Protection (E-Commerce) Rules/amendments, disclosures, grievance handling, discount claims, GST/invoicing/tax duties, payment/KYC/settlement requirements, privacy, restricted products, product safety and IP complaints.

A Government of India announcement dated 10 September 2026 describes 2026 e-commerce rule amendments intended to commence on 1 January 2027. Verify final notified text and applicability for the actual launch date.

1. OWASP API Security Top 10: https://owasp.org/www-project-api-security/
2. OWASP API risk list: https://api-security.owasp.org/editions/2023/en/0x11-t10/
3. Government of India, Consumer Protection Acts and Rules: https://consumeraffairs.gov.in/pages/consumer-protection-acts
4. PIB e-commerce rules amendment announcement: https://www.pib.gov.in/newsite/erelcontent.aspx?lang=2&reg=48&relid=294532
5. Google web.dev Core Web Vitals: https://web.dev/articles/vitals
6. Google web.dev thresholds: https://web.dev/articles/defining-core-web-vitals-thresholds

## 17. Companion documents and approval

PRD.md defines product goals/scope; this SRS specifies testable behaviour. UI-UX.md must cover P0 states including mixed-seller checkout, fees, payment pending/failure, seller approval, shipment tracking and returns/refunds. Architecture.md must resolve data model, transaction boundaries, inventory concurrency, payment verification, finance ledger, integrations, access control and recovery. Development-Plan.md must include dependency-aware testing/release gates for payments, COD, mixed-seller fulfilment, refunds, security and operations.

If documents conflict, record the discrepancy and obtain an explicit product decision.

| Reviewer | Responsibility | Status |
|---|---|---|
| Product owner | Scope, business rules, TBDs | Pending |
| Engineering/architecture | Feasibility, data/API/security/NFRs | Pending |
| Operations/finance | Fulfilment, COD, returns, commission/settlement | Pending |
| Legal/compliance adviser | Obligations for model/launch date | Pending |
| QA/test owner | Tests and traceability evidence | Pending |

**Approval rule:** This SRS becomes the implementation baseline only after the product owner and relevant reviewers approve release-blocking policies. Until then it remains a planning draft.
