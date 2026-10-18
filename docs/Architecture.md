# System Architecture Document

## Project
- **Name:** CheapBazzar
- **Description:** CheapBazzar is an online marketplace for budget-conscious shoppers to discover affordable products from verified sellers, compare options quickly, and buy with confidence.

## 1. Architecture Goals
- Practical MVP architecture with fast implementation.
- Clear separation of frontend, backend, and data layer.
- Secure authentication and role-based access.
- Simple scaling path as traffic grows.

## 2. Recommended Tech Stack
- **Frontend:** React + TypeScript + Vite
- **Backend:** Node.js + Express (TypeScript)
- **Database:** PostgreSQL
- **Caching (optional MVP+):** Redis for session/cache
- **Auth:** JWT (short-lived access token) + refresh token (HTTP-only cookie)
- **Deployment:** Frontend on Vercel/Netlify, Backend on Render/Fly/EC2, DB on managed PostgreSQL
- **Monitoring:** Structured logs + uptime checks + basic metrics dashboard

## 3. High-Level Components
1. **Web Client**
   - Catalog browsing, account, checkout, seller dashboard
2. **API Server**
   - Auth, catalog, cart, orders, seller listing services
3. **Database**
   - Stores users, products, carts, orders, and audit logs
4. **File/Image Storage**
   - Product images via object storage or trusted image URLs

## 4. Backend Modules
- Auth Module
- User Profile Module
- Product Catalog Module
- Cart Module
- Checkout & Order Module
- Seller Management Module
- Admin Moderation Module

## 5. API Design (REST)
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/products`
- `GET /api/products/:id`
- `POST /api/cart/items`
- `PATCH /api/cart/items/:id`
- `DELETE /api/cart/items/:id`
- `POST /api/orders`
- `GET /api/orders`
- `PATCH /api/orders/:id/status` (seller/admin)
- `POST /api/seller/products`
- `PATCH /api/seller/products/:id`

## 6. Data Flow (Order Placement)
1. Buyer authenticates and adds products to cart.
2. Client calls checkout endpoint with shipping data.
3. Backend validates stock and ownership constraints.
4. Backend creates order + order items in a transaction.
5. Stock is decremented atomically.
6. API returns order confirmation and status = Placed.

## 7. Storage Strategy
- Relational schema with indexes on:
  - `products(name, category, is_active)`
  - `orders(buyer_id, created_at)`
  - `order_items(seller_id, order_id)`
- Keep immutable order snapshot fields (`unit_price`, `product_name`) in order items to preserve history.

## 8. Security Controls
- HTTPS everywhere
- Password hashing with bcrypt/argon2
- RBAC middleware for role checks
- Input schema validation (e.g., Zod/Joi)
- Rate limiting and brute-force protection on auth routes
- Audit logging for seller/admin sensitive actions

## 9. Deployment
- CI pipeline: lint, test, build
- Staging environment mirrors production config
- Environment variables for secrets via provider secret manager
- Zero-downtime deploy preferred via rolling updates

## 10. Monitoring and Observability
- Request/response logs with correlation ID
- Error monitoring with alerts for elevated 5xx rates
- DB performance monitoring (slow queries, connection usage)

## 11. Scalability Plan
- Phase 1 (MVP): Single API instance + managed DB
- Phase 2: Horizontal API scaling behind load balancer
- Phase 3: Add Redis cache for hot catalog queries and session/token controls

## 12. Non-Goals (MVP)
- Microservices split
- Event-driven architecture
- Real-time recommendation infrastructure
