# Product Requirements Document (PRD)

## Project
- **Name:** CheapBazzar
- **Description:** CheapBazzar is an online marketplace for budget-conscious shoppers to discover affordable products from verified sellers, compare options quickly, and buy with confidence.

## Problem Statement
Budget-conscious buyers struggle to find trustworthy low-cost products in one place. Existing marketplaces often prioritize premium listings, hide total cost until checkout, and provide weak trust signals for low-price sellers.

## Target Users
1. **Budget Shoppers** (students, families, first-job earners)
2. **Deal Hunters** (users frequently comparing offers and discounts)
3. **Small Sellers** (local or independent sellers offering low-cost goods)

## Product Goals
1. Help users find affordable products quickly.
2. Increase trust in low-cost purchases through seller verification and transparent pricing.
3. Enable smooth end-to-end purchase flow for MVP users.

## Core Features
1. User registration/login
2. Product catalog with search, category filters, and price sorting
3. Product detail page with images, total price visibility, and seller info
4. Cart and checkout flow
5. Basic seller onboarding and product listing management
6. Order history and order status tracking

## MVP Scope
### In Scope
- Email/password authentication
- Buyer and seller roles
- Product listing CRUD for sellers
- Product browse/search/filter for buyers
- Cart, address capture, and order placement (cash-on-delivery or simulated payment)
- Basic order tracking states: Placed, Confirmed, Shipped, Delivered, Cancelled

### Out of Scope (MVP)
- Live chat between buyer and seller
- Recommendation engine
- Dynamic pricing/auction
- Multi-language/localization
- Loyalty points and referral programs
- Native mobile apps (web responsive only)

## User Stories
- As a buyer, I want to search products by keyword so I can quickly find what I need.
- As a buyer, I want to filter by price range so I can stay within budget.
- As a buyer, I want clear seller and delivery details before checkout so I can buy confidently.
- As a seller, I want to add and edit product listings so I can manage my store.
- As a buyer, I want to view my order history so I can track past purchases.

## Success Metrics (MVP)
1. Product search-to-detail click-through rate >= 25%
2. Cart-to-order conversion rate >= 15%
3. Order completion rate (non-cancelled) >= 80%
4. Seller listing activation within 24 hours for >= 70% of onboarded sellers
5. First purchase completion within 10 minutes median from first product view

## Assumptions
- Users have reliable internet and email access.
- Sellers can provide valid business/identity information.
- Initial traffic is moderate and can be handled by a single-region deployment.

## Risks
- Low-quality listings may reduce buyer trust.
- Incomplete seller verification may increase fraudulent activity.
- Checkout drop-offs due to hidden delivery cost or long forms.

## Acceptance Criteria
1. Buyers can sign up/login and browse products by category and keyword.
2. Product details show price, delivery estimate, and seller details before checkout.
3. Buyers can add items to cart and place orders with valid shipping data.
4. Sellers can create, update, and deactivate product listings.
5. Orders move through defined statuses and are visible in buyer order history.
