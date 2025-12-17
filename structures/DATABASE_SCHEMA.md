# Mawsim Database Schema Documentation

## Database Overview
- **Database Name**: mawsim_db
- **DBMS**: MariaDB 10.x
- **Character Set**: utf8mb4
- **Collation**: utf8mb4_unicode_ci
- **Engine**: InnoDB (supports foreign keys and transactions)

---

## Complete Database Schema

### 1. Users Table
Central table for all system users (Merchants, Admins, Employees)

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_id INT DEFAULT NULL,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role ENUM('merchant', 'admin', 'employee') NOT NULL DEFAULT 'merchant',
    phone VARCHAR(20),
    is_active BOOLEAN DEFAULT TRUE,
    email_verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    last_login TIMESTAMP NULL,
    
    INDEX idx_email (email),
    INDEX idx_role (role),
    INDEX idx_shop_id (shop_id),
    FOREIGN KEY (shop_id) REFERENCES shops(shop_id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `user_id`: Primary key, auto-increment
- `shop_id`: Foreign key to shops table (NULL for admin users)
- `name`: User's full name (REQ-001, REQ-013)
- `email`: Unique email for login (REQ-002)
- `password`: Hashed password (Bcrypt/Argon2)
- `role`: User type - merchant, admin, or employee
- `is_active`: Account status flag
- `email_verified_at`: Email verification timestamp
- `last_login`: Last successful login timestamp

---

### 2. Shops Table
Store information for each merchant's business

```sql
CREATE TABLE shops (
    shop_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_name VARCHAR(100) NOT NULL,
    business_type ENUM('supermarket', 'hypermarket', 'grocery', 'other') DEFAULT 'supermarket',
    location VARCHAR(100),
    city VARCHAR(50),
    region VARCHAR(50),
    phone VARCHAR(20),
    email VARCHAR(100),
    license_number VARCHAR(50),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_city (city),
    INDEX idx_region (region)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `shop_id`: Primary key
- `shop_name`: Business name (REQ-001)
- `business_type`: Type of retail business
- `location`: Full address
- `city`: City name (for regional analysis)
- `region`: Province/Region (e.g., Makkah, Riyadh)
- `license_number`: Commercial registration number

---

### 3. Products Table
Product catalog for each shop

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_id INT NOT NULL,
    product_name VARCHAR(90) NOT NULL,
    category VARCHAR(50),
    subcategory VARCHAR(50),
    barcode VARCHAR(50) UNIQUE,
    sku VARCHAR(50),
    description TEXT,
    price DECIMAL(10,2) NOT NULL DEFAULT 0.00,
    cost_price DECIMAL(10,2),
    unit VARCHAR(20) DEFAULT 'piece',
    brand VARCHAR(50),
    supplier VARCHAR(100),
    image_url VARCHAR(255),
    seasonal_tag VARCHAR(50),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_shop_id (shop_id),
    INDEX idx_category (category),
    INDEX idx_seasonal_tag (seasonal_tag),
    INDEX idx_product_name (product_name),
    FULLTEXT idx_search (product_name, description),
    FOREIGN KEY (shop_id) REFERENCES shops(shop_id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `product_id`: Primary key (REQ-005)
- `shop_id`: Foreign key to shops
- `product_name`: Product name (REQ-005, REQ-011)
- `category`: Product category (REQ-012)
- `barcode`: Unique product barcode
- `price`: Selling price (REQ-006)
- `seasonal_tag`: Season association (e.g., Ramadan, Eid)
- `FULLTEXT idx_search`: For fast product search (REQ-011)

---

### 4. Inventory Table
Real-time stock levels

```sql
CREATE TABLE inventory (
    inventory_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL DEFAULT 0,
    reorder_level INT DEFAULT 10,
    max_stock_level INT,
    warehouse_location VARCHAR(50),
    last_restock_date DATE,
    next_restock_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    UNIQUE KEY uk_shop_product (shop_id, product_id),
    INDEX idx_quantity (quantity),
    FOREIGN KEY (shop_id) REFERENCES shops(shop_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `quantity`: Current stock level (REQ-010)
- `reorder_level`: Minimum stock trigger
- `max_stock_level`: Maximum warehouse capacity
- `last_restock_date`: Last restocking date
- `next_restock_date`: Planned restock date

---

### 5. Sales Table
Historical sales transactions

```sql
CREATE TABLE sales (
    sale_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity_sold INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_price DECIMAL(10,2) NOT NULL,
    discount DECIMAL(10,2) DEFAULT 0.00,
    sale_date DATE NOT NULL,
    sale_datetime TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    payment_method ENUM('cash', 'card', 'digital_wallet', 'other') DEFAULT 'cash',
    transaction_id VARCHAR(100),
    cashier_id INT,
    
    INDEX idx_shop_id (shop_id),
    INDEX idx_product_id (product_id),
    INDEX idx_sale_date (sale_date),
    INDEX idx_sale_datetime (sale_datetime),
    INDEX idx_composite (shop_id, product_id, sale_date),
    FOREIGN KEY (shop_id) REFERENCES shops(shop_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `sale_id`: Primary key
- `quantity_sold`: Units sold
- `unit_price`: Price per unit at time of sale
- `total_price`: Total transaction amount
- `sale_date`: Date of sale (for daily aggregation)
- `sale_datetime`: Exact timestamp (for hourly analysis)
- `idx_composite`: Composite index for fast analytics queries

---

### 6. Seasons Table
Seasonal events and periods

```sql
CREATE TABLE seasons (
    season_id INT PRIMARY KEY AUTO_INCREMENT,
    season_name VARCHAR(50) NOT NULL,
    hijri_month VARCHAR(50),
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    year INT NOT NULL,
    season_type ENUM('religious', 'holiday', 'school', 'weather', 'cultural') DEFAULT 'religious',
    description TEXT,
    is_recurring BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_start_date (start_date),
    INDEX idx_year (year),
    INDEX idx_season_type (season_type)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `season_name`: Season identifier (e.g., "Ramadan 1446")
- `hijri_month`: Islamic calendar month
- `start_date`: Season start (Gregorian)
- `end_date`: Season end (Gregorian)
- `season_type`: Category of seasonal event
- `is_recurring`: Whether season repeats annually

**Sample Data:**
```sql
INSERT INTO seasons (season_name, hijri_month, start_date, end_date, year, season_type) VALUES
('Ramadan 1446', 'Ramadan', '2025-03-01', '2025-03-30', 2025, 'religious'),
('Eid Al-Fitr 1446', 'Shawwal', '2025-03-31', '2025-04-03', 2025, 'religious'),
('Hajj Season 1446', 'Dhul-Hijjah', '2025-06-04', '2025-06-09', 2025, 'religious'),
('Back to School', NULL, '2025-09-01', '2025-09-15', 2025, 'school'),
('Summer', NULL, '2025-06-21', '2025-09-22', 2025, 'weather');
```

---

### 7. Predictions Table
AI-generated forecasts

```sql
CREATE TABLE predictions (
    prediction_id INT PRIMARY KEY AUTO_INCREMENT,
    shop_id INT NOT NULL,
    product_id INT NOT NULL,
    season_id INT,
    prediction_date DATE NOT NULL,
    predicted_demand INT NOT NULL,
    confidence_lower INT,
    confidence_upper INT,
    confidence_score DECIMAL(3,2),
    model_version VARCHAR(20),
    model_type ENUM('arima', 'prophet', 'lstm', 'ensemble') DEFAULT 'ensemble',
    actual_demand INT,
    prediction_accuracy DECIMAL(5,2),
    recommendation TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_shop_product (shop_id, product_id),
    INDEX idx_prediction_date (prediction_date),
    INDEX idx_season_id (season_id),
    FOREIGN KEY (shop_id) REFERENCES shops(shop_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE CASCADE,
    FOREIGN KEY (season_id) REFERENCES seasons(season_id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `predicted_demand`: AI forecast quantity (REQ-004)
- `confidence_lower`: Lower bound of prediction
- `confidence_upper`: Upper bound of prediction
- `confidence_score`: Model confidence (0.00 to 1.00)
- `model_version`: ML model version used
- `actual_demand`: Real sales (filled after prediction date)
- `prediction_accuracy`: Calculated accuracy percentage
- `recommendation`: AI-generated suggestion

---

### 8. Permissions Table
System permissions/capabilities

```sql
CREATE TABLE permissions (
    permission_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) UNIQUE NOT NULL,
    description VARCHAR(255),
    category VARCHAR(50),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_name (name),
    INDEX idx_category (category)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Sample Permissions:**
```sql
INSERT INTO permissions (name, description, category) VALUES
('view_products', 'Can view product list', 'products'),
('create_product', 'Can create new products', 'products'),
('edit_product', 'Can edit existing products', 'products'),
('delete_product', 'Can delete products', 'products'),
('upload_products', 'Can bulk upload products', 'products'),
('view_analytics', 'Can view analytics dashboard', 'analytics'),
('view_predictions', 'Can view AI predictions', 'analytics'),
('manage_employees', 'Can manage employee accounts', 'admin'),
('manage_permissions', 'Can assign/modify permissions', 'admin'),
('view_sales', 'Can view sales reports', 'sales'),
('manage_inventory', 'Can manage inventory levels', 'inventory');
```

---

### 9. Permission_User Table (Junction)
Many-to-many relationship between users and permissions

```sql
CREATE TABLE permission_user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    permission_id INT NOT NULL,
    user_id INT NOT NULL,
    granted_by INT,
    granted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NULL,
    
    UNIQUE KEY uk_permission_user (permission_id, user_id),
    INDEX idx_user_id (user_id),
    FOREIGN KEY (permission_id) REFERENCES permissions(permission_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (granted_by) REFERENCES users(user_id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `permission_id`: Foreign key to permissions (REQ-015, 016, 017, 018)
- `user_id`: Foreign key to users
- `granted_by`: Admin who granted the permission
- `granted_at`: Timestamp of permission assignment
- `expires_at`: Optional permission expiration

---

### 10. External_Data Table
Data from external APIs

```sql
CREATE TABLE external_data (
    data_id INT PRIMARY KEY AUTO_INCREMENT,
    data_source VARCHAR(50) NOT NULL,
    data_type ENUM('weather', 'trends', 'market_index', 'social_media', 'economic') NOT NULL,
    location VARCHAR(100),
    date DATE NOT NULL,
    data_json JSON NOT NULL,
    sync_status ENUM('pending', 'completed', 'failed') DEFAULT 'completed',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_data_source (data_source),
    INDEX idx_date (date),
    INDEX idx_location (location),
    UNIQUE KEY uk_source_date (data_source, date, location)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Fields Description:**
- `data_source`: API source name (REQ-019)
- `data_type`: Category of external data
- `data_json`: JSON blob of fetched data
- `sync_status`: Sync operation status

**Sample JSON Structure:**
```json
{
  "temperature": 38.5,
  "humidity": 35,
  "weather_condition": "sunny",
  "rainfall": 0,
  "wind_speed": 12
}
```

---

### 11. Activity_Logs Table
Audit trail for security and debugging

```sql
CREATE TABLE activity_logs (
    log_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(50),
    entity_id INT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    request_data JSON,
    response_status INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_id (user_id),
    INDEX idx_action (action),
    INDEX idx_created_at (created_at),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Sample Actions:**
- `user_login`, `user_logout`
- `product_created`, `product_updated`, `product_deleted`
- `permission_assigned`, `permission_removed`
- `prediction_generated`

---

### 12. Password_Resets Table
Password reset tokens

```sql
CREATE TABLE password_resets (
    email VARCHAR(100) NOT NULL,
    token VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    
    INDEX idx_email (email),
    INDEX idx_token (token)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 13. Sessions Table (if using database sessions)
User session management

```sql
CREATE TABLE sessions (
    session_id VARCHAR(255) PRIMARY KEY,
    user_id INT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    payload TEXT,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_user_id (user_id),
    INDEX idx_last_activity (last_activity)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Database Views

### View: v_season_sales
Aggregated sales by season

```sql
CREATE VIEW v_season_sales AS
SELECT 
    s.shop_id,
    s.product_id,
    p.product_name,
    p.category,
    sea.season_name,
    sea.season_id,
    COUNT(s.sale_id) AS transaction_count,
    SUM(s.quantity_sold) AS total_quantity,
    SUM(s.total_price) AS total_revenue,
    AVG(s.unit_price) AS avg_unit_price,
    MIN(s.sale_date) AS first_sale_date,
    MAX(s.sale_date) AS last_sale_date
FROM sales s
JOIN products p ON s.product_id = p.product_id
JOIN seasons sea ON s.sale_date BETWEEN sea.start_date AND sea.end_date
GROUP BY s.shop_id, s.product_id, sea.season_id;
```

**Usage (REQ-003, REQ-004):**
```sql
SELECT * FROM v_season_sales 
WHERE product_id = 123 
ORDER BY season_name;
```

---

### View: v_product_analytics
Product performance metrics

```sql
CREATE VIEW v_product_analytics AS
SELECT 
    p.product_id,
    p.product_name,
    p.category,
    p.shop_id,
    i.quantity AS current_stock,
    COUNT(DISTINCT s.sale_date) AS days_sold,
    SUM(s.quantity_sold) AS total_sold,
    AVG(s.quantity_sold) AS avg_daily_sales,
    MAX(s.sale_date) AS last_sale_date,
    DATEDIFF(CURDATE(), MAX(s.sale_date)) AS days_since_last_sale
FROM products p
LEFT JOIN inventory i ON p.product_id = i.product_id
LEFT JOIN sales s ON p.product_id = s.product_id
GROUP BY p.product_id;
```

---

## Stored Procedures

### Procedure: sp_calculate_demand
Calculate historical demand patterns

```sql
DELIMITER //

CREATE PROCEDURE sp_calculate_demand(
    IN p_product_id INT,
    IN p_start_date DATE,
    IN p_end_date DATE
)
BEGIN
    SELECT 
        DATE_FORMAT(sale_date, '%Y-%m') AS month,
        SUM(quantity_sold) AS total_demand,
        COUNT(DISTINCT sale_date) AS selling_days,
        AVG(quantity_sold) AS avg_daily_demand,
        MIN(quantity_sold) AS min_daily_demand,
        MAX(quantity_sold) AS max_daily_demand
    FROM sales
    WHERE product_id = p_product_id
        AND sale_date BETWEEN p_start_date AND p_end_date
    GROUP BY month
    ORDER BY month;
END //

DELIMITER ;
```

**Usage:**
```sql
CALL sp_calculate_demand(123, '2024-01-01', '2024-12-31');
```

---

## Indexes Summary

### Critical Performance Indexes

1. **Sales Table** (high-volume queries):
   - Composite: `(shop_id, product_id, sale_date)`
   - Individual: `sale_date`, `sale_datetime`

2. **Products Table** (frequent searches):
   - FULLTEXT: `(product_name, description)` for REQ-011
   - Individual: `shop_id`, `category`, `seasonal_tag`

3. **Users Table**:
   - Unique: `email`
   - Individual: `role`, `shop_id`

4. **Predictions Table**:
   - Composite: `(shop_id, product_id)`
   - Individual: `prediction_date`, `season_id`

---

## Foreign Key Relationships

```
users.shop_id → shops.shop_id
products.shop_id → shops.shop_id
inventory.shop_id → shops.shop_id
inventory.product_id → products.product_id
sales.shop_id → shops.shop_id
sales.product_id → products.product_id
predictions.shop_id → shops.shop_id
predictions.product_id → products.product_id
predictions.season_id → seasons.season_id
permission_user.permission_id → permissions.permission_id
permission_user.user_id → users.user_id
```

---

## Data Retention Policies

1. **Sales Data**: Retain for 5 years (regulatory compliance)
2. **Activity Logs**: Retain for 1 year, then archive
3. **Predictions**: Retain for 2 years for model improvement
4. **Sessions**: Auto-delete after 24 hours of inactivity
5. **Password Resets**: Auto-delete after token expiration (1 hour)

---

## Backup Strategy

1. **Full Backup**: Daily at 2:00 AM UTC
2. **Incremental Backup**: Every 6 hours
3. **Binary Logs**: Enabled for point-in-time recovery
4. **Retention**: 30 days for full backups, 7 days for incremental

---

## Database Performance Tuning

### Key Configurations (my.cnf)
```ini
[mysqld]
innodb_buffer_pool_size = 2G
innodb_log_file_size = 512M
max_connections = 200
query_cache_size = 64M
tmp_table_size = 64M
max_heap_table_size = 64M
```

### Query Optimization Tips
1. Always use indexed columns in WHERE clauses
2. Avoid SELECT * - specify needed columns
3. Use EXPLAIN to analyze query execution plans
4. Limit result sets with appropriate pagination
5. Use prepared statements to prevent SQL injection

---

**Schema Version**: 1.0  
**Last Updated**: December 2024  
**Compatibility**: MariaDB 10.5+
