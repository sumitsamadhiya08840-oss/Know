# CITY LEVEL DIGITAL MARKETPLACE PLATFORM

## 1. Project Title
CityConnect – One City, One Marketplace

---

# 2. Vision
Har city ki sabhi shops ko ek hi digital platform par lana jahan se customers poori city ke products dekh sake, compare kar sake aur order kar sake.

Goal: "City ka apna Amazon" banana.

---

# 3. Problem Statement
- Local shops online visible nahi hoti.
- Customers ko har shop alag-alag visit karni padti hai.
- Small shopkeepers ke paas website/app banane ka budget nahi hota.
- City level par koi centralized digital marketplace nahi hai.

---

# 4. Solution Overview
Ek centralized city-based app & website jahan:
- Har shop online registration kar sake.
- Har shop ko personal URL mile.
- Har shop ko QR code mile jo wo apni shop par laga sake.
- Customers city ki kisi bhi shop se order kar sake.

Example:
cityapp.in/gupta-medical
cityapp.in/sharma-electronics

---

# 5. Core Features

## 5.1 Shop Registration
- Online registration
- Shop details upload
- GST / basic verification
- Shop dashboard access

## 5.2 Personal Shop Page (Mini Digital Dukaan)
- Shop name, address, timing
- Product listing
- Offers & discounts
- Reviews & ratings
- WhatsApp integration
- QR code generation

## 5.3 Customer Features
- Area wise filter
- Nearby shop search
- Price comparison
- Reviews system
- Cash on delivery option

---

# 6. Revenue Model

## 6.1 Commission Model
- Har product par 3% commission.
- Example: Agar product ₹1000 ka hai → ₹30 platform commission.

## 6.2 Subscription Model (Model 2 – 6 Month Free Plan)

Launch Strategy:
"Launch offer – 6 mahine bilkul free"

6 mahine ke baad:
- ₹299/month – Basic Plan
- ₹999/month – Premium Plan

Psychological Advantage:
Shopkeeper ko lagega ki usko pehle free service mili hai, isliye paid plan lena easy ho jayega.

Basic Plan Includes:
- Standard listing
- Limited product upload
- Basic visibility

Premium Plan Includes:
- Unlimited products
- Top listing placement
- Sponsored visibility
- Analytics dashboard
- Featured tag

## 6.3 Delivery Charges Model
Delivery cost:
- Ya to Customer pay kare
- Ya Shop pay kare

Flexible system rakha jayega jahan shop decide kar sake delivery ka charge kaun bear karega.

---

# 7. Execution Plan

## Phase 1 – Platform Development
- Website + Mobile App development
- Shop dashboard system
- QR code integration
- Basic listing system

## Phase 2 – Shop Onboarding
- Target: First 100 shops free lifetime
- Physical visit + demo
- QR sticker distribution
- Launch offer promotion

## Phase 3 – Customer Acquisition
- Local marketing
- Social media ads
- College promotions
- Auto branding
- Referral system

## Phase 4 – Delivery Integration
- Local delivery partners onboard
- Live tracking system
- Same-day delivery option

---

# 8. Competitive Advantage
- Sirf ek city par focus
- Hyperlocal targeting
- Direct shop-customer connection
- Low commission (3%)
- Affordable subscription plans

---

# 9. Long Term Vision
- Har major city me expansion
- City loyalty program
- Advertisement revenue
- Sponsored product placements
- City ecosystem (jobs, services, rentals)

---

# 10. Future Scalability
- Multi-city architecture
- Franchise model
- Investor funding stage
- White-label city solution

---

# 11. Summary
Ye platform ek city-level digital revolution create karega jahan:
- Local shops ko online identity milegi
- Customers ko convenience milega
- Platform ko commission + subscription revenue milega

Business Model Mix:
✔ 3% Commission per product
✔ 6 Month Free Launch Offer
✔ ₹299 Basic Plan
✔ ₹999 Premium Plan
✔ Flexible Delivery Charges (Customer ya Shop pay kare)

---

END OF DOCUMENT


----------------------------------------------------------------------

APP Structure :

----------------------------------------------------------------------



# 📱 USER APP (Customer Layer)

## Technical Product Specification Document (Node.js Version)

---

# 1. 📌 Project Overview

Customer mobile application for a city-level multi-vendor marketplace platform.

The app allows customers to:

* Discover nearby shops
* Browse products
* Add items to cart
* Place orders
* Track orders

Frontend: React Native
Backend: Node.js (Express.js)
Database: MongoDB
Cache: Redis

---

# 2. 🏗 System Architecture

React Native App (Customer)
↓
Node.js (Express API Layer)
↓
PostgreSQL Database
↓
Admin Monitoring Layer
↓
Redis (Caching Layer)

Customer App never connects directly to database.

---

# 3. 👤 Authentication Engine

## Login Method

* Phone Number (OTP Based)
* JWT Token Authentication
* Refresh Token System

## Database Schema

### users

| Field      | Type      | Notes              |
| ---------- | --------- | ------------------ |
| id         | UUID      | Primary Key        |
| name       | varchar   | nullable           |
| phone      | varchar   | unique             |
| role       | varchar   | default = customer |
| is_active  | boolean   | default true       |
| created_at | timestamp | auto               |
| updated_at | timestamp | auto               |

## Security Flow

* OTP stored temporarily in Redis
* JWT access token (short expiry)
* Refresh token stored securely

---

# 4. 📍 Geo-Location Engine

## Purpose

Show nearby shops within configurable radius (default 5 km).

## Tables

### user_locations

| Field      | Type      |
| ---------- | --------- |
| user_id    | FK        |
| latitude   | decimal   |
| longitude  | decimal   |
| updated_at | timestamp |

### shops

| Field       | Type    |
| ----------- | ------- |
| id          | UUID    |
| name        | varchar |
| category_id | FK      |
| latitude    | decimal |
| longitude   | decimal |
| is_active   | boolean |
| city_id     | FK      |

## Backend Logic

* Use Haversine Formula OR PostGIS extension
* Filter shops within radius
* Sort by distance ASC
* Cache nearby results (short duration)

---

# 5. 🗂 Category & Subcategory Engine

## categories

| id | name | icon | theme_color | is_active |

## subcategories

| id | category_id | name | is_active |

* Dynamic loading in app
* Cached using Redis

---

# 6. 🛍 Product Engine

## products

| Field       | Type      |
| ----------- | --------- |
| id          | UUID      |
| shop_id     | FK        |
| name        | varchar   |
| description | text      |
| price       | decimal   |
| stock       | integer   |
| image_url   | text      |
| is_active   | boolean   |
| created_at  | timestamp |

Indexes Required:

* shop_id
* name (for search)
* price

---

# 7. 🛒 Cart Engine

## carts

| id | user_id | shop_id | created_at |

Rule: One cart per shop (MVP limitation)

## cart_items

| id | cart_id | product_id | quantity |

Logic:

* If same product added → increase quantity
* If different shop → block or clear cart (MVP decision)

---

# 8. 📦 Order Engine

## orders

| Field        | Type         |
| ------------ | ------------ |
| id           | UUID         |
| user_id      | FK           |
| shop_id      | FK           |
| total_amount | decimal      |
| commission   | decimal (3%) |
| final_amount | decimal      |
| payment_mode | varchar      |
| status       | varchar      |
| created_at   | timestamp    |

### Status Options:

* pending
* accepted
* rejected
* preparing
* delivered
* cancelled

## order_items

| id | order_id | product_id | quantity | price |

---

# 9. 💰 Commission Logic

commission = total_amount * 0.03
shop_receivable = total_amount - commission

* Commission stored at order level
* Transaction must be atomic (use DB transaction)

---

# 10. 🔎 Search & Filter System

Supported Filters:

* Product name search (ILIKE)
* Shop name search
* Category filter
* Subcategory filter
* Price range
* Distance filter

Future Upgrade:

* ElasticSearch integration

---

# 11. 👤 Customer Profile Engine

## addresses

| id | user_id | address | latitude | longitude | is_default |

Features:

* Multiple addresses
* Default selection
* Address-based distance calculation

---

# 12. 🔔 Notification Engine

Use Firebase Cloud Messaging (FCM)

Trigger Events:

* Order placed
* Order accepted
* Order rejected
* Order delivered

Store notification logs in DB (optional).

---

# 13. 🌐 API Structure (Express.js REST)

## Auth APIs

POST   /api/v1/auth/register
POST   /api/v1/auth/verify-otp
POST   /api/v1/auth/login
POST   /api/v1/auth/refresh-token

## Shop APIs

GET    /api/v1/shops
GET    /api/v1/shops/:id

## Product APIs

GET    /api/v1/products
GET    /api/v1/products/:id

## Cart APIs

POST   /api/v1/cart/add
GET    /api/v1/cart
DELETE /api/v1/cart/remove

## Order APIs

POST   /api/v1/orders/create
GET    /api/v1/orders
GET    /api/v1/orders/:id

---

# 14. 🔐 Security Layer

* JWT Authentication
* Role-based middleware
* Input validation (Joi/Zod)
* SQL injection prevention
* Rate limiting (express-rate-limit)
* Helmet for HTTP security
* CORS control
* Logging (Winston)

---

# 15. ⚡ Performance Optimization

Database:

* Proper indexing
* Query optimization
* Connection pooling

Caching:

* Redis for shop list
* Redis for category list
* OTP stored in Redis

Frontend:

* Lazy loading
* Pagination
* Image CDN
* Compression

API:

* Response compression (gzip)
* Pagination required

---

# 16. 📊 Scalability Planning

* Add city_id in shops & users
* Multi-city ready architecture
* Load balancer compatible
* Stateless API design
* Docker ready deployment
* Microservice-ready separation (future split: Order Service, Auth Service)

---

# 17. 🚀 MVP Scope (Phase 1)

Included:

* OTP login
* Nearby shops
* Category filtering
* Single-shop cart
* COD only
* Order placement
* Basic order tracking
* Commission calculation (3%)

Excluded (Phase 2):

* Online payment
* Wallet
* Delivery tracking
* Loyalty points
* Referral system
* Reviews & ratings

---

# 18. 🔮 Advanced Features (Future Phase)

* Online Payment Gateway
* Wallet System
* Loyalty Points
* Referral System
* Real-time Delivery Tracking
* In-app Chat
* Ratings & Reviews
* AI-based product recommendation
* Flash offers & coupons
* Customer subscription plan
* ElasticSearch
* Analytics dashboard

---

# 19. 🧠 Core Engines Summary

1. Authentication Engine
2. Geo-location Engine
3. Category Engine
4. Product Engine
5. Cart Engine
6. Order Engine
7. Commission Engine
8. Notification Engine
9. Profile Engine
10. Search Engine

---

# 20. 📌 Developer Notes

* Use clean architecture pattern
* Separate controller, service, repository layers
* Use environment-based config
* Maintain transaction integrity during order creation
* Use async error handling middleware
* Code must be modular & scalable
* Follow REST standards strictly
* All IDs must be UUID
* Monetary values stored as decimal (not float)

---

#21. Flow:

A. Customer Journey Flow:-

App Open
   ↓
Location Permission
   ↓
Nearby Shops Fetch
   ↓
Shop Listing
   ↓
Product View
   ↓
Add to Cart
   ↓
Checkout
   ↓
Payment / COD
   ↓
Order Created
   ↓
Shopkeeper Notified
   ↓
Order Delivered



B. Technical Flow:-

Customer App
   ↓ (REST API)
API Gateway
   ↓
Auth Middleware
   ↓
Shop Service
   ↓
Product Service
   ↓
Cart Service
   ↓
Order Service
   ↓
Payment Service
   ↓
Notification Service
   ↓
Database

---


END OF CUSTOMER LAYER SPECIFICATION (Node.js Version)

-------------------------------------------------------------


# 🏪 SHOPKEEPER DASHBOARD (Vendor Layer)

## Technical Product Specification Document (Node.js Version)

---

# 1. 📌 Overview

Shopkeeper dashboard (Web-based Admin Panel) for managing:

* Shop profile
* Products
* Inventory
* Orders
* Earnings
* Analytics

Frontend: React.js (Web Admin Panel)
Backend: Node.js (Express.js – Shared Core API)
Database: PostgreSQL
Cache: Redis

---

# 2. 👤 Shopkeeper Authentication & Roles

## Login Methods

* Phone + OTP
* Email + Password (Optional Phase 2)
* JWT based authentication

## Roles

* shop_owner
* shop_manager (optional future)

---

# 3. 🏬 Shop Profile Management

## shops Table Structure

| Field          | Type       | Notes               |
| -------------- | ---------- | ------------------- |
| id             | UUID       | Primary Key         |
| owner_id       | FK (users) | Shop owner          |
| name           | varchar    | Required            |
| logo_url       | text       |                     |
| banner_url     | text       |                     |
| category_id    | FK         |                     |
| subcategory_id | FK         |                     |
| description    | text       |                     |
| phone          | varchar    |                     |
| address        | text       |                     |
| latitude       | decimal    |                     |
| longitude      | decimal    |                     |
| city_id        | FK         |                     |
| is_verified    | boolean    | Default false       |
| is_active      | boolean    | Controlled by admin |
| created_at     | timestamp  |                     |
| updated_at     | timestamp  |                     |

Features:

* Edit shop details
* Upload logo/banner
* Business timing configuration
* Toggle shop open/closed

---

# 4. 📦 Product Management Engine

## Capabilities

* Add product
* Edit product
* Delete product (soft delete)
* Bulk upload (CSV – Phase 2)
* Stock management

## products Table (Vendor Controlled Fields)

| Field          | Type                                   |
| -------------- | -------------------------------------- |
| id             | UUID                                   |
| shop_id        | FK                                     |
| name           | varchar                                |
| description    | text                                   |
| price          | decimal                                |
| discount_price | decimal                                |
| stock          | integer                                |
| sku            | varchar                                |
| image_url      | text                                   |
| is_featured    | boolean                                |
| status         | varchar (active/inactive/out_of_stock) |
| created_at     | timestamp                              |
| updated_at     | timestamp                              |

Rules:

* Cannot delete product if active orders exist
* Stock auto-reduce on order confirmation
* Out-of-stock products auto hidden (optional)

---

# 5. 🛒 Order Management System

## Shop View of Orders

Shopkeeper can:

* View new orders
* Accept / Reject order
* Update status (accepted → preparing → delivered)
* View customer details
* Cancel order (if not prepared)

## Order Status Flow

pending → accepted → preparing → delivered
pending → rejected

All updates trigger notification to customer.

---

# 6. 💰 Earnings & Commission Engine

Commission is:

* Configurable
* Negotiable per shop
* Controlled by Super Admin

## earnings Table

| Field             | Type      |
| ----------------- | --------- |
| id                | UUID      |
| shop_id           | FK        |
| order_id          | FK        |
| total_amount      | decimal   |
| commission_rate   | decimal   |
| commission_amount | decimal   |
| shop_receivable   | decimal   |
| settlement_status | varchar   |
| created_at        | timestamp |

Settlement Status:

* pending
* settled

---

# 7. 📊 Analytics Dashboard

Metrics:

* Total Orders
* Total Revenue
* Total Commission
* Net Earnings
* Top Selling Products
* Daily / Weekly / Monthly Graph

Future Enhancements:

* AI-based sales insights
* Demand prediction

---

# 8. 🧾 Inventory & Stock Alerts

Features:

* Low stock alert system
* Out of stock auto-hide
* Inventory history log
* Stock update history

---

# 9. 🔔 Notification Engine (Vendor Side)

Triggers:

* New order received
* Order cancelled by customer
* Payment settled
* Admin announcements

---

# 10. 🔐 Security & Access Control

* JWT protected APIs
* Role-based middleware
* Shop-level data isolation
* Rate limiting
* Input validation (Joi/Zod)
* Logging (Winston)

---

# 11. 🌐 Vendor API Structure

GET    /api/v1/vendor/dashboard
POST   /api/v1/vendor/products
PUT    /api/v1/vendor/products/:id
DELETE /api/v1/vendor/products/:id
GET    /api/v1/vendor/orders
PUT    /api/v1/vendor/orders/:id/status
GET    /api/v1/vendor/earnings
PUT    /api/v1/vendor/shop-profile

---

# 12. 🚀 MVP Scope (Vendor Phase 1)

Included:

* Login
* Shop profile management
* Add/edit products
* Accept/reject orders
* Basic earnings view
* Stock management

Excluded (Phase 2):

* Advanced analytics
* Bulk upload
* Multiple staff accounts
* Automated payout system
* Tax invoice generation
* GST reporting module

---

# 13. 🧠 Vendor Core Engines Summary

1. Vendor Authentication Engine
2. Shop Management Engine
3. Product Management Engine
4. Order Management Engine
5. Earnings Engine
6. Analytics Engine
7. Inventory Engine

---

# 14. Flow:

A. Shopkeeper Flow:-

Shopkeeper Login
   ↓
Dashboard Load
   ↓
New Order Notification
   ↓
Accept / Reject Order
   ↓
Update Order Status
   ↓
Delivery Complete
   ↓
Sales Analytics Update


B. Technical Flow:-

Shopkeeper Dashboard
   ↓
Auth Service
   ↓
Order Service
   ↓
Inventory Service
   ↓
Commission Engine
   ↓
Notification Service
   ↓
Database

---

END OF SHOPKEEPER DASHBOARD SPECIFICATION (Node.js Version)

----------------------------------------------------------

# 🛡 SUPER ADMIN DASHBOARD (Control Layer)

## Technical Product Specification Document (Node.js Version)

---

# 1. 📌 Overview

Super Admin Dashboard is the central control system of the entire city-level marketplace platform.

Super Admin can manage:

* All Customers
* All Shopkeepers
* All Shops
* Commission Settings
* Orders Monitoring
* Settlements
* Categories & Subcategories
* City Management
* Reports & Analytics
* Platform Settings

Frontend: React.js (Admin Web Panel)
Backend: Node.js (Express.js – Central API Layer)
Database: PostgreSQL
Cache: Redis

---

# 2. 👤 Admin Authentication & Roles

## Login

* Email + Password
* 2FA (Phase 2)
* JWT Authentication

## Roles

* super_admin (Full access)
* city_admin (Restricted to specific city – Future Phase)
* finance_admin (Settlement & commission access only – Future Phase)

---

# 3. 🏙 City Management Engine

## cities Table

| Field      | Type      |
| ---------- | --------- |
| id         | UUID      |
| name       | varchar   |
| state      | varchar   |
| is_active  | boolean   |
| created_at | timestamp |

Capabilities:

* Add new city
* Activate / deactivate city
* Assign commission rules per city

---

# 4. 🏬 Shop Management Engine

Super Admin Controls:

* View all shops
* Approve / Reject new shop registration
* Activate / Deactivate shop
* Set commission per shop
* View shop performance

## Extended Shop Controls

Additional Fields:

| Field           | Type                                |
| --------------- | ----------------------------------- |
| approval_status | varchar (pending/approved/rejected) |
| commission_rate | decimal                             |
| is_featured     | boolean                             |
| priority_rank   | integer                             |

---

# 5. 👥 Customer Management Engine

Admin can:

* View all users
* Block / Unblock user
* View order history
* Detect fraud patterns

Additional Controls:

| Field          | Type    |
| -------------- | ------- |
| is_blocked     | boolean |
| blocked_reason | text    |

---

# 6. 🛍 Category & Subcategory Management

Admin Controls:

* Create categories
* Create subcategories
* Upload icons
* Set theme colors
* Activate / deactivate
* Arrange display order

---

# 7. 💰 Commission & Revenue Engine

Commission Model:

* Configurable per city
* Configurable per category
* Negotiable per shop

## commission_rules Table

| Field        | Type          |
| ------------ | ------------- |
| id           | UUID          |
| city_id      | FK            |
| category_id  | FK (nullable) |
| default_rate | decimal       |
| created_at   | timestamp     |

Super Admin Can:

* Override commission per shop
* View total commission earned
* Track revenue growth

---

# 8. 📦 Order Monitoring Engine

Admin View:

* All orders across city
* Filter by city
* Filter by shop
* Filter by date
* Filter by status

Capabilities:

* Force cancel order
* Refund trigger (Phase 2)
* View payment logs

---

# 9. 💳 Settlement & Payout Engine

## settlements Table

| Field             | Type                   |
| ----------------- | ---------------------- |
| id                | UUID                   |
| shop_id           | FK                     |
| total_orders      | integer                |
| gross_amount      | decimal                |
| commission_amount | decimal                |
| net_payable       | decimal                |
| settlement_period | varchar                |
| status            | varchar (pending/paid) |
| created_at        | timestamp              |

Admin Controls:

* Generate weekly/monthly settlements
* Mark settlement as paid
* Export settlement report (CSV)

---

# 10. 📊 Platform Analytics Dashboard

Metrics:

* Total Users
* Total Shops
* Active Shops
* Total Orders
* Gross GMV (Gross Merchandise Value)
* Total Commission Earned
* City-wise Performance
* Category-wise Performance

Charts Required:

* Daily orders graph
* Monthly revenue graph
* Shop growth graph

---

# 11. 🔔 Platform Notification Engine

Admin can send:

* Push notification to all users
* Notification to specific city
* Notification to specific category shops
* System announcement banner

---

# 12. ⚙ Platform Settings Engine

Admin configurable settings:

* Default commission rate
* Minimum order amount
* Delivery radius default
* App maintenance mode
* Banner management (Home screen)
* Featured shops section

---

# 13. 🔐 Security & Audit System

Security Features:

* Admin role-based access control
* Activity logs
* Login history
* Audit trail for commission changes
* IP monitoring (optional)

## admin_logs Table

| Field      | Type      |
| ---------- | --------- |
| id         | UUID      |
| admin_id   | FK        |
| action     | text      |
| target_id  | UUID      |
| created_at | timestamp |

---

# 14. 🌐 Admin API Structure

GET    /api/v1/admin/dashboard
GET    /api/v1/admin/shops
PUT    /api/v1/admin/shops/:id/approve
PUT    /api/v1/admin/shops/:id/block
GET    /api/v1/admin/users
PUT    /api/v1/admin/users/:id/block
GET    /api/v1/admin/orders
POST   /api/v1/admin/commission-rule
GET    /api/v1/admin/settlements
POST   /api/v1/admin/settlements/generate
POST   /api/v1/admin/notifications/send
PUT    /api/v1/admin/settings

---

# 15. 🚀 MVP Scope (Admin Phase 1)

Included:

* Admin login
* Shop approval system
* Basic commission setting
* Order monitoring
* Settlement calculation (manual mark paid)
* Category management

Excluded (Phase 2):

* Automated payout integration
* AI fraud detection
* Advanced financial reports
* Multi-admin hierarchy
* Role permission matrix builder

---

# 16. 🧠 Super Admin Core Engines Summary

1. Admin Authentication Engine
2. City Management Engine
3. Shop Approval Engine
4. Commission Engine
5. Order Monitoring Engine
6. Settlement Engine
7. Analytics Engine
8. Notification Engine
9. Platform Settings Engine
10. Audit & Security Engine

---

# 17. Flow:

A.🛠 4️⃣ Super Admin Flow:

Admin Login
   ↓
View Shops
   ↓
Approve / Reject Shops
   ↓
Monitor Sales
   ↓
View Revenue
   ↓
Suspend Shop (if needed)

---

B. Technical Flow:

Admin Panel
   ↓
Admin Auth
   ↓
Shop Management Service
   ↓
User Management Service
   ↓
Revenue Service
   ↓
Audit Logs
   ↓
Database

---

END OF SUPER ADMIN DASHBOARD SPECIFICATION (Node.js Version)

----------------------------------------------------------------

# 📝 SHOP REGISTRATION PANEL (Onboarding Layer)

## Technical Product Specification Document (Node.js Version)

---

# 1. 📌 Overview

Shop Registration Panel is a dedicated onboarding system for new shopkeepers who want to join the city-level marketplace platform.

Purpose:

* Allow shop owners to register online
* Collect business details
* Upload required documents
* Auto-map shop to correct category & city
* Send request for Super Admin approval

Frontend: React.js (Web Panel / Mobile Web Compatible)
Backend: Node.js (Express.js)
Database: PostgreSQL
File Storage: AWS S3 / Cloudinary
Cache: Redis (OTP + temporary session)

---

# 2. 🔐 Registration Flow Structure

Step 1: Mobile Number Verification (OTP)
Step 2: Basic Shop Details
Step 3: Category & Subcategory Selection
Step 4: Business Address & Geo Location
Step 5: Document Upload
Step 6: Bank Details (Optional in MVP)
Step 7: Submit for Approval

After submission:

Status = pending
Super Admin reviews → approved / rejected

---

# 3. 👤 Step 1: Shopkeeper Account Creation

## users Table (role = shop_owner)

| Field      | Type                           |
| ---------- | ------------------------------ |
| id         | UUID                           |
| name       | varchar                        |
| phone      | varchar (unique)               |
| email      | varchar                        |
| role       | varchar (shop_owner)           |
| password   | varchar (nullable if OTP only) |
| is_active  | boolean                        |
| created_at | timestamp                      |

Security:

* OTP stored in Redis (5 min expiry)
* JWT token generated after verification

---

# 4. 🏬 Step 2: Shop Basic Details

## shops (Draft Mode)

| Field            | Type                                      |
| ---------------- | ----------------------------------------- |
| id               | UUID                                      |
| owner_id         | FK                                        |
| name             | varchar                                   |
| description      | text                                      |
| phone            | varchar                                   |
| logo_url         | text                                      |
| banner_url       | text                                      |
| category_id      | FK                                        |
| subcategory_id   | FK                                        |
| city_id          | FK                                        |
| address          | text                                      |
| latitude         | decimal                                   |
| longitude        | decimal                                   |
| approval_status  | varchar (draft/pending/approved/rejected) |
| rejection_reason | text                                      |
| created_at       | timestamp                                 |

Draft Mode Logic:

* Until final submit → status = draft
* After submit → status = pending

---

# 5. 🗂 Category Mapping Engine

Dynamic Behavior:

* Admin-created categories auto load
* Subcategories depend on selected category
* UI auto-theme can be linked to category

Example:

If Category = Medical Store
→ Show subcategories: Medicines, Surgical, Health Devices
→ Medical themed icon & color (optional future enhancement)

---

# 6. 📍 Geo-Location Mapping

Features:

* Google Maps API integration
* Pin location selection
* Auto-detect current location (optional)
* Validate shop within active city boundary

Database Requirement:

* Latitude & Longitude mandatory
* City mapping validation before submit

---

# 7. 📄 Document Upload System

Required Documents (Configurable by Admin):

* GST Certificate (optional MVP)
* Shop License
* Owner ID Proof
* Cancelled Cheque (Phase 2)

## shop_documents Table

| Field               | Type                                |
| ------------------- | ----------------------------------- |
| id                  | UUID                                |
| shop_id             | FK                                  |
| document_type       | varchar                             |
| file_url            | text                                |
| verification_status | varchar (pending/approved/rejected) |
| uploaded_at         | timestamp                           |

File Handling:

* Store in cloud storage
* Secure access via signed URLs

---

# 8. 💳 Bank Details (Optional MVP)

## shop_bank_details

| Field               | Type      |
| ------------------- | --------- |
| id                  | UUID      |
| shop_id             | FK        |
| account_holder_name | varchar   |
| account_number      | varchar   |
| ifsc_code           | varchar   |
| bank_name           | varchar   |
| created_at          | timestamp |

Encrypted storage required.

---

# 9. 📩 Approval Workflow Engine

After submission:

1. Admin gets notification
2. Admin reviews documents
3. Admin verifies category & location
4. Admin sets commission rate
5. Admin clicks approve / reject

If Approved:

* Shop becomes active
* Shopkeeper dashboard enabled
* Shop visible in User App

If Rejected:

* Reason stored
* Shopkeeper notified
* Can re-edit & resubmit

---

# 10. 🔔 Notification Flow

Shopkeeper receives notification when:

* Registration submitted
* Approved
* Rejected
* Document rejected

Admin receives notification when:

* New registration submitted

---

# 11. 🌐 API Structure

POST   /api/v1/shop/register/start
POST   /api/v1/shop/register/verify-otp
POST   /api/v1/shop/register/save-draft
POST   /api/v1/shop/register/upload-document
POST   /api/v1/shop/register/submit
GET    /api/v1/shop/register/status

Admin Side:

GET    /api/v1/admin/shop-registrations
PUT    /api/v1/admin/shop-registrations/:id/approve
PUT    /api/v1/admin/shop-registrations/:id/reject

---

# 12. 🚀 MVP Scope (Registration Phase 1)

Included:

* OTP verification
* Basic shop details
* Category selection
* Location mapping
* Document upload
* Admin approval system

Excluded (Phase 2):

* Auto document verification
* Video KYC
* AI fraud detection
* Automatic GST verification API

---

# 13. 🧠 Core Engines Summary

1. OTP Verification Engine
2. Draft Management Engine
3. Category Mapping Engine
4. Geo-Validation Engine
5. Document Management Engine
6. Approval Workflow Engine
7. Notification Engine

# 14. Flow:



---

END OF SHOP REGISTRATION PANEL SPECIFICATION (Node.js Version)

-----------------------------------------------------------------------


# 🗄 MASTER DATABASE ER STRUCTURE

## City-Level Multi-Vendor Marketplace Platform

(Node.js + PostgreSQL Based Architecture)

---

# 1. 📌 Overview

This document defines the complete database-level structure for the entire marketplace platform including:

* Customer Layer
* Shopkeeper Layer
* Super Admin Layer
* Registration Layer
* Commission & Settlement Engine

All IDs use UUID.
All monetary fields use DECIMAL.
All timestamps use TIMESTAMP.

---

# 2. 🧩 CORE ENTITY LIST

Main Entities:

1. users
2. cities
3. shops
4. categories
5. subcategories
6. products
7. carts
8. cart_items
9. orders
10. order_items
11. earnings
12. settlements
13. commission_rules
14. addresses
15. shop_documents
16. shop_bank_details
17. admin_logs
18. notifications

---

# 3. 👤 USERS ENTITY

users

| Field      | Type             | Description                   |
| ---------- | ---------------- | ----------------------------- |
| id         | UUID             | Primary Key                   |
| name       | varchar          |                               |
| phone      | varchar (unique) |                               |
| email      | varchar          |                               |
| password   | varchar          | nullable                      |
| role       | varchar          | customer / shop_owner / admin |
| city_id    | FK (cities)      |                               |
| is_active  | boolean          |                               |
| is_blocked | boolean          |                               |
| created_at | timestamp        |                               |

RELATIONSHIPS:

users (1) → (M) addresses
users (1) → (M) orders
users (1) → (1) shop (if role = shop_owner)

---

# 4. 🏙 CITIES ENTITY

cities

| Field      | Type      |
| ---------- | --------- |
| id         | UUID      |
| name       | varchar   |
| state      | varchar   |
| is_active  | boolean   |
| created_at | timestamp |

RELATIONSHIPS:

cities (1) → (M) shops
cities (1) → (M) users
cities (1) → (M) commission_rules

---

# 5. 🏬 SHOPS ENTITY

shops

| Field           | Type        |
| --------------- | ----------- |
| id              | UUID        |
| owner_id        | FK (users)  |
| city_id         | FK (cities) |
| name            | varchar     |
| description     | text        |
| category_id     | FK          |
| subcategory_id  | FK          |
| latitude        | decimal     |
| longitude       | decimal     |
| approval_status | varchar     |
| commission_rate | decimal     |
| is_active       | boolean     |
| created_at      | timestamp   |

RELATIONSHIPS:

shops (1) → (M) products
shops (1) → (M) orders
shops (1) → (M) earnings
shops (1) → (M) settlements
shops (1) → (M) shop_documents
shops (1) → (1) shop_bank_details

---

# 6. 🗂 CATEGORY STRUCTURE

categories

| id | name | icon | theme_color | is_active |

subcategories

| id | category_id (FK) | name | is_active |

RELATIONSHIPS:

categories (1) → (M) subcategories
categories (1) → (M) shops
subcategories (1) → (M) shops

---

# 7. 🛍 PRODUCTS ENTITY

products

| Field          | Type      |
| -------------- | --------- |
| id             | UUID      |
| shop_id        | FK        |
| name           | varchar   |
| description    | text      |
| price          | decimal   |
| discount_price | decimal   |
| stock          | integer   |
| status         | varchar   |
| created_at     | timestamp |

RELATIONSHIPS:

products (1) → (M) cart_items
products (1) → (M) order_items

---

# 8. 🛒 CART STRUCTURE

carts

| id | user_id (FK) | shop_id (FK) | created_at |

cart_items

| id | cart_id (FK) | product_id (FK) | quantity |

RELATIONSHIPS:

users (1) → (M) carts
shops (1) → (M) carts
carts (1) → (M) cart_items

---

# 9. 📦 ORDER STRUCTURE

orders

| Field             | Type      |
| ----------------- | --------- |
| id                | UUID      |
| user_id           | FK        |
| shop_id           | FK        |
| total_amount      | decimal   |
| commission_amount | decimal   |
| final_amount      | decimal   |
| status            | varchar   |
| payment_mode      | varchar   |
| created_at        | timestamp |

order_items

| id | order_id (FK) | product_id (FK) | quantity | price |

RELATIONSHIPS:

users (1) → (M) orders
shops (1) → (M) orders
orders (1) → (M) order_items

---

# 10. 💰 COMMISSION & EARNINGS

earnings

| id | shop_id (FK) | order_id (FK) | commission_rate | commission_amount | shop_receivable | created_at |

commission_rules

| id | city_id (FK) | category_id (FK nullable) | default_rate | created_at |

RELATIONSHIPS:

orders (1) → (1) earnings
cities (1) → (M) commission_rules

---

# 11. 💳 SETTLEMENT SYSTEM

settlements

| id | shop_id (FK) | gross_amount | commission_amount | net_payable | period | status | created_at |

RELATIONSHIPS:

shops (1) → (M) settlements

---

# 12. 📍 ADDRESS SYSTEM

addresses

| id | user_id (FK) | address | latitude | longitude | is_default |

RELATIONSHIPS:

users (1) → (M) addresses

---

# 13. 📄 DOCUMENT SYSTEM

shop_documents

| id | shop_id (FK) | document_type | file_url | verification_status | uploaded_at |

shop_bank_details

| id | shop_id (FK) | account_number | ifsc_code | bank_name | created_at |

---

# 14. 🔔 NOTIFICATION SYSTEM

notifications

| id | user_id (FK nullable) | shop_id (FK nullable) | title | message | is_read | created_at |

---

# 15. 🔐 ADMIN LOG SYSTEM

admin_logs

| id | admin_id (FK users) | action | target_id | created_at |

---

# 16. 🔄 HIGH LEVEL RELATION FLOW

Customer → Orders → Shop → Earnings → Settlement

City → Shops → Products → Orders

Admin → Commission Rules → Earnings → Reports

---

# 17. 🧠 DATABASE DESIGN PRINCIPLES

* Use Foreign Key constraints
* Add indexes on all FK fields
* Add index on phone, city_id, shop_id
* Use transactions for order creation
* Soft delete where needed (is_active flag)
* Avoid redundant data storage
* All monetary values in DECIMAL
* Use UUID for distributed scalability

---

END OF MASTER DATABASE ER STRUCTURE D

-----------------------------------------------------

# 🔄 END‑TO‑END SYSTEM FLOW DIAGRAM

## Technical Architecture Level – City Marketplace Platform

(Node.js + PostgreSQL + Redis + React Native + React.js)

---

# 1️⃣ HIGH LEVEL ARCHITECTURE FLOW

```
Customer App (React Native)
        ↓
API Gateway (Express.js)
        ↓
Core Services Layer (Node.js)
        ↓
PostgreSQL Database
        ↓
Redis Cache Layer
        ↓
Cloud Storage (Images/Documents)
        ↓
Admin & Vendor Dashboards (React.js)
```

System is Stateless at API Layer.
All authentication via JWT.

---

# 2️⃣ COMPLETE USER → SHOP → ADMIN FLOW

## 🔹 A. Customer Browsing Flow

1. Customer opens app
2. App sends request → /shops?lat=xx&long=xx
3. Backend:

   * Validates JWT
   * Fetches nearby shops (Geo query)
   * Uses Redis cache (if available)
4. Response returned → Shop List
5. Customer opens shop → products loaded
6. Products cached (short duration)

Flow Summary:

Customer → API → Geo Filter → DB → Cache → Response

---

## 🔹 B. Add to Cart Flow

1. Customer clicks Add to Cart
2. API validates:

   * Stock availability
   * Single shop rule (MVP)
3. Cart created/updated
4. Response returned with updated cart

Flow:

Customer → Cart API → DB Transaction → Cart Updated

---

## 🔹 C. Order Placement Flow (Critical Transaction)

1. Customer clicks Place Order
2. Backend:

   * Begin DB Transaction
   * Validate stock
   * Calculate commission (dynamic rule)
   * Create order
   * Create order_items
   * Reduce stock
   * Create earnings entry
   * Commit transaction
3. Send notification to shopkeeper
4. Clear cart

Flow:

Customer → Order API → Transaction Engine → Orders + Earnings → Notification Service

---

## 🔹 D. Shopkeeper Order Handling Flow

1. Shopkeeper receives notification
2. Shopkeeper dashboard fetches /vendor/orders
3. Shopkeeper accepts/rejects order
4. Order status updated
5. Customer notified via FCM

Flow:

Vendor Dashboard → API → Update Order Status → Notify Customer

---

## 🔹 E. Commission & Earnings Flow

At Order Creation:

commission_amount = total × commission_rate
shop_receivable = total − commission

Stored in earnings table.

Later during settlement:

Admin generates settlement →

* Sum gross orders
* Sum commission
* Calculate net payable
* Create settlement entry

Flow:

Orders → Earnings → Settlement Engine → Admin Dashboard

---

# 3️⃣ SHOP REGISTRATION FLOW

1. Shopkeeper verifies mobile (OTP via Redis)
2. Fills shop details
3. Uploads documents (Cloud Storage)
4. Submits for approval
5. Admin reviews
6. If approved:

   * Shop status = active
   * Vendor dashboard enabled
   * Shop visible in User App

Flow:

Shop Registration Panel → API → DB (Draft) → Admin Approval → Activate Shop

---

# 4️⃣ ADMIN CONTROL FLOW

Admin Dashboard connects to same API layer.

Admin can:

* Approve shops
* Block users
* Change commission rules
* Generate settlements
* Send global notifications

Flow:

Admin Dashboard → API → DB Update → Notification Engine

All admin actions logged in admin_logs.

---

# 5️⃣ NOTIFICATION FLOW (FCM Based)

Trigger Events:

* Order placed
* Order accepted
* Order rejected
* Order delivered
* Shop approved

Flow:

Event Occurs → Notification Service → FCM → Target Device

Notification stored in notifications table.

---

# 6️⃣ DATA FLOW SEGMENTATION

## Layer Separation

Presentation Layer:

* React Native App
* React.js Dashboards

Application Layer:

* Express API
* Business Logic Services

Data Layer:

* PostgreSQL
* Redis
* Cloud Storage

Infrastructure Layer:

* Load Balancer
* Docker Containers
* Nginx
* SSL

---

# 7️⃣ PRODUCTION SCALING FLOW

Future Microservice Split:

Auth Service
Order Service
Commission Service
Notification Service
Settlement Service

Each service:

* Separate DB schema (optional future)
* Communicate via REST or Message Queue

---

# 8️⃣ ERROR HANDLING FLOW

If Order Transaction Fails:

* Rollback transaction
* Log error
* Return proper API response

If Payment fails (future phase):

* Mark order failed
* Do not reduce stock

---

# 9️⃣ SECURITY FLOW

Every request:

1. JWT validation middleware
2. Role check middleware
3. Input validation
4. Controller execution
5. Service layer logic
6. DB interaction

Admin actions additionally logged.

---

# 🔟 COMPLETE PLATFORM FLOW SUMMARY

Customer Layer → Order → Shop Layer → Earnings → Settlement → Admin Analytics

Shop Registration → Approval → Activation → Visible in Customer App

Commission Rules → Applied at Order Level → Aggregated at Settlement Level

---

# 🧠 FINAL SYSTEM FLOW MODEL

The platform works as a centralized marketplace engine where:

* Customer generates demand
* Shop fulfills demand
* Platform earns commission
* Admin controls rules
* Settlement engine distributes revenue

System is:

* Stateless
* Scalable
* Multi-city ready
* Commission-configurable
* Role-based secure

---

END OF END‑TO‑END SYSTEM FLOW ARCHITECTURE DOCUMENT

-----------------------------------------------------------------------

# 🗺 DEPLOYMENT ARCHITECTURE DOCUMENT

## City-Level Marketplace Platform

(Node.js + PostgreSQL + Redis + React Native + React.js)

---

# 1️⃣ DEPLOYMENT OVERVIEW

This document defines production-ready deployment architecture for:

* User App Backend APIs
* Shopkeeper Dashboard
* Super Admin Dashboard
* Shop Registration Panel
* Database Layer
* Caching Layer
* Storage Layer

Deployment Options Covered:

1. VPS Based Deployment (Budget Phase)
2. AWS Cloud Deployment (Scalable Phase)
3. Docker Container Architecture

---

# 2️⃣ OPTION 1 – VPS DEPLOYMENT (STARTUP PHASE)

Recommended For:

* Single city launch
* Low traffic (< 10k users)
* Budget-friendly deployment

## Server Requirements

Minimum:

* 8 GB RAM
* 4 vCPU
* 150 GB SSD
* Ubuntu 22.04 LTS

## VPS Architecture

```
Internet
   ↓
Nginx (Reverse Proxy + SSL)
   ↓
Node.js API (PM2 Managed)
   ↓
PostgreSQL (Same Server or Separate VPS)
   ↓
Redis (Same Server)
```

## Components Setup

* Nginx → SSL (Let's Encrypt)
* Node.js → PM2 process manager
* PostgreSQL → Local install
* Redis → Local install
* File uploads → AWS S3 (Recommended even in VPS phase)

## Pros

* Low cost
* Simple to manage
* Fast launch

## Cons

* Limited scalability
* Single point of failure

---

# 3️⃣ OPTION 2 – AWS PRODUCTION DEPLOYMENT (SCALABLE)

Recommended For:

* Multi-city expansion
* High traffic
* Investor-ready architecture

## AWS Architecture Diagram

```
Users
   ↓
CloudFront (CDN)
   ↓
Application Load Balancer
   ↓
EC2 Auto Scaling Group
   ↓
Node.js API Containers (Docker)
   ↓
RDS PostgreSQL
   ↓
ElastiCache (Redis)
   ↓
S3 (Images & Documents)
```

## Core AWS Services

1. EC2 → Application Servers
2. RDS → Managed PostgreSQL
3. ElastiCache → Redis
4. S3 → File Storage
5. CloudFront → CDN
6. ALB → Load Balancer
7. IAM → Access control
8. Route53 → Domain management
9. CloudWatch → Monitoring

---

# 4️⃣ DOCKER CONTAINER ARCHITECTURE

All services containerized.

## Dockerized Services

* api-service
* nginx-service
* postgres-service
* redis-service

## docker-compose.yml (Conceptual Structure)

* API container connects to:

  * PostgreSQL container
  * Redis container
* Nginx acts as reverse proxy

## Benefits

* Environment consistency
* Easy scaling
* CI/CD ready
* Production portability

---

# 5️⃣ CI/CD PIPELINE STRUCTURE

Recommended Tools:

* GitHub Actions
* Docker Hub / AWS ECR
* Auto deploy to EC2

Pipeline Flow:

1. Developer pushes code
2. CI runs tests
3. Docker image built
4. Image pushed to registry
5. Deployment triggered
6. New containers rolled out

---

# 6️⃣ DATABASE DEPLOYMENT STRATEGY

## VPS Phase

* PostgreSQL on same server
* Daily backup via cron
* Manual monitoring

## AWS Phase

* RDS PostgreSQL (Multi-AZ enabled)
* Automated backups
* Read replicas (future scaling)

---

# 7️⃣ SCALING STRATEGY

Horizontal Scaling:

* Add more EC2 instances
* Auto Scaling based on CPU usage

Vertical Scaling:

* Increase server RAM/CPU

Database Scaling:

* Read replicas
* Query optimization
* Index tuning

---

# 8️⃣ SECURITY ARCHITECTURE

## Network Security

* Security Groups
* Private DB subnet
* Public API subnet

## Application Security

* HTTPS only
* JWT authentication
* Rate limiting
* Helmet middleware

## Backup Strategy

* Daily DB backup
* Weekly snapshot
* S3 versioning enabled

---

# 9️⃣ MONITORING & LOGGING

Tools:

* PM2 logs (VPS phase)
* AWS CloudWatch (Cloud phase)
* Winston logger in app

Monitoring Metrics:

* CPU usage
* Memory usage
* API response time
* DB slow queries
* Failed login attempts

---

# 🔟 COST ESTIMATION (APPROX)

## VPS Phase

* VPS: ₹4,000 – ₹8,000/month
* S3 Storage: ₹500 – ₹1,000/month

Total Estimated: ₹5,000 – ₹10,000/month

## AWS Phase (Scalable Setup)

* EC2: ₹8,000 – ₹15,000/month
* RDS: ₹6,000 – ₹12,000/month
* Redis: ₹3,000 – ₹6,000/month
* S3 + CloudFront: ₹2,000 – ₹5,000/month

Total Estimated: ₹20,000 – ₹40,000/month

---

# 🧠 FINAL DEPLOYMENT STRATEGY RECOMMENDATION

Phase 1:

* Launch on VPS
* Dockerized setup
* S3 for storage

Phase 2:

* Migrate to AWS
* Enable Auto Scaling
* Separate services

Phase 3:

* Microservice architecture
* Message queue (RabbitMQ / Kafka)
* Multi-region deployment

---

END OF DEPLOYMENT ARCHITECTURE DOCUMENT


---------------------------------------------------------------------

📘 COMPLETE SYSTEM FLOW DOCUMENTATION
City-Level Multi-Vendor Commerce Platform
1️⃣ SYSTEM OVERVIEW
1.1 Purpose

Ye document platform ke complete end-to-end system flow ko define karta hai jisme:

Customer App

Shopkeeper Dashboard

Super Admin Panel

Shop Registration Panel

Backend Services

Database Layer

Sab ek structured architecture me connected hain.

2️⃣ HIGH LEVEL ARCHITECTURE FLOW
Customer App
    ↓
API Gateway / Load Balancer
    ↓
Authentication Layer
    ↓
Microservices Layer
    ↓
Database Layer
    ↓
Dashboards (Admin + Shopkeeper)
3️⃣ CUSTOMER FLOW (END TO END)
3.1 User Journey Flow

App Open

Location Permission

Nearby Shops Fetch

Shop Listing

Product View

Add to Cart

Checkout

Payment / COD

Order Created

Shopkeeper Notification

Order Delivered

3.2 Backend Flow
Customer App
   ↓
API Gateway
   ↓
Auth Service
   ↓
Shop Service
   ↓
Product Service
   ↓
Cart Service
   ↓
Order Service
   ↓
Payment Service
   ↓
Notification Service
   ↓
Database
4️⃣ SHOPKEEPER FLOW
4.1 Shopkeeper Operational Flow

Login

Dashboard Load

New Order Notification

Accept / Reject Order

Update Order Status

Mark Delivered

Sales Updated

4.2 Backend Interaction
Shopkeeper Dashboard
   ↓
Auth Service
   ↓
Order Service
   ↓
Inventory Service
   ↓
Commission Engine
   ↓
Notification Service
   ↓
Database
5️⃣ SUPER ADMIN FLOW
5.1 Admin Controls

View Shops

Approve / Reject Shops

Monitor Sales

View Platform Revenue

Suspend Shop

Manage Categories

Manage Cities

5.2 Backend Flow
Admin Panel
   ↓
Admin Auth
   ↓
Shop Management Service
   ↓
User Management Service
   ↓
Revenue Service
   ↓
Audit Logs
   ↓
Database
6️⃣ SHOP REGISTRATION → UI LINK FLOW
6.1 Registration Flow

Shopkeeper fills registration form

Select category

Upload logo & banner

Submit documents

Admin verification

Approval

Auto Shop URL Generate

Auto UI Page Creation

Shop visible in customer app

6.2 Dynamic UI Logic

If category = Medical:

Prescription upload enabled

Medicine categories visible

If category = Grocery:

Weight-based pricing enabled

Daily offers section active

If category = Electronics:

Warranty field enabled

Product specs enabled

7️⃣ LOCATION BASED SYSTEM FLOW

User location fetch

Send coordinates to backend

Geo query (PostGIS)

Fetch shops within X KM

Sort by:

Distance

Rating

Popularity

Return results

8️⃣ PAYMENT FLOW
8.1 Online Payment

Order created (Pending)

Redirect to payment gateway

Payment success

Order status updated = Paid

Shopkeeper notified

8.2 COD Flow

Order created

Status = COD Pending

Shopkeeper notified

9️⃣ DATABASE STRUCTURE INTERACTION
Core Tables

Users

Shops

Products

Categories

Orders

Order_Items

Payments

Transactions

Reviews

Notifications

Commission

Relationships

User → Orders

Shop → Products

Shop → Orders

Order → Order_Items

Order → Payment

Shop → Commission

🔟 REAL-TIME EVENT FLOW
WebSocket / Real-time Updates

Event: New Order Created
↓
Backend event trigger
↓
WebSocket emit
↓
Shopkeeper dashboard instant notification

1️⃣1️⃣ AUTHENTICATION FLOW

Login

JWT token generate

Token stored securely

Every API call token verified

Roles:

Customer

Shopkeeper

Admin

1️⃣2️⃣ SCALABILITY STRUCTURE (MULTI CITY READY)

Add:

City Table

City ID in shops

City filter in API

Subdomain system

Example:

varanasi.platform.com
lucknow.platform.com
1️⃣3️⃣ MICROSERVICE READY STRUCTURE

Services:

Auth Service

Shop Service

Product Service

Cart Service

Order Service

Payment Service

Notification Service

Admin Service

Each deployable independently.

1️⃣4️⃣ SYSTEM CORE PRINCIPLE

Customer → Generates Demand
Shop → Fulfills Order
Platform → Manages Flow
Admin → Controls System

1️⃣5️⃣ FUTURE ENHANCEMENT PLACEHOLDER (Editable Section)

Delivery Partner Module

Subscription Model

Ads Placement for Shops

Loyalty Points

Wallet System

AI Recommendation Engine

Multi-language Support

---------------------------------------------------------------------








