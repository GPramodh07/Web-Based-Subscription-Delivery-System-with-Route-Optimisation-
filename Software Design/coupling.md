# Coupling Analysis

Definition:
Coupling refers to the degree of dependency between modules.

---

---

##  Subscription Management → User Management

Coupling Type: Data Coupling

Description:\
Data Communicated: user_id (integer)
Data Type: primitive numeric identifier

Files Involved:
 → [backend/controllers/subscriptionController.js](backend/controllers/subscriptionController.js)
 → [backend/controllers/addressController.js](backend/controllers/addressController.js)
 → [backend/models/subscriptionModel.js](backend/models/subscriptionModel.js)

Code Evidence:
subscriptionController.getUserSubscriptions reads user_id and calls getSubscriptionsByUserId(userId)

Justification:\
Data coupling exists because the subscription module depends only on a numeric user_id from user management to query and persist subscription/address records.

---

##  Payment Processing → User Management

Coupling Type: Data Coupling

Description:\
Data Communicated: user_id (integer), subscription_id (integer)
Data Type: primitive numeric identifiers

Files Involved:
 → [backend/controllers/comboController.js](backend/controllers/comboController.js)
 → [backend/models/comboModel.js](backend/models/comboModel.js)

Code Evidence:
ComboModel.createPayment inserts payments with user_id and subscription_id

Justification:\
Data coupling exists because the payment module consumes only primitive identifiers supplied by user/subscription context.

---

##  Order Processing → User Management

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: users.id, users.name, users.role, users.status

Files Involved:
 → [backend/services/orderService.js](backend/services/orderService.js)
 → [backend/models/userModel.js](backend/models/userModel.js)

Code Evidence:
getOrdersByPartnerId joins orders with users to fetch customer_name

Justification:\
Common coupling exists because both modules depend on shared users table fields, so schema changes ripple across them.

---

##  Delivery Management → User Management

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: users.id, users.role, users.status, users.current_lat, users.current_lng

Files Involved:
 → [backend/services/deliveryClustering.js](backend/services/deliveryClustering.js)
 → [backend/controllers/deliveryClusteringController.js](backend/controllers/deliveryClusteringController.js)

Code Evidence:
generateDeliveryRoutes selects users where role = 'delivery_partner'; setPartnerLocation updates users.current_lat/current_lng

Justification:\
Common coupling exists because both modules share global user data items (partner identity and GPS columns).

---

##  Reports and Salary Management → User Management

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: users.id, users.role, users.status, users.name

Files Involved:
 → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)
 → [backend/models/userModel.js](backend/models/userModel.js)

Code Evidence:
getMonthSalaryOverview selects from users; requireAdmin calls findById(userId)

Justification:\
Common coupling exists because the salary module reads shared user data items to validate admins and compute partner salary records.

---

##  Payment Processing → Subscription Management

Coupling Type: Stamp Coupling

Description:\
Data Structure Communicated: address object { street, city, state, pincode, latitude, longitude, address_type }
Data Structure Communicated: combo object { items: [ { item_id, price, quantity } ] }

Files Involved:
 → [backend/controllers/comboController.js](backend/controllers/comboController.js)
 → [backend/models/subscriptionModel.js](backend/models/subscriptionModel.js)

Code Evidence:
completeCheckout consumes address and combo.items objects before inserting into subscriptions

Justification:\
Stamp coupling exists because the subscription creation logic consumes structured payloads instead of simple scalar parameters.

---

##  Subscription Management → Payment Processing

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: payments.subscription_id, payments.user_id, payments.amount

Files Involved:
 → [backend/models/subscriptionModel.js](backend/models/subscriptionModel.js)
 → [backend/models/comboModel.js](backend/models/comboModel.js)

Code Evidence:
deleteSubscriptionByIdForUser executes DELETE FROM payments WHERE subscription_id = ?

Justification:\
Common coupling exists because both modules depend on shared global data items in the payments table.

---

##  Order Processing → Subscription Management

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: subscriptions.status, subscriptions.delivery_slot, subscriptions.combo_id, subscription_pauses.pause_date, subscription_pauses.subscription_id

Files Involved:
 → [backend/services/orderService.js](backend/services/orderService.js)
 → [backend/models/subscriptionModel.js](backend/models/subscriptionModel.js)

Code Evidence:
generateDailyOrders inserts into orders by selecting active subscriptions and combos

Justification:\
Common coupling exists because the order module reads shared global data items from subscriptions and subscription_pauses.

---

##  Delivery Management → Order Processing

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: orders.order_id, orders.partner_id, orders.status, orders.delivery_date, orders.delivery_slot

Files Involved:
 → [backend/services/deliveryClustering.js](backend/services/deliveryClustering.js)
 → [frontend/assets/js/delivery/delivery_maps.js](frontend/assets/js/delivery/delivery_maps.js)
 → [backend/services/orderService.js](backend/services/orderService.js)

Code Evidence:
UPDATE orders SET partner_id = ? in generateDeliveryRoutes; delivery_maps.js calls /api/orders/:id/deliver

Justification:\
Common coupling exists because both modules depend on shared global order data items.

---

##  Reports and Salary Management → Order Processing

Coupling Type: Common Coupling

Description:\
Global Data Items Shared: orders.status, orders.delivery_date, orders.total_amount, orders.partner_id

Files Involved:
 → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)
 → [backend/services/orderService.js](backend/services/orderService.js)

Code Evidence:
getMonthSalaryOverview joins orders to compute delivery counts and subscription revenue

Justification:\
Common coupling exists because salary reporting depends on shared global order data items.

---

# Final Summary

Most Common Coupling:
Common Coupling

Control Coupling Present:
No

Stamp Coupling Present:
Yes → Payment Processing → Subscription Management

Common Coupling:
Present

Content Coupling:
Not Present

Conclusion:
System is tightly coupled because multiple modules share core database tables (users, orders, subscriptions, payments), creating broad ripple effects from schema changes.

---

---
