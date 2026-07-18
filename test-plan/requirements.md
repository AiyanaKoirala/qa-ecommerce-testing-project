# Requirement Analysis Document
## Project: QA E-Commerce Testing Project
**Application Under Test (AUT):** SauceDemo (https://www.saucedemo.com)
**API Under Test:** Fake Store API (https://fakestoreapi.com)
**Prepared by:** Aiyana Koirala
**Version:** 2.0
**Date:** July 2026

---

## 1. Purpose
This document defines the functional and non-functional requirements of the e-commerce application and its backend API. It is the base reference for writing test cases, designing API tests, and building automation scripts. Every requirement below is traceable to a Test Case ID in `/manual-testing/` and, where applicable, to a Postman/Selenium test.

## 2. Scope
In scope: Login/Authentication, Session/Logout, Product Catalog, Search & Navigation, Cart Management, Checkout Flow, Payment (mock), Order Confirmation, and backend Product/Cart/User APIs.
Out of scope: Real payment gateway integration, multi-language support, native mobile app testing (see Section 7 — Assumptions).

## 3. Requirement Format
- **Req ID** – unique identifier (FR = Functional, NFR = Non-Functional)
- **Requirement** – what the system must do
- **Business Need (Why)** – the reason this feature exists / the problem it solves
- **Acceptance Criteria** – Given/When/Then, the measurable condition for "this works"
- **Priority** – High / Medium / Low

---

## 4. Functional Requirements — Web Application (SauceDemo)

### 4.1 Authentication (FR-01 to FR-06)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-01 | System shall allow a registered user to log in using a valid username and password. | Protects user accounts and personalizes the session; prevents unauthorized cart/order access. | Given valid credentials, When user submits the login form, Then user is redirected to the product listing page. | High |
| FR-02 | System shall reject login with invalid username/password and display an error message. | Gives the user clear feedback to correct input and reduces support requests. | Given invalid credentials, When user submits the form, Then error "Username and password do not match any user" is shown and user stays on the login page. | High |
| FR-03 | System shall block login for a "locked out" user account and show a specific reason. | Distinguishes a locked account from a simple wrong-password error, so the user knows what actually happened. | Given a locked-out username, When user submits valid password, Then error "Sorry, this user has been locked out" is shown. | Medium |
| FR-04 | System shall not allow empty username or password fields to be submitted. | Prevents unnecessary server calls and gives immediate validation feedback. | Given an empty field, When user clicks Login, Then a required-field error is shown and no request is sent. | Medium |
| FR-05 | System shall mask the password field input (dots/asterisks). | Basic privacy/security expectation — prevents shoulder-surfing of credentials. | Given the login form, When user types a password, Then characters are masked on screen. | Medium |
| FR-06 | System shall allow the user to log out from any authenticated page via the side menu. | Users must be able to end their session, especially on shared/public devices, to protect account and cart data. | Given a logged-in user, When user selects Logout from the menu, Then user is redirected to the login page and the session is cleared. | High |

### 4.2 Product Catalog & Navigation (FR-07 to FR-13)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-07 | System shall display all available products with name, price, image, and description. | The user needs enough information to decide on a purchase without extra clicks. | Given a logged-in user, When the inventory page loads, Then all products display name, price, and image without broken links. | High |
| FR-08 | System shall allow sorting products by name (A-Z, Z-A) and price (low-high, high-low). | Helps users find products faster — a core usability/retention feature in larger catalogs. | Given the sort dropdown, When user selects "Price low to high", Then products reorder ascending by price. | Medium |
| FR-09 | System shall allow a user to click a product to view its full detail page. | Users often need more detail (larger image, full description) before deciding to buy. | Given the inventory page, When user clicks a product name/image, Then the product detail page opens with matching info. | Medium |
| FR-10 | System shall provide a "Back to products" navigation option from the product detail page. | Users must be able to return to browsing without using the browser back button, which can cause state issues. | Given the product detail page, When user clicks "Back to products", Then the full inventory list is shown again. | Low |
| FR-11 | System shall display a hamburger/side menu with navigation links (All Items, About, Logout, Reset App State). | Centralized navigation is a baseline usability expectation for any e-commerce site. | Given a logged-in user, When user clicks the menu icon, Then the side menu opens with all four links visible. | Low |
| FR-12 | System shall provide a "Reset App State" option that clears the cart without logging the user out. | Lets a user (or tester) reset test data quickly without a full re-login — useful both for real users abandoning a cart and for QA repeatability. | Given items in cart, When user clicks "Reset App State", Then cart count returns to 0 while user remains logged in. | Low |
| FR-13 | System shall retain the selected sort order when navigating between inventory and a product detail page and back. | Losing a user's applied preference (like sort order) mid-session is a common usability complaint. | Given a non-default sort applied, When user opens and closes a product detail page, Then the sort order remains applied. | Low |

### 4.3 Cart Management (FR-14 to FR-20)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-14 | System shall allow a user to add a product to the cart, updating the cart icon count. | Core purchasing action — if the count doesn't update, the user has no confidence the action succeeded, risking duplicate clicks or abandonment. | Given a product page, When user clicks "Add to Cart", Then button changes to "Remove" and cart badge count increases by 1. | High |
| FR-15 | System shall allow a user to remove ("cancel") an item from the cart, from either the product page or the cart page, decreasing the cart count. | Users must be able to correct mistakes or change their mind before checkout — a missing/incorrect cancel response directly causes billing disputes and lost trust. | Given an item in the cart, When user clicks "Remove", Then the item disappears from the cart list and the badge count decreases by 1, with no page reload required. | High |
| FR-16 | System shall persist cart contents while the user continues browsing in the same session. | Losing cart items when navigating between pages is a top cause of cart abandonment in e-commerce. | Given items in cart, When user navigates from product page to cart page and back, Then cart items remain unchanged. | Medium |
| FR-17 | System shall show an accurate item count and item list on the Cart page before checkout. | Prevents billing disputes — the user must verify what they're about to pay for before committing. | Given items in the cart, When user opens the Cart page, Then listed items and count match what was actually added. | High |
| FR-18 | System shall allow the user to continue shopping from the Cart page without losing cart contents. | Users frequently add items, check the cart, then return to browse more — this must not reset their progress. | Given items in cart, When user clicks "Continue Shopping", Then inventory page loads with cart contents intact. | Medium |
| FR-19 | System shall not allow the same product to be added to the cart twice as a duplicate line item. | Prevents billing confusion — duplicate line items for the same product would misrepresent the order. | Given a product already in cart, When user revisits and the button reads "Remove", Then no second "Add to Cart" action is available for that item. | Medium |
| FR-20 | System shall display an empty-cart state clearly when no items have been added. | Users need explicit confirmation the cart is empty rather than an ambiguous blank page, to avoid confusion over a possible bug. | Given no items added, When user opens the Cart page, Then an empty cart list is shown (no items, no error). | Low |

### 4.4 Checkout Flow (FR-21 to FR-27)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-21 | System shall require First Name, Last Name, and Zip/Postal Code before proceeding to order overview. | Minimum shipping/billing identification needed to process an order. | Given the checkout info form, When any required field is empty, Then an inline error is shown and user cannot proceed. | High |
| FR-22 | System shall validate that checkout fields do not accept only whitespace as valid input. | Prevents users from bypassing required-field validation with blank-looking input, which would corrupt order records. | Given a field with only spaces, When user submits, Then the required-field error is still triggered. | Medium |
| FR-23 | System shall display an Order Overview screen showing item list, price total, tax, and grand total before final confirmation. | A user must see the final charge amount before confirming a purchase — prevents "surprise" charges and disputes. | Given valid checkout info, When user proceeds, Then Overview page shows itemized price, tax, and total that mathematically add up. | High |
| FR-24 | System shall allow the user to cancel the checkout process at any step and return to the cart without losing cart data. | Users must retain control to back out of a purchase — losing the cart on cancel is a critical usability failure. | Given any checkout step, When user clicks "Cancel", Then user returns to the Cart page with all items intact. | Medium |
| FR-25 | System shall calculate tax as a consistent percentage of the item subtotal. | Incorrect tax calculation is both a compliance risk and a direct cause of billing disputes. | Given a known subtotal, When Overview page loads, Then displayed tax matches the expected tax rate calculation. | Medium |
| FR-26 | System shall display an order confirmation message upon successful checkout completion. | Confirms to the user that the order succeeded — its absence creates uncertainty over whether the purchase went through. | Given completed checkout, When user clicks "Finish", Then confirmation message "Thank you for your order!" and success icon are displayed. | High |
| FR-27 | System shall clear the cart automatically after a successful order completion. | An already-placed order must not remain in the cart, or the user risks accidentally re-ordering the same items. | Given a completed order, When user returns to the inventory page, Then cart badge count shows 0. | Medium |

### 4.5 Payment (Current State + Documented Gap) (FR-28 to FR-29)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-28 | System shall simulate payment authorization automatically after checkout info is submitted (no real payment gateway in current version). | Since this AUT is a demo application, payment is mocked — documented so testers don't expect a real payment form, and so the gap is flagged for future real-world equivalents. | Given valid checkout info, When user reaches Overview, Then a "Payment Information" summary line (mock data) is shown without requiring real card entry. | Low |
| FR-29 | System shall display shipping information as a static/mock value on the Overview screen. | Confirms to the user that a delivery method was considered, even in a demo/mock context, matching real checkout UX conventions. | Given the Overview page, When it loads, Then a "Shipping Information" line is present with a mock value. | Low |

---

## 5. Functional Requirements — Backend API (Fake Store API)

### 5.1 Product Endpoints (FR-30 to FR-33)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-30 | API shall return a list of all products via GET `/products` with a 200 status code. | The product catalog is the foundation of the storefront — this endpoint must be reliable since the entire UI depends on it. | Given a GET request to `/products`, When called, Then response status is 200 and body is a non-empty JSON array. | High |
| FR-31 | API shall return details of a single product via GET `/products/{id}` for a valid ID. | Product detail pages depend on fetching a specific item's full data. | Given a valid product ID, When GET `/products/{id}` is called, Then response contains matching id, title, price, and category fields. | High |
| FR-32 | API shall return a 404 or empty response for a GET request with a non-existent product ID. | Prevents the frontend from silently rendering a broken/blank product page — the API must signal invalid data clearly. | Given an invalid product ID (e.g. 99999), When GET `/products/{id}` is called, Then response indicates not found. | Medium |
| FR-33 | API shall support filtering/listing products by category via GET `/products/category/{name}`. | Category filtering is core to product discovery on any e-commerce platform. | Given a valid category name, When endpoint is called, Then only products belonging to that category are returned. | Medium |

### 5.2 Cart Endpoints (FR-34 to FR-37)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-34 | API shall create a new cart via POST `/carts` with userId, date, and product list. | The backend equivalent of "add to cart" — must reliably persist what the user intends to buy. | Given valid cart payload, When POST `/carts` is called, Then response status is 200/201 and returns a cart object with an id. | High |
| FR-35 | API shall retrieve an existing cart via GET `/carts/{id}`. | The frontend must be able to re-fetch cart state (e.g. on page reload) without data loss. | Given a valid cart ID, When GET `/carts/{id}` is called, Then response returns the matching cart with correct product list. | High |
| FR-36 | API shall update an existing cart via PUT `/carts/{id}` (e.g. changing quantity). | Users changing item quantities is a standard cart interaction that the backend must support. | Given a valid cart ID and updated payload, When PUT is called, Then response reflects the updated product list/quantities. | Medium |
| FR-37 | API shall delete a cart via DELETE `/carts/{id}`. | Backend equivalent of clearing/canceling a cart — must actually remove the data, not just hide it. | Given a valid cart ID, When DELETE is called, Then response confirms deletion and subsequent GET returns not-found. | Medium |

### 5.3 User & Auth Endpoints (FR-38 to FR-40)

| Req ID | Requirement | Business Need | Acceptance Criteria | Priority |
|---|---|---|---|---|
| FR-38 | API shall authenticate a user via POST `/auth/login` with valid username and password, returning a token. | The backend equivalent of FR-01 — the UI login only works if this endpoint correctly issues session tokens. | Given valid credentials, When POST `/auth/login` is called, Then response status is 200 and a token is returned. | High |
| FR-39 | API shall reject POST `/auth/login` with invalid credentials and return an appropriate error status. | Prevents unauthorized access at the API level, independent of any UI-level validation (UI checks can be bypassed by direct API calls). | Given invalid credentials, When POST `/auth/login` is called, Then response status is 401 (or documented error code) with no token returned. | High |
| FR-40 | API shall retrieve user profile details via GET `/users/{id}` for a valid user ID. | Confirms the backend correctly stores and returns user account data used across checkout (name, address). | Given a valid user ID, When GET `/users/{id}` is called, Then response returns matching user fields (name, email, address). | Medium |

---

## 6. Non-Functional Requirements (NFR-01 to NFR-06)

| Req ID | Requirement | Business Need | Priority |
|---|---|---|---|
| NFR-01 | Login response time shall be under 2 seconds under normal load. | Slow login is a top cause of user drop-off before reaching the product catalog. | Medium |
| NFR-02 | The application shall be usable on the latest stable versions of Chrome and Firefox. | Cross-browser support is a baseline expectation for any public-facing e-commerce site. | Medium |
| NFR-03 | Error messages shall be clear, specific, and in plain English (no raw system/stack errors exposed to the user). | Protects usability and security — stack traces can leak implementation details to attackers. | High |
| NFR-04 | API endpoints shall respond within 1 second for single-resource GET requests under normal load. | Slow API responses directly degrade perceived UI performance, since the frontend blocks on these calls. | Medium |
| NFR-05 | The application shall remain functional and readable at common desktop resolutions (1366x768 and above). | Ensures a baseline responsive/usable layout without requiring a full responsive-design overhaul for this project's scope. | Low |
| NFR-06 | Sensitive fields (password) shall never be logged or displayed in plaintext in application error messages. | Basic security hygiene — prevents credential leakage through logs or debug output. | High |

---

## 7. Assumptions / Out of Scope (Documented Gaps)
Documented explicitly rather than silently ignored — standard practice when the AUT doesn't fully match a production system:

- **Payment types:** The AUT does not support selecting a real payment method (credit card, wallet, COD, etc.). A production system would need a dedicated requirement set covering supported card types, validation rules (card number, CVV, expiry), and failed-payment handling. Flagged as **future scope**.
- **Multi-language / localization:** The AUT is English-only. A production requirement set would need entries for language switcher behavior, currency localization, and RTL layout support. Flagged as **future scope**.
- **Guest checkout:** Not available — login is mandatory. Documented as a design constraint, not a defect.
- **API authentication persistence:** Fake Store API's `/auth/login` token is not actually required by other endpoints (all data is mocked/reset periodically) — documented so testers don't mistake this for a security defect.

## 8. Traceability Note
Each Req ID above will map to one or more Test Case IDs in the Manual Testing tracker and, for Section 5, to Postman requests in `/api-testing/` (e.g., FR-15 → TC-012, TC-013; FR-38 → Postman "Login - Valid Credentials"). The full traceability matrix will be added once the test case tracker is complete.

---
**Total Requirements: 40 Functional (FR-01–FR-40) + 6 Non-Functional (NFR-01–NFR-06) = 46**
