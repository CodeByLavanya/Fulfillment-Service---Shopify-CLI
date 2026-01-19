Develop a Shopify app that guides the merchant through a multi-step setup process
upon installation.
Step 1: Service Setup
The first step should display an informational screen with a button. The information
should clearly state that clicking the button will:
 Create a Carrier Service
 Create a Fulfillment Service
 Register an Order Created webhook
The IDs of the created services and webhook must be stored in Prisma (or any chosen
database).
Step 2: Product Configuration
The second step should display two sections: Available Products and Added
Products.
 Merchants should be able to search for products from the store and add them.
 Once a product is added, its information must be stored in the database.
 While adding the product, it must also be associated with the previously created
Fulfillment Service.
 If the merchant closes the setup midway and returns later, the app should
resume from the step where they left off. Step completion status must be
persisted.
 The merchant must add at least one product to proceed to the next step.
Step 3: Dashboard
After completing the setup, the merchant should be redirected to the Dashboard, which
contains two pages:
 Products
 Orders
Products Page
 Display all previously added products.
 Provide an Add Product button that opens a popup, allowing the merchant to
add new products.
Once the merchant completes the setup and lands on the dashboard, any subsequent
visits to the app should take them directly to the dashboard, not the setup flow.

Node.js Express Server Requirements
Create a separate Node.js Express server with the following API endpoints:
1. POST /inventory
2. POST /request-fulfillment
3. POST /fulfill-order
4. POST /carrier-service
 The /carrier-service endpoint must be configured as the callback URL when
creating the Shopify Carrier Service.
 The Order Created webhook should use the Shopify app’s backend as the
callback URL.

Inventory Management Flow
In the app’s Admin Dashboard → Products section:
 Each product should have an Update Inventory button.
 When clicked, the app should call the /inventory API on the Node server and send
the product SKU.
 If the SKU length is 4, multiply it by 2 and return the calculated value as the
inventory count.

Shipping Rate Logic (Carrier Service)
When a customer adds products to the cart and proceeds to checkout:
 Shopify should trigger the Carrier Service callback to the Node server.
 Based on the total product count, return the following shipping options:
o 1 product → Standard Delivery (Free delivery, 4 days)
o 2 products → Standard Delivery and Moderate Delivery
o More than 3 products → Standard, Moderate, and Fast Delivery

Order Processing &amp; Fulfillment Flow
1. When the customer selects a shipping option and places the order:
o Capture the Order Created event in the Shopify app callback URL.
o Verify that the products in the order belong to your Fulfillment Service.

o If an order contains multiple items and only some belong to your fulfillment
service (e.g., 4 items total, 3 under your service), store only those 3
items in the database.
o Store all required order details needed for fulfillment and admin display.
2. Create a Fulfillment Request in Shopify using only the line items under your
fulfillment service.
o Send these line items to the /request-fulfillment API on the Node server.
o The Node server should:
 Reject the request if the line item count is 1
 Accept the request if the line item count is greater than 1

3. Admin Dashboard → Orders Page
o List all stored orders with basic information and line item count.
o Each order should have a Fulfill Order button.
4. When the admin clicks Fulfill Order:
o Send the order details to the /fulfill-order API.
o The Node server should respond with:
 Tracking URL
 Carrier and shipment details required by Shopify
o Use the response to complete the fulfillment in Shopify with the provided
tracking and shipping information.

<img width="1440" height="900" alt="Screenshot 2026-01-19 at 8 12 24 PM" src="https://github.com/user-attachments/assets/834963d8-0701-4137-84bd-563262a74507" />



<img width="1440" height="900" alt="Screenshot 2026-01-19 at 8 12 40 PM" src="https://github.com/user-attachments/assets/2f5ca77b-95c5-4bb8-9a24-7a50d142423f" />


<img width="1194" height="585" alt="Screenshot 2026-01-19 at 8 19 46 PM" src="https://github.com/user-attachments/assets/e379f725-e826-4a42-be40-af6d60422caa" />


