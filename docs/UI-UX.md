# UI/UX Document

## Project
- **Name:** CheapBazzar
- **Description:** CheapBazzar is an online marketplace for budget-conscious shoppers to discover affordable products from verified sellers, compare options quickly, and buy with confidence.

## 1. Design Principles
1. **Clarity first:** Price and delivery info must be obvious.
2. **Speed:** Keep actions and flows short.
3. **Trust:** Surface seller credibility and order transparency.
4. **Accessibility:** Keyboard-friendly and readable interfaces.

## 2. Primary User Journeys
1. Browse catalog -> filter/search -> open product -> add to cart -> checkout -> track order.
2. Register as seller -> create listing -> manage inventory -> update order status.

## 3. Navigation Structure
- Top Nav: Logo, Search, Categories, Cart, Account
- Buyer Account Menu: Profile, Orders, Addresses, Logout
- Seller Dashboard Menu: Listings, Orders, Profile
- Footer: Help, Policies, Contact

## 4. Screens (MVP)
1. Home/Catalog
2. Search Results
3. Product Detail
4. Cart
5. Checkout
6. Login/Register
7. Buyer Order History
8. Seller Listing Management
9. Seller Order Queue

## 5. User Flows
### Buyer Purchase Flow
Catalog -> Product Detail -> Add to Cart -> Cart -> Checkout Form -> Order Confirmation

### Seller Listing Flow
Seller Dashboard -> Add Product Form -> Save Listing -> Listing Table -> Edit/Deactivate

## 6. Components
- Product Card (image, title, price, badge, CTA)
- Filter Panel (category, min-max price, sort)
- Search Bar
- Cart Item Row
- Checkout Summary Card
- Status Chip (Placed/Confirmed/Shipped/Delivered/Cancelled)
- Form Fields with inline validation

## 7. Interaction Guidelines
- Primary CTA buttons: high contrast, consistent placement.
- Form validation: inline + on submit.
- Disable CTA while submission is in progress.
- Confirm destructive actions (remove cart item, deactivate listing).

## 8. Form Requirements
- Registration: name, email, password, role
- Product Create/Edit: name, category, price, stock, image URL, description
- Checkout: full name, phone, address line, city, postal code

## 9. States
### Loading
- Skeleton cards for product grid
- Spinner on checkout/place order action

### Empty
- No search results: show reset filters CTA
- Empty cart: show "Start shopping" CTA
- Seller no listings: show "Add first product" CTA

### Error
- Inline form errors for invalid fields
- Toast/banner for API errors with retry where relevant

## 10. Responsive Behavior
- Mobile-first layout
- Grid: 1 column mobile, 2 tablet, 4 desktop
- Sticky bottom checkout CTA on mobile cart page
- Collapsible filters on small screens

## 11. Accessibility
- Minimum text contrast 4.5:1
- Keyboard navigation for all interactive elements
- Proper labels for all form controls
- ARIA live region for async success/error feedback

## 12. Visual Style Guide
- **Typography:** Inter or system sans-serif
  - H1: 32px/700
  - H2: 24px/600
  - Body: 16px/400
  - Caption: 14px/400
- **Color Tokens:**
  - Primary: #0D6EFD
  - Success: #198754
  - Warning: #FFC107
  - Danger: #DC3545
  - Text Primary: #212529
  - Surface: #FFFFFF
  - Background: #F8F9FA
- **Spacing Scale:** 4, 8, 12, 16, 24, 32
- **Radius:** 8px standard cards/buttons

## 13. Implementation Readiness Checklist
- All screens mapped to components
- All forms define fields + validation behavior
- Loading, empty, and error states defined per screen
- Responsive and accessibility rules documented for handoff
