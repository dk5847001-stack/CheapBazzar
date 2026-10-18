# Software Requirements Specification (SRS)

## Project
- **Name:** CheapBazzar
- **Description:** CheapBazzar is an online marketplace for budget-conscious shoppers to discover affordable products from verified sellers, compare options quickly, and buy with confidence.

## 1. Scope
This document defines testable system requirements for the CheapBazzar MVP web application.

## 2. User Roles and Permissions
1. **Guest**
   - View product catalog and product detail pages.
   - Cannot place orders or manage listings.
2. **Buyer**
   - Manage account/profile.
   - Add/remove cart items, checkout, place orders, and view order history.
3. **Seller**
   - Manage account/profile.
   - Create/read/update/deactivate own product listings.
   - View incoming orders for own products.
4. **Admin (MVP basic)**
   - View users, products, orders.
   - Suspend malicious listings/users.

## 3. Functional Requirements
### 3.1 Authentication and Account
- FR-001: System shall allow account registration with email and password.
- FR-002: System shall require unique email for each account.
- FR-003: System shall allow secure login/logout.
- FR-004: System shall allow role assignment (buyer/seller) at registration.

### 3.2 Product Catalog
- FR-005: System shall list active products with name, image, price, and seller rating badge.
- FR-006: System shall support keyword search on product name.
- FR-007: System shall support category and price-range filtering.
- FR-008: System shall support sorting by price ascending/descending.

### 3.3 Product Detail
- FR-009: System shall display product details, stock, seller info, and delivery estimate.
- FR-010: System shall prevent purchase when stock is zero.

### 3.4 Cart and Checkout
- FR-011: System shall allow buyers to add/remove/update cart item quantity.
- FR-012: System shall validate quantity <= available stock.
- FR-013: System shall capture shipping address and contact number during checkout.
- FR-014: System shall display item subtotal, delivery fee, and total before order confirmation.
- FR-015: System shall create order record with status "Placed" after successful checkout.

### 3.5 Order Management
- FR-016: System shall show buyers order history and status timeline.
- FR-017: System shall allow sellers to update order status (Confirmed, Shipped, Delivered, Cancelled).
- FR-018: System shall audit order status transitions with timestamp and actor.

### 3.6 Seller Listings
- FR-019: System shall allow sellers to create listing with required fields (name, description, price, stock, category, image URL).
- FR-020: System shall allow sellers to edit or deactivate only their own listings.
- FR-021: System shall block inactive listings from buyer search results.

## 4. Business Rules
- BR-001: Product price must be greater than 0.
- BR-002: Stock must be integer >= 0.
- BR-003: Buyers cannot order their own listed products.
- BR-004: Order status cannot skip from Placed directly to Delivered.
- BR-005: Only seller of an order item or admin can update that order's fulfillment status.

## 5. Data Requirements
### Core Entities
- User(id, name, email, password_hash, role, status, created_at)
- Product(id, seller_id, name, description, category, price, stock, is_active, created_at, updated_at)
- CartItem(id, buyer_id, product_id, quantity, updated_at)
- Order(id, buyer_id, total_amount, delivery_fee, shipping_address, contact_number, status, created_at)
- OrderItem(id, order_id, product_id, seller_id, unit_price, quantity)
- OrderStatusLog(id, order_id, from_status, to_status, changed_by, changed_at)

## 6. Validations
- VR-001: Email must follow standard email format.
- VR-002: Password minimum length must be 8 characters.
- VR-003: Contact number must be numeric and 10–15 digits.
- VR-004: Category must be from configured category list.
- VR-005: Quantity updates must fail with clear error when exceeding stock.

## 7. Authentication and Authorization
- AR-001: Passwords shall be stored only as salted hashes.
- AR-002: All protected endpoints shall require valid authenticated session/token.
- AR-003: Role-based access checks shall be enforced server-side for seller/admin actions.

## 8. Error Handling
- EH-001: API errors shall return structured JSON with code, message, and request ID.
- EH-002: Validation failures shall return field-level messages.
- EH-003: Unauthorized access shall return 401; forbidden actions shall return 403.
- EH-004: Missing resources shall return 404.

## 9. Edge Cases
- EC-001: Simultaneous checkout attempts for limited stock must prevent overselling.
- EC-002: Deactivated product in cart must be blocked at checkout with user prompt to remove.
- EC-003: Status update attempts in invalid sequence must be rejected.

## 10. Security Requirements
- SR-001: Use HTTPS for all traffic.
- SR-002: Protect auth endpoints against brute-force with rate limiting.
- SR-003: Sanitize/validate all user inputs to prevent injection and XSS.
- SR-004: Prevent IDOR by checking ownership on resource operations.

## 11. Performance Requirements
- PR-001: Catalog page response p95 <= 800ms under 200 concurrent users.
- PR-002: Checkout transaction completion p95 <= 2s excluding payment gateway latency.
- PR-003: Search results should return first page within 1s for up to 50k active products.

## 12. Acceptance Criteria
1. All functional requirements FR-001 to FR-021 pass integration and role-based authorization tests.
2. Business rules BR-001 to BR-005 are enforced and test-covered.
3. Security checks for authentication/authorization and basic input validation pass.
4. API error responses follow EH-001 format across covered endpoints.
