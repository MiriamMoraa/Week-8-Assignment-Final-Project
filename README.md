E-commerce Database Schema - Question 1
A comprehensive relational database design for an e-commerce store management system using MySQL.
📋 Assignment Overview
Objective: Design and implement a full-featured relational database using MySQL for a real-world e-commerce use case.
Use Case: Online E-commerce Store Management System
🏗️ Database Architecture
Core Entities
1.	Categories - Product categorization
2.	Products - Store inventory items
3.	Customers - User accounts and profiles
4.	Orders - Purchase transactions
5.	Order Items - Individual products within orders
6.	Suppliers - Product suppliers and vendors
7.	Product Suppliers - Supplier-product relationships
8.	Customer Reviews - Product ratings and feedback
🔗 Relationship Design
One-to-Many Relationships
•	Categories → Products: One category can have many products
•	Customers → Orders: One customer can place many orders
•	Orders → Order Items: One order can contain many items
•	Products → Order Items: One product can appear in many orders
•	Customers → Reviews: One customer can write many reviews
•	Products → Reviews: One product can receive many reviews
Many-to-Many Relationships
•	Products ↔ Suppliers: Products can have multiple suppliers, suppliers can supply multiple products
•	Orders ↔ Products: Resolved through order_items junction table
Key Constraints Implemented
•	PRIMARY KEY: Unique identifier for each table
•	FOREIGN KEY: Maintains referential integrity
•	NOT NULL: Ensures required fields are populated
•	UNIQUE: Prevents duplicate entries (emails, SKUs)
•	CHECK: Validates data ranges (prices ≥ 0, ratings 1-5)
•	ENUM: Restricts values to predefined options
📊 Database Schema Details
Tables Overview
Table	Purpose	Key Relationships
categories	Product categories	Referenced by products
customers	Customer information	References orders, reviews
products	Product catalog	References categories, suppliers
orders	Purchase orders	References customers
order_items	Order line items	References orders, products
suppliers	Supplier information	Referenced by product_suppliers
product_suppliers	Product-supplier mapping	References products, suppliers
customer_reviews	Product reviews	References customers, products
Key Features
Data Integrity
•	Foreign key constraints with appropriate CASCADE/RESTRICT actions
•	Check constraints for business rules (positive prices, valid ratings)
•	Unique constraints preventing duplicate critical data
Performance Optimization
•	Strategic indexes on frequently queried columns
•	Composite indexes for multi-column searches
•	Primary keys for efficient row identification
Business Logic
•	Soft deletes using is_active flags
•	Automatic timestamps for audit trails
•	Enumerated values for status fields
•	Default values for common fields
🚀 Setup Instructions
Prerequisites
•	MySQL Server 8.0 or higher
•	MySQL client or GUI tool (MySQL Workbench, phpMyAdmin, etc.)
•	Administrative privileges on MySQL server
Installation Steps
1.	Connect to MySQL
2.	mysql -u root -p
3.	Execute the Schema
4.	-- Option 1: Copy and paste the entire schema from ecommerce_schema.sql
5.	-- Option 2: Source the file if saved locally
6.	source /path/to/ecommerce_schema.sql
7.	Verify Installation
8.	USE ecommerce_store;
9.	SHOW TABLES;
10.	DESCRIBE products;  -- Check table structure
11.	SELECT COUNT(*) FROM products;  -- Verify sample data
Sample Data Included
The schema includes sample data for testing:
•	5 product categories
•	4 customers with Kenyan addresses
•	3 suppliers
•	6 products across different categories
•	4 orders with order items
•	5 customer reviews
📋 Database Tables Detailed
1. Categories
- category_id (PK, AUTO_INCREMENT)
- category_name (UNIQUE, NOT NULL)
- description (TEXT)
- created_at, updated_at (TIMESTAMPS)
2. Customers
- customer_id (PK, AUTO_INCREMENT)
- first_name, last_name (NOT NULL)
- email (UNIQUE, NOT NULL)
- phone, address, city, postal_code
- country (DEFAULT 'Kenya')
- date_joined (TIMESTAMP)
- is_active (BOOLEAN, DEFAULT TRUE)
3. Products
- product_id (PK, AUTO_INCREMENT)
- product_name (NOT NULL)
- description (TEXT)
- price (DECIMAL(10,2), CHECK ≥ 0)
- stock_quantity (INT, CHECK ≥ 0)
- category_id (FK to categories)
- sku (UNIQUE)
- is_active (BOOLEAN, DEFAULT TRUE)
- created_at, updated_at (TIMESTAMPS)
4. Orders
- order_id (PK, AUTO_INCREMENT)
- customer_id (FK to customers)
- order_date (TIMESTAMP)
- total_amount (DECIMAL(10,2), CHECK ≥ 0)
- order_status (ENUM: pending, processing, shipped, delivered, cancelled)
- shipping_address (NOT NULL)
- payment_method (ENUM: credit_card, debit_card, paypal, bank_transfer, cash_on_delivery)
- payment_status (ENUM: pending, completed, failed, refunded)
5. Order Items
- order_item_id (PK, AUTO_INCREMENT)
- order_id (FK to orders, CASCADE DELETE)
- product_id (FK to products, RESTRICT DELETE)
- quantity (INT, CHECK > 0)
- unit_price, subtotal (DECIMAL(10,2), CHECK ≥ 0)
- UNIQUE constraint on (order_id, product_id)
🔍 Key Design Decisions
1. Normalization
•	Database follows 3NF (Third Normal Form)
•	Eliminates data redundancy
•	Ensures data consistency
2. Referential Integrity
•	CASCADE DELETE for dependent records (order_items when order deleted)
•	RESTRICT DELETE for referenced entities (prevent deleting categories with products)
3. Soft Deletes
•	Uses is_active flags instead of hard deletes
•	Preserves data for reporting and audit purposes
•	Maintains referential integrity
4. Indexing Strategy
•	Primary keys for unique identification
•	Foreign keys for join performance
•	Business-specific indexes (email, price, date ranges)
🧪 Testing Queries
Basic CRUD Operations
-- Create
INSERT INTO products (product_name, price, stock_quantity, category_id, sku)
VALUES ('New Product', 1500.00, 100, 1, 'NEW-001');

-- Read
SELECT p.product_name, p.price, c.category_name 
FROM products p 
JOIN categories c ON p.category_id = c.category_id;

-- Update
UPDATE products SET price = 1600.00 WHERE product_id = 1;

-- Delete (Soft)
UPDATE products SET is_active = FALSE WHERE product_id = 1;
Complex Queries
-- Customer order history with totals
SELECT c.first_name, c.last_name, o.order_id, o.total_amount, o.order_status
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
ORDER BY o.order_date DESC;

-- Products with average ratings
SELECT p.product_name, AVG(r.rating) as avg_rating, COUNT(r.review_id) as review_count
FROM products p
LEFT JOIN customer_reviews r ON p.product_id = r.product_id
GROUP BY p.product_id;

-- Top selling products
SELECT p.product_name, SUM(oi.quantity) as total_sold, SUM(oi.subtotal) as revenue
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
JOIN orders o ON oi.order_id = o.order_id
WHERE o.order_status = 'delivered'
GROUP BY p.product_id
ORDER BY total_sold DESC;
🔧 Maintenance Commands
Database Backup
mysqldump -u root -p ecommerce_store > backup_ecommerce.sql
Database Restore
mysql -u root -p ecommerce_store < backup_ecommerce.sql
Performance Analysis
SHOW INDEX FROM products;
EXPLAIN SELECT * FROM products WHERE category_id = 1;
📈 Future Enhancements
Potential schema extensions:
•	Shopping cart functionality
•	Product variants (size, color)
•	Discount and coupon system
•	Shipping methods and rates
•	Payment transaction history
•	Product images and media
•	Inventory tracking and alerts
🎯 Assignment Deliverables Met
✅ Complete Database Schema: All tables with proper structure
✅ Primary Keys: Every table has appropriate primary key
✅ Foreign Keys: All relationships properly defined with constraints
✅ NOT NULL Constraints: Critical fields marked as required
✅ UNIQUE Constraints: Email, SKU, and other unique fields protected
✅ Relationships: One-to-One, One-to-Many, Many-to-Many implemented
✅ Sample Data: Realistic test data for all tables
✅ Single SQL File: Complete schema in ecommerce_schema.sql
This database schema provides a solid foundation for a real-world e-commerce application with proper normalization, constraints, and relationships.

