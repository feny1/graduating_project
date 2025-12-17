# Mawsim API Interface Documentation

## Base URL
```
Production: https://api.mawsim.sa/v1
Development: http://localhost:8000/api/v1
```

---

## Authentication Endpoints

### 1. Register Merchant (REQ-001)
**POST** `/auth/register`

**Request Body:**
```json
{
  "user_id": null,
  "shop_id": null,
  "name": "string (varchar 50, NOT NULL)",
  "email": "string (email format, unique)",
  "password": "string (min 8 chars)",
  "password_confirmation": "string",
  "phone": "string (optional)",
  "business_name": "string",
  "business_type": "string (supermarket/hypermarket)",
  "location": "string (city)"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "Account Created Successfully",
  "data": {
    "user_id": 1,
    "name": "Ahmed Al-Qarni",
    "email": "ahmed@example.com",
    "role": "merchant",
    "shop": {
      "shop_id": 1,
      "shop_name": "Al-Qarni Supermarket",
      "location": "Jeddah"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 3600
  }
}
```

**Error Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": {
    "email": ["Email already exists"],
    "password": ["Password must be at least 8 characters"]
  }
}
```

---

### 2. Login (REQ-002)
**POST** `/auth/login`

**Request Body:**
```json
{
  "email": "string",
  "password": "string"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "user_id": 1,
      "name": "Ahmed Al-Qarni",
      "email": "ahmed@example.com",
      "role": "merchant"
    },
    "permissions": [
      {
        "permission_id": 1,
        "name": "view_products"
      },
      {
        "permission_id": 2,
        "name": "create_product"
      }
    ],
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "dGhpc2lzYXJlZnJlc2h0b2tlbg...",
    "expires_in": 3600
  }
}
```

**Error Response (401 Unauthorized):**
```json
{
  "success": false,
  "message": "Invalid credentials"
}
```

---

### 3. Refresh Token
**POST** `/auth/refresh`

**Request Headers:**
```
Authorization: Bearer {refresh_token}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 3600
  }
}
```

---

### 4. Logout
**POST** `/auth/logout`

**Request Headers:**
```
Authorization: Bearer {token}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

## Product Management Endpoints

### 5. Create Product (REQ-005)
**POST** `/merchant/products`

**Request Headers:**
```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:**
```json
{
  "product_id": null,
  "product_name": "string (varchar 90, NOT NULL)",
  "category": "string (optional)",
  "price": "decimal (10,2)",
  "stock": "integer",
  "description": "text (optional)",
  "image_url": "string (optional)",
  "seasonal_tag": "string (optional, e.g., 'Ramadan', 'Eid')"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "Product created successfully",
  "data": {
    "product_id": 123,
    "product_name": "Vimto 1L",
    "category": "Beverages",
    "price": 15.50,
    "stock": 500,
    "seasonal_tag": "Ramadan",
    "created_at": "2024-12-17T10:30:00Z"
  }
}
```

---

### 6. Update Product (REQ-006)
**PUT** `/merchant/products/{product_id}`

**Request Body:**
```json
{
  "product_name": "string (optional)",
  "category": "string (optional)",
  "price": "decimal (optional)",
  "stock": "integer (optional)",
  "description": "text (optional)",
  "seasonal_tag": "string (optional)"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Product updated successfully",
  "data": {
    "product_id": 123,
    "product_name": "Vimto 1.5L",
    "price": 18.00,
    "updated_at": "2024-12-17T11:45:00Z"
  }
}
```

---

### 7. Delete Product (REQ-007)
**DELETE** `/merchant/products/{product_id}`

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Product deleted successfully"
}
```

**Confirmation Flow:**
The frontend should show a confirmation modal before calling this endpoint.

---

### 8. Upload Products (Bulk) (REQ-008)
**POST** `/merchant/products/upload`

**Request Headers:**
```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**Request Body (Form Data):**
```
file: products.csv (or .xlsx)
```

**CSV Format:**
```csv
product_name,category,price,stock,description,seasonal_tag
"Vimto 1L","Beverages",15.50,500,"Ramadan special drink","Ramadan"
"Dates Premium Box","Food",45.00,200,"High quality dates","Ramadan"
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Products uploaded successfully",
  "data": {
    "total_rows": 100,
    "successful": 95,
    "failed": 5,
    "errors": [
      {
        "row": 23,
        "error": "Invalid price format"
      },
      {
        "row": 67,
        "error": "Product name is required"
      }
    ]
  }
}
```

---

### 9. View Products (REQ-009)
**GET** `/merchant/products`

**Query Parameters:**
```
page: integer (default: 1)
limit: integer (default: 20, max: 100)
sort_by: string (name|price|stock|created_at)
order: string (asc|desc)
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "products": [
      {
        "product_id": 123,
        "product_name": "Vimto 1L",
        "category": "Beverages",
        "price": 15.50,
        "stock": 500,
        "seasonal_tag": "Ramadan",
        "image_url": "https://cdn.mawsim.sa/products/vimto.jpg",
        "created_at": "2024-12-17T10:30:00Z"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 10,
      "total_items": 195,
      "items_per_page": 20
    }
  }
}
```

---

### 10. View Product Details (REQ-010)
**GET** `/merchant/products/{product_id}`

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "product_id": 123,
    "product_name": "Vimto 1L",
    "category": "Beverages",
    "price": 15.50,
    "stock": 500,
    "description": "Popular Ramadan drink",
    "seasonal_tag": "Ramadan",
    "image_url": "https://cdn.mawsim.sa/products/vimto.jpg",
    "demand_history": [
      {
        "month": "2024-03",
        "sales": 2500,
        "season": "Ramadan"
      },
      {
        "month": "2024-04",
        "sales": 450,
        "season": "Regular"
      }
    ],
    "inventory_status": "In Stock",
    "created_at": "2024-12-17T10:30:00Z",
    "updated_at": "2024-12-17T11:45:00Z"
  }
}
```

---

### 11. Search Products (REQ-011)
**GET** `/merchant/products/search`

**Query Parameters:**
```
q: string (search query)
category: string (optional)
seasonal_tag: string (optional)
status: string (in_stock|low_stock|out_of_stock)
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "product_id": 123,
        "product_name": "Vimto 1L",
        "category": "Beverages",
        "price": 15.50,
        "stock": 500,
        "relevance_score": 0.95
      }
    ],
    "count": 15,
    "search_time": "0.045s"
  }
}
```

**Performance**: Must return results within 2.5 seconds (REQ-2.1.2.3.4)

---

### 12. Filter and Sort Products (REQ-012)
**GET** `/merchant/products/filter`

**Query Parameters:**
```
category: string[] (multi-select)
price_min: decimal
price_max: decimal
stock_status: string (in_stock|low_stock|out_of_stock)
seasonal_tag: string[]
sort_by: string (name|price|stock|popularity)
order: string (asc|desc)
page: integer
limit: integer
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "products": [...],
    "filters_applied": {
      "category": ["Beverages", "Food"],
      "price_range": [10.00, 50.00],
      "stock_status": "in_stock"
    },
    "pagination": {...}
  }
}
```

---

## Timeline & Prediction Endpoints

### 13. View Product Status Timeline (REQ-003)
**GET** `/merchant/timeline/{product_id}`

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "product_id": 123,
    "product_name": "Vimto 1L",
    "timeline": [
      {
        "period": "2024-03",
        "season": "Ramadan",
        "hijri_month": "Ramadan 1445",
        "status": "Peak",
        "demand_level": "High",
        "sales_volume": 2500,
        "confidence": 0.92
      },
      {
        "period": "2024-04",
        "season": "Shawwal",
        "hijri_month": "Shawwal 1445",
        "status": "Low",
        "demand_level": "Normal",
        "sales_volume": 450,
        "confidence": 0.88
      }
    ],
    "current_status": "Normal",
    "next_peak": {
      "season": "Ramadan",
      "date": "2025-03-01",
      "hijri_date": "1 Ramadan 1446",
      "days_until": 74
    }
  }
}
```

**Performance**: Must load within 2-4 seconds (REQ-2.1.2.3.4)

---

### 14. AI Seasonal Forecasting (REQ-004)
**POST** `/predictions/forecast`

**Request Body:**
```json
{
  "product_id": 123,
  "forecast_horizon": "6_months",
  "include_weather": true,
  "include_trends": true
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "product_id": 123,
    "product_name": "Vimto 1L",
    "forecast": [
      {
        "period": "2025-01",
        "predicted_demand": 520,
        "confidence_interval": {
          "lower": 480,
          "upper": 560
        },
        "season": "Regular",
        "recommendation": "Maintain normal stock levels"
      },
      {
        "period": "2025-03",
        "predicted_demand": 2800,
        "confidence_interval": {
          "lower": 2500,
          "upper": 3100
        },
        "season": "Ramadan",
        "recommendation": "Increase stock by 400%"
      }
    ],
    "best_time_to_stock": "2025-02-15",
    "recommended_quantity": 2500,
    "model_accuracy": 0.89,
    "generated_at": "2024-12-17T12:00:00Z"
  }
}
```

**Performance**: Must generate within 6-10 seconds (REQ-2.1.2.3.4)

---

## Admin Endpoints

### 15. Register Employee (REQ-013)
**POST** `/admin/employees`

**Request Body:**
```json
{
  "name": "string",
  "email": "string (unique)",
  "password": "string",
  "phone": "string (optional)",
  "department": "string (optional)",
  "position": "string (optional)"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "Account Created Successfully",
  "data": {
    "user_id": 45,
    "name": "Sara Ahmed",
    "email": "sara@example.com",
    "role": "employee",
    "created_at": "2024-12-17T13:00:00Z"
  }
}
```

---

### 16. View Employees (REQ-014)
**GET** `/admin/employees`

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "employees": [
      {
        "user_id": 45,
        "name": "Sara Ahmed",
        "email": "sara@example.com",
        "role": "employee",
        "permissions_count": 5,
        "status": "active",
        "created_at": "2024-12-17T13:00:00Z"
      }
    ],
    "total": 23
  }
}
```

---

### 17. View Employee Permissions (REQ-015)
**GET** `/admin/employees/{user_id}/permissions`

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "user_id": 45,
    "name": "Sara Ahmed",
    "permissions": [
      {
        "permission_id": 1,
        "name": "view_products",
        "description": "Can view product list"
      },
      {
        "permission_id": 2,
        "name": "create_product",
        "description": "Can create new products"
      },
      {
        "permission_id": 5,
        "name": "view_analytics",
        "description": "Can view analytics dashboard"
      }
    ]
  }
}
```

---

### 18. Assign Permissions (REQ-016)
**POST** `/admin/employees/{user_id}/permissions`

**Request Body:**
```json
{
  "permission_ids": [1, 2, 5, 8, 12]
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Permissions assigned successfully",
  "data": {
    "user_id": 45,
    "assigned_permissions": 5
  }
}
```

---

### 19. Modify Permissions (REQ-017)
**PUT** `/admin/employees/{user_id}/permissions`

**Request Body:**
```json
{
  "add_permissions": [15, 16],
  "remove_permissions": [2, 8]
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Permissions updated successfully",
  "data": {
    "user_id": 45,
    "total_permissions": 5,
    "added": 2,
    "removed": 2
  }
}
```

---

### 20. Remove Permissions (REQ-018)
**DELETE** `/admin/employees/{user_id}/permissions`

**Request Body:**
```json
{
  "permission_ids": [1, 5]
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Permissions removed successfully",
  "data": {
    "user_id": 45,
    "removed_permissions": 2,
    "remaining_permissions": 3
  }
}
```

---

## External Data Sync Endpoint

### 21. External Data Sync (REQ-019)
**POST** `/external/sync`

**Request Body:**
```json
{
  "data_source": "weather_api|trends_api|market_data",
  "date_range": {
    "start": "2024-01-01",
    "end": "2024-12-31"
  }
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Data sync completed successfully",
  "data": {
    "source": "weather_api",
    "records_fetched": 365,
    "records_updated": 340,
    "records_new": 25,
    "sync_duration": "12.5s",
    "last_sync": "2024-12-17T14:00:00Z"
  }
}
```

**Automated Scheduling:**
This endpoint is also called automatically by a cron job every 24 hours.

---

## Error Codes & Messages

### HTTP Status Codes
- `200` - OK: Successful request
- `201` - Created: Resource created successfully
- `400` - Bad Request: Invalid input data
- `401` - Unauthorized: Authentication required
- `403` - Forbidden: Insufficient permissions
- `404` - Not Found: Resource doesn't exist
- `422` - Unprocessable Entity: Validation failed
- `429` - Too Many Requests: Rate limit exceeded
- `500` - Internal Server Error: Server error

### Standard Error Response Format
```json
{
  "success": false,
  "message": "Error description",
  "errors": {
    "field_name": ["Error message 1", "Error message 2"]
  },
  "error_code": "ERR_CODE_001"
}
```

---

## Rate Limiting

All endpoints are rate-limited:
- **Authenticated requests**: 1000 requests per hour
- **Authentication endpoints**: 10 requests per minute
- **Upload endpoints**: 5 requests per minute

**Rate Limit Headers:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 847
X-RateLimit-Reset: 1702825600
```

---

## Security Headers

All API responses include:
```
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000
```

---

## Pagination

Standard pagination response structure:
```json
{
  "pagination": {
    "current_page": 1,
    "total_pages": 10,
    "total_items": 195,
    "items_per_page": 20,
    "has_next": true,
    "has_previous": false,
    "links": {
      "first": "/api/v1/products?page=1",
      "last": "/api/v1/products?page=10",
      "next": "/api/v1/products?page=2",
      "previous": null
    }
  }
}
```

---

## Localization

All endpoints support localization via the `Accept-Language` header:
```
Accept-Language: ar-SA (Arabic)
Accept-Language: en-US (English)
```

Date formats automatically adjust based on locale:
- **Gregorian**: ISO 8601 format
- **Hijri**: Islamic calendar format

---

**API Version**: 1.0  
**Last Updated**: December 2024  
**Base URL**: https://api.mawsim.sa/v1
