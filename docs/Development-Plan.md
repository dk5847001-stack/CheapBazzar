# Development Plan

## Project
- **Name:** CheapBazzar
- **Description:** CheapBazzar is an online marketplace for budget-conscious shoppers to discover affordable products from verified sellers, compare options quickly, and buy with confidence.

## 1. MVP Scope Alignment
Deliver only:
- Auth + role onboarding
- Product browse/search/filter
- Seller listing management
- Cart + checkout + order tracking
- Basic admin moderation

## 2. Roadmap Phases
### Phase 0: Project Setup (Priority: P0)
- Initialize frontend and backend projects
- Configure linting, formatting, testing, env management
- Set up CI pipeline (lint/test/build)

### Phase 1: Core Backend + Database (P0)
- Design relational schema and migrations
- Implement auth endpoints and RBAC middleware
- Build product catalog and seller listing APIs
- Build cart and order APIs with transactional stock handling

### Phase 2: Frontend MVP Flows (P0)
- Build catalog/search/filter UI
- Build product detail, cart, checkout screens
- Build login/register and account pages
- Build seller dashboard (listings + order queue)

### Phase 3: Integrations and Hardening (P1)
- Add image upload/storage strategy
- Add rate limiting and security middleware
- Add structured logging and error boundaries

### Phase 4: Testing + Bug Fixing (P0)
- Unit tests for critical services and validators
- API integration tests for auth, cart, checkout, order transitions
- Frontend component/flow tests for checkout and listing creation
- Fix discovered defects and regressions

### Phase 5: Deployment and Release (P0)
- Provision production DB and app hosting
- Configure secrets and environment variables
- Run staging verification checklist
- Deploy MVP and monitor post-release metrics

## 3. Dependencies
- Auth must be complete before protected seller/order flows.
- Product APIs required before catalog frontend integration.
- Cart/order transaction logic required before checkout UI completion.
- Monitoring setup required before production launch.

## 4. Milestones
1. **M1:** Auth + RBAC + DB schema complete
2. **M2:** Product catalog + seller listing management complete
3. **M3:** Cart, checkout, and order tracking complete
4. **M4:** Test pass + security checks + deployment ready

## 5. Prioritization
- **P0 Must-have:** all MVP user stories and acceptance criteria
- **P1 Should-have:** moderation polish, logging improvements
- **P2 Later:** recommendations, chat, loyalty programs

## 6. Definition of Done (DoD)
A backlog item is done when:
1. Acceptance criteria pass.
2. Automated tests added/updated and passing.
3. No high-severity security findings remain.
4. Code reviewed and merged with CI green.
5. User-visible behavior documented if changed.

## 7. Delivery Checklist
- [ ] Backend APIs implemented and documented
- [ ] Database migrations applied and verified
- [ ] Frontend flows complete for buyer and seller MVP journeys
- [ ] Targeted tests passing
- [ ] Security and performance checks completed
- [ ] Deployment runbook executed successfully
