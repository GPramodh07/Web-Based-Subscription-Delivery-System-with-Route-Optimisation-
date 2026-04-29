# Cohesion Analysis

Definition:
Cohesion refers to the strength of the relationship between functions within a module.

---

## User Management

Register User [register()] → Authenticate User [login()]
Cohesion Type: Communicational Cohesion

Description: Both functions operate on the same user credential data (email, password) and user identity records.

Files Involved: [backend/controllers/authController.js](backend/controllers/authController.js) → [backend/controllers/authController.js](backend/controllers/authController.js)

Code Evidence:
register() checks existing user via findByEmail(); login() retrieves user via findByEmail() before verifying credentials.

Justification: The functions share the same user data set and database entities, indicating communicational cohesion.

---

Authenticate User [login()] → Update User Profile [updateProfile()]
Cohesion Type: Communicational Cohesion

Description: Both functions access the same user account record and identifiers.

Files Involved: [backend/controllers/authController.js](backend/controllers/authController.js) → [backend/controllers/authController.js](backend/controllers/authController.js)

Code Evidence:
login() fetches user by email; updateProfile() fetches user by id and updates user profile fields.

Justification: The relationship is based on shared user data rather than a strict output-to-input flow.

---

Manage Users [getCustomers(), getDeliveryPartners()] → Manage Users [updateManagedUserStatus()]
Cohesion Type: Communicational Cohesion

Description: Management operations query and update the same user records for customers and delivery partners.

Files Involved: [backend/controllers/adminUserController.js](backend/controllers/adminUserController.js) → [backend/controllers/adminUserController.js](backend/controllers/adminUserController.js)

Code Evidence:
getCustomers()/getDeliveryPartners() call getUsersByRole(); updateManagedUserStatus() calls findById() and updateUserStatus().

Justification: The functions work on the same user dataset, showing communicational cohesion.

---

Manage Users [requireAdmin()] → Manage Users [getCustomers()]
Cohesion Type: Sequential Cohesion

Description: Admin validation must complete before user listing handlers execute.

Files Involved: [backend/routes/adminUserRoutes.js](backend/routes/adminUserRoutes.js) → [backend/controllers/adminUserController.js](backend/controllers/adminUserController.js)

Code Evidence:
router.use(requireAdmin); then router.get("/users/customers", getCustomers).

Justification: The output of admin validation controls access to subsequent user management functions.

---

Register User [register()] → Manage Users [updateManagedUserStatus()]
Cohesion Type: Communicational Cohesion

Description: Both functions operate on the same users table records after creation or during status changes.

Files Involved: [backend/controllers/authController.js](backend/controllers/authController.js) → [backend/controllers/adminUserController.js](backend/controllers/adminUserController.js)

Code Evidence:
register() creates users via createUser(); updateManagedUserStatus() updates user status via updateUserStatus().

Justification: Shared user record data links the functions rather than a strict sequence.

---

## Subscription Management

Address and Slot Booking [addAddress(), updateAddress()] → Subscription Enrollment [completeCheckout()]
Cohesion Type: Sequential Cohesion

Description: Address capture precedes subscription enrollment and is consumed during checkout.

Files Involved: [backend/controllers/addressController.js](backend/controllers/addressController.js) → [backend/controllers/comboController.js](backend/controllers/comboController.js)

Code Evidence:
completeCheckout() validates and inserts address details before inserting subscriptions.

Justification: Address information output is required to create the subscription, forming a sequential flow.

---

Manage Items [getAllItems()] → Subscription Enrollment [completeCheckout()]
Cohesion Type: Communicational Cohesion

Description: Item definitions are used to assemble combo items for subscription enrollment.

Files Involved: [backend/controllers/itemController.js](backend/controllers/itemController.js) → [backend/controllers/comboController.js](backend/controllers/comboController.js)

Code Evidence:
completeCheckout() consumes combo.items (item_id, price, quantity) that originate from items listing and selection.

Justification: Both functions operate on the same item data set, indicating communicational cohesion.

---

Subscription Enrollment [completeCheckout()] → Manage Subscription Status [pauseSubscriptionForToday()]
Cohesion Type: Sequential Cohesion

Description: A subscription must exist before pause/unpause or delete actions can be applied.

Files Involved: [backend/controllers/comboController.js](backend/controllers/comboController.js) → [backend/controllers/subscriptionController.js](backend/controllers/subscriptionController.js)

Code Evidence:
completeCheckout() inserts subscriptions; pauseSubscriptionForToday() updates subscription pause state by id.

Justification: Subscription creation enables subsequent status operations, forming a sequential relationship.

---

Address and Slot Booking [getUserAddresses()] → Manage Subscription Status [getUserSubscriptions()]
Cohesion Type: Communicational Cohesion

Description: Both functions use the same user_id to retrieve related address and subscription data.

Files Involved: [backend/controllers/addressController.js](backend/controllers/addressController.js) → [backend/controllers/subscriptionController.js](backend/controllers/subscriptionController.js)

Code Evidence:
getUserAddresses() queries addresses by user_id; getUserSubscriptions() queries subscriptions by user_id.

Justification: Shared user identifiers create communicational cohesion across these functions.

---

Manage Subscription Status [pauseSubscriptionForToday()] → Manage Subscription Status [unpauseSubscriptionForToday()]
Cohesion Type: Procedural Cohesion

Description: Both functions handle the ordered workflow of pausing or resuming subscription delivery for a day.

Files Involved: [backend/controllers/subscriptionController.js](backend/controllers/subscriptionController.js) → [backend/controllers/subscriptionController.js](backend/controllers/subscriptionController.js)

Code Evidence:
pauseSubscriptionForToday() and unpauseSubscriptionForToday() call corresponding model functions to update pause records.

Justification: The functions are part of a fixed operational procedure for status handling.

---

## Order Processing

Order Creation [generateDailyOrders()] → Provide Order Status [getUserOrders()]
Cohesion Type: Sequential Cohesion

Description: Orders are generated first, then queried for customers and admins.

Files Involved: [backend/services/orderService.js](backend/services/orderService.js) → [backend/controllers/orderController.js](backend/controllers/orderController.js)

Code Evidence:
orderController.getUserOrders() calls getOrdersByCustomerId(); generateDailyOrders() inserts orders into the orders table.

Justification: Status retrieval depends on orders created earlier, giving sequential cohesion.

---

Provide Order Status [markDelivered()] → Provide Order Status [getTrackingInfo()]
Cohesion Type: Communicational Cohesion

Description: Both functions read and update the same order status data for tracking.

Files Involved: [backend/controllers/orderController.js](backend/controllers/orderController.js) → [backend/controllers/orderController.js](backend/controllers/orderController.js)

Code Evidence:
markDelivered() calls markOrderDelivered(); getTrackingInfo() calls getLatestOrderBySubscription() which reads order status.

Justification: Shared order status data links the functions, indicating communicational cohesion.

---

Provide Order Status [getAdminOrdersByDate()] → Provide Order Status [getPartnerOrders()]
Cohesion Type: Communicational Cohesion

Description: Both functions read from the same orders table with different filters.

Files Involved: [backend/controllers/orderController.js](backend/controllers/orderController.js) → [backend/controllers/orderController.js](backend/controllers/orderController.js)

Code Evidence:
getAdminOrdersByDate() calls getOrdersByDate(); getPartnerOrders() calls getOrdersByPartnerId().

Justification: They operate on the same data set (orders) and are related by shared data.

---

Order Creation [generateTodayDemoOrders()] → Provide Order Status [resetOrderForDemo()]
Cohesion Type: Procedural Cohesion

Description: Demo order creation and reset follow a defined sequence for demonstration workflows.

Files Involved: [backend/controllers/orderController.js](backend/controllers/orderController.js) → [backend/controllers/orderController.js](backend/controllers/orderController.js)

Code Evidence:
generateTodayDemoOrders() calls generateDailyOrders(); resetOrderForDemo() calls resetOrderToOutForDelivery().

Justification: Both functions support a fixed demo procedure rather than a single functional task.

---

## Delivery Management

Route Generation [generateRoutes()] → Assign Delivery [generateDeliveryRoutes()]
Cohesion Type: Sequential Cohesion

Description: Route generation in the controller triggers service logic that assigns deliveries to partners.

Files Involved: [backend/controllers/deliveryClusteringController.js](backend/controllers/deliveryClusteringController.js) → [backend/services/deliveryClustering.js](backend/services/deliveryClustering.js)

Code Evidence:
generateRoutes() calls generateDeliveryRoutes(date, slot) to compute clusters and update orders.partner_id.

Justification: The controller output directly feeds the assignment operation, forming sequential cohesion.

---

Assign Delivery [generateDeliveryRoutes()] → Update Delivery Record [setPartnerLocation()]
Cohesion Type: Communicational Cohesion

Description: Both functions use the same partner_id and delivery records to track assignment and live location.

Files Involved: [backend/services/deliveryClustering.js](backend/services/deliveryClustering.js) → [backend/controllers/deliveryClusteringController.js](backend/controllers/deliveryClusteringController.js)

Code Evidence:
generateDeliveryRoutes() sets orders.partner_id; setPartnerLocation() updates users.current_lat/current_lng for that partner.

Justification: Shared partner assignment data links the functions.

---

Route Generation [getPartnerRoute()] → Update Delivery Record [getMyRoute()]
Cohesion Type: Sequential Cohesion

Description: Controller retrieval uses the service route query to expose delivery assignments.

Files Involved: [backend/services/deliveryClustering.js](backend/services/deliveryClustering.js) → [backend/controllers/deliveryClusteringController.js](backend/controllers/deliveryClusteringController.js)

Code Evidence:
getMyRoute() calls getPartnerRoute(partnerId, date) and returns the assigned orders.

Justification: The controller output depends on the service query output, indicating sequential cohesion.

---

## Payment Processing

Verify Payment [completeCheckout()] → Payment Processing [createPayment()]
Cohesion Type: Sequential Cohesion

Description: Payment validation happens before payment persistence.

Files Involved: [backend/controllers/comboController.js](backend/controllers/comboController.js) → [backend/models/comboModel.js](backend/models/comboModel.js)

Code Evidence:
completeCheckout() validates payment method/details then calls ComboModel.createPayment(...).

Justification: Payment verification output is required before the payment record is created.

---

## Reports and Salary Management

Generate Reports [requireAdmin()] → Generate Reports [getMonthSalaryOverview()]
Cohesion Type: Sequential Cohesion

Description: Admin validation must complete before report generation.

Files Involved: [backend/controllers/salaryController.js](backend/controllers/salaryController.js) → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)

Code Evidence:
router.use(requireAdmin); then router.get("/salaries/overview", getMonthSalaryOverview).

Justification: Authorization output gates report generation, forming a sequential relation.

---

Calculate Salary [getMonthSalaryOverview()] → Calculate Salary [approveAllSalaries()]
Cohesion Type: Sequential Cohesion

Description: Salary calculation and record creation precede approval.

Files Involved: [backend/controllers/salaryController.js](backend/controllers/salaryController.js) → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)

Code Evidence:
getMonthSalaryOverview() inserts/updates partner_salary_records; approveAllSalaries() updates status to approved.

Justification: Approval depends on prior calculation output, indicating sequential cohesion.

---

Calculate Salary [approveAllSalaries()] → Calculate Salary [markAllSalariesPaid()]
Cohesion Type: Sequential Cohesion

Description: Salaries are approved before being marked as paid.

Files Involved: [backend/controllers/salaryController.js](backend/controllers/salaryController.js) → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)

Code Evidence:
approveAllSalaries() updates status to approved; markAllSalariesPaid() updates status to paid.

Justification: This is a sequential workflow for salary processing.

---

Generate Reports [getMonthSalaryOverview()] → Calculate Salary [updateSingleSalary()]
Cohesion Type: Communicational Cohesion

Description: Both functions operate on the same partner_salary_records data.

Files Involved: [backend/controllers/salaryController.js](backend/controllers/salaryController.js) → [backend/controllers/salaryController.js](backend/controllers/salaryController.js)

Code Evidence:
getMonthSalaryOverview() writes salary rows; updateSingleSalary() updates bonus/deductions for the same month record.

Justification: Shared salary record data connects the functions, indicating communicational cohesion.

---

# Final Summary

Highest Cohesion Type: Sequential Cohesion

Sequential Cohesion Present:
Yes → User Management (requireAdmin → getCustomers), Subscription Management (address to enrollment), Order Processing (order creation to status), Delivery Management (generateRoutes → generateDeliveryRoutes), Payment Processing (verify → createPayment), Reports and Salary Management (calculate → approve/pay)

Procedural Cohesion Present:
Yes → Subscription Management (pause/unpause workflow), Order Processing (demo order workflow)

Communicational Cohesion Present:
Yes → User Management (shared user records), Subscription Management (shared user_id and items), Order Processing (shared orders), Delivery Management (shared partner_id), Reports and Salary Management (shared salary records)

Logical Cohesion Present:
No

Coincidental Cohesion:
Not Present

Conclusion: Overall cohesion is moderate to high, with most modules exhibiting sequential and communicational cohesion driven by ordered workflows and shared data.

---

---
