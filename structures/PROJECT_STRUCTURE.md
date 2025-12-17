# Mawsim Project - Complete File and Folder Structure

## Project Overview
**Mawsim** - AI-powered seasonal demand forecasting system for Saudi supermarkets and hypermarkets.

---

## Root Directory Structure

```
mawsim/
├── frontend/                      # React SPA with SSR
├── backend/                       # Pure PHP REST API
├── ml-service/                    # Python ML microservice
├── database/                      # Database schemas and migrations
├── docker/                        # Docker configurations
├── docs/                          # Project documentation
├── scripts/                       # Deployment and utility scripts
├── tests/                         # Integration and E2E tests
├── .github/                       # GitHub Actions workflows
├── docker-compose.yml             # Docker orchestration
├── .env.example                   # Environment variables template
├── .gitignore                     # Git ignore rules
├── README.md                      # Project README
└── LICENSE                        # Project license
```

---

## 1. Frontend Structure (`/frontend`)

```
frontend/
├── public/
│   ├── index.html                 # HTML template
│   ├── favicon.ico
│   ├── manifest.json              # PWA manifest
│   ├── robots.txt
│   └── assets/
│       ├── images/
│       │   ├── logo.svg
│       │   ├── logo-ar.svg        # Arabic version
│       │   └── icons/
│       └── fonts/
│           ├── arabic-fonts/      # Arabic typography
│           └── english-fonts/
│
├── src/
│   ├── index.js                   # Entry point
│   ├── App.js                     # Root component
│   ├── App.css                    # Global styles
│   │
│   ├── components/                # Reusable UI components
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   │   ├── Button.jsx
│   │   │   │   ├── Button.test.jsx
│   │   │   │   └── Button.module.css
│   │   │   ├── Input/
│   │   │   │   ├── Input.jsx
│   │   │   │   └── Input.module.css
│   │   │   ├── Card/
│   │   │   │   ├── Card.jsx
│   │   │   │   └── Card.module.css
│   │   │   ├── Modal/
│   │   │   │   ├── Modal.jsx
│   │   │   │   └── Modal.module.css
│   │   │   ├── Table/
│   │   │   │   ├── Table.jsx
│   │   │   │   ├── TableRow.jsx
│   │   │   │   ├── TableHeader.jsx
│   │   │   │   └── Table.module.css
│   │   │   ├── Loader/
│   │   │   │   ├── Loader.jsx
│   │   │   │   └── Loader.module.css
│   │   │   ├── Alert/
│   │   │   │   ├── Alert.jsx
│   │   │   │   └── Alert.module.css
│   │   │   └── Pagination/
│   │   │       ├── Pagination.jsx
│   │   │       └── Pagination.module.css
│   │   │
│   │   ├── layout/
│   │   │   ├── Header/
│   │   │   │   ├── Header.jsx
│   │   │   │   ├── Header.module.css
│   │   │   │   └── components/
│   │   │   │       ├── UserMenu.jsx
│   │   │   │       ├── Notifications.jsx
│   │   │   │       └── LanguageSwitcher.jsx
│   │   │   ├── Sidebar/
│   │   │   │   ├── Sidebar.jsx
│   │   │   │   ├── Sidebar.module.css
│   │   │   │   └── components/
│   │   │   │       ├── NavItem.jsx
│   │   │   │       └── NavGroup.jsx
│   │   │   ├── Footer/
│   │   │   │   ├── Footer.jsx
│   │   │   │   └── Footer.module.css
│   │   │   └── DashboardLayout/
│   │   │       ├── DashboardLayout.jsx
│   │   │       └── DashboardLayout.module.css
│   │   │
│   │   ├── charts/                # Data visualization components
│   │   │   ├── TimelineChart/
│   │   │   │   ├── TimelineChart.jsx
│   │   │   │   └── TimelineChart.module.css
│   │   │   ├── SeasonalChart/
│   │   │   │   ├── SeasonalChart.jsx
│   │   │   │   └── SeasonalChart.module.css
│   │   │   ├── DemandCurve/
│   │   │   │   ├── DemandCurve.jsx
│   │   │   │   └── DemandCurve.module.css
│   │   │   └── SalesComparison/
│   │   │       ├── SalesComparison.jsx
│   │   │       └── SalesComparison.module.css
│   │   │
│   │   └── forms/                 # Form components
│   │       ├── ProductForm/
│   │       │   ├── ProductForm.jsx
│   │       │   ├── ProductForm.validation.js
│   │       │   └── ProductForm.module.css
│   │       ├── FileUpload/
│   │       │   ├── FileUpload.jsx
│   │       │   ├── CSVUploader.jsx
│   │       │   └── FileUpload.module.css
│   │       └── SearchFilters/
│   │           ├── SearchFilters.jsx
│   │           └── SearchFilters.module.css
│   │
│   ├── pages/                     # Page components
│   │   ├── auth/
│   │   │   ├── Login/
│   │   │   │   ├── Login.jsx
│   │   │   │   └── Login.module.css
│   │   │   ├── Register/
│   │   │   │   ├── Register.jsx
│   │   │   │   └── Register.module.css
│   │   │   └── ForgotPassword/
│   │   │       ├── ForgotPassword.jsx
│   │   │       └── ForgotPassword.module.css
│   │   │
│   │   ├── merchant/
│   │   │   ├── Dashboard/
│   │   │   │   ├── Dashboard.jsx
│   │   │   │   ├── Dashboard.module.css
│   │   │   │   └── widgets/
│   │   │   │       ├── QuickStats.jsx
│   │   │   │       ├── UpcomingSeasons.jsx
│   │   │   │       └── RecentPredictions.jsx
│   │   │   ├── Products/
│   │   │   │   ├── ProductList.jsx
│   │   │   │   ├── ProductDetails.jsx
│   │   │   │   ├── AddProduct.jsx
│   │   │   │   ├── EditProduct.jsx
│   │   │   │   └── Products.module.css
│   │   │   ├── Timeline/
│   │   │   │   ├── StatusTimeline.jsx       # REQ-003
│   │   │   │   └── Timeline.module.css
│   │   │   └── Analytics/
│   │   │       ├── SalesAnalytics.jsx
│   │   │       ├── SeasonalTrends.jsx
│   │   │       └── Analytics.module.css
│   │   │
│   │   ├── admin/
│   │   │   ├── Dashboard/
│   │   │   │   ├── AdminDashboard.jsx
│   │   │   │   └── AdminDashboard.module.css
│   │   │   ├── Permissions/
│   │   │   │   ├── PermissionList.jsx       # REQ-014
│   │   │   │   ├── EmployeePermissions.jsx  # REQ-015, 016, 017, 018
│   │   │   │   ├── AssignPermissions.jsx
│   │   │   │   └── Permissions.module.css
│   │   │   ├── Employees/
│   │   │   │   ├── EmployeeList.jsx
│   │   │   │   ├── RegisterEmployee.jsx     # REQ-013
│   │   │   │   └── Employees.module.css
│   │   │   └── SystemSettings/
│   │   │       ├── SystemSettings.jsx
│   │   │       └── SystemSettings.module.css
│   │   │
│   │   ├── employee/
│   │   │   ├── Dashboard/
│   │   │   │   ├── EmployeeDashboard.jsx
│   │   │   │   └── EmployeeDashboard.module.css
│   │   │   └── Tasks/
│   │   │       ├── AssignedTasks.jsx
│   │   │       └── Tasks.module.css
│   │   │
│   │   └── shared/
│   │       ├── NotFound/
│   │       │   ├── NotFound.jsx
│   │       │   └── NotFound.module.css
│   │       └── Unauthorized/
│   │           ├── Unauthorized.jsx
│   │           └── Unauthorized.module.css
│   │
│   ├── hooks/                     # Custom React hooks
│   │   ├── useAuth.js
│   │   ├── usePermissions.js
│   │   ├── useProducts.js
│   │   ├── usePredictions.js
│   │   ├── useDebounce.js
│   │   ├── useLocalStorage.js
│   │   └── useApi.js
│   │
│   ├── contexts/                  # React contexts
│   │   ├── AuthContext.js
│   │   ├── ThemeContext.js
│   │   ├── LanguageContext.js
│   │   └── PermissionContext.js
│   │
│   ├── services/                  # API service layer
│   │   ├── api/
│   │   │   ├── axios.config.js    # Axios instance
│   │   │   ├── interceptors.js    # Request/Response interceptors
│   │   │   └── endpoints.js       # API endpoint constants
│   │   ├── auth.service.js        # REQ-001, 002
│   │   ├── product.service.js     # REQ-005 to 012
│   │   ├── employee.service.js    # REQ-013, 014
│   │   ├── permission.service.js  # REQ-015 to 018
│   │   ├── prediction.service.js  # REQ-004
│   │   ├── timeline.service.js    # REQ-003
│   │   └── external.service.js    # REQ-019
│   │
│   ├── utils/                     # Utility functions
│   │   ├── validation.js          # Form validation helpers
│   │   ├── formatters.js          # Date, currency, number formatters
│   │   ├── hijri-calendar.js      # Hijri date utilities
│   │   ├── storage.js             # LocalStorage helpers
│   │   ├── permissions.js         # Permission checking utilities
│   │   └── constants.js           # App-wide constants
│   │
│   ├── styles/                    # Global styles
│   │   ├── variables.css          # CSS variables
│   │   ├── theme.css              # Theme definitions
│   │   ├── typography.css         # Typography styles
│   │   ├── rtl.css                # RTL (Arabic) styles
│   │   └── responsive.css         # Responsive breakpoints
│   │
│   ├── i18n/                      # Internationalization
│   │   ├── index.js
│   │   ├── config.js
│   │   └── locales/
│   │       ├── en/
│   │       │   ├── common.json
│   │       │   ├── products.json
│   │       │   ├── auth.json
│   │       │   └── dashboard.json
│   │       └── ar/
│   │           ├── common.json
│   │           ├── products.json
│   │           ├── auth.json
│   │           └── dashboard.json
│   │
│   ├── routes/                    # Route configuration
│   │   ├── index.js               # Main router
│   │   ├── ProtectedRoute.jsx    # Route guard
│   │   ├── RoleRoute.jsx          # Role-based routing
│   │   └── routes.config.js       # Route definitions
│   │
│   └── __tests__/                 # Frontend tests
│       ├── components/
│       ├── pages/
│       ├── hooks/
│       └── utils/
│
├── package.json                   # Dependencies
├── package-lock.json
├── .eslintrc.js                   # ESLint config
├── .prettierrc                    # Prettier config
├── jsconfig.json                  # JavaScript config
├── tailwind.config.js             # Tailwind CSS config (if using)
└── vite.config.js                 # Vite bundler config (or webpack.config.js)
```

---

## 2. Backend Structure (`/backend`)

```
backend/
├── public/
│   ├── index.php                  # Entry point
│   └── .htaccess                  # Apache rewrite rules
│
├── src/
│   ├── Config/
│   │   ├── Database.php           # Database connection
│   │   ├── App.php                # App configuration
│   │   ├── Jwt.php                # JWT configuration
│   │   └── Environment.php        # Environment loader
│   │
│   ├── Controllers/               # Request handlers
│   │   ├── Auth/
│   │   │   ├── LoginController.php          # REQ-002
│   │   │   ├── RegisterController.php       # REQ-001
│   │   │   ├── LogoutController.php
│   │   │   └── RefreshTokenController.php
│   │   ├── Merchant/
│   │   │   ├── ProductController.php        # REQ-005 to 012
│   │   │   ├── TimelineController.php       # REQ-003
│   │   │   └── DashboardController.php
│   │   ├── Admin/
│   │   │   ├── EmployeeController.php       # REQ-013, 014
│   │   │   ├── PermissionController.php     # REQ-015 to 018
│   │   │   └── SystemController.php
│   │   ├── Employee/
│   │   │   └── TaskController.php
│   │   └── Prediction/
│   │       ├── ForecastController.php       # REQ-004
│   │       └── AnalyticsController.php
│   │
│   ├── Models/                    # Data models
│   │   ├── User.php
│   │   ├── Merchant.php
│   │   ├── Employee.php
│   │   ├── Admin.php
│   │   ├── Product.php
│   │   ├── Shop.php
│   │   ├── Sale.php
│   │   ├── Inventory.php
│   │   ├── Season.php
│   │   ├── Permission.php
│   │   ├── PermissionUser.php
│   │   ├── Prediction.php
│   │   └── ExternalData.php
│   │
│   ├── Repositories/              # Data access layer
│   │   ├── UserRepository.php
│   │   ├── ProductRepository.php
│   │   ├── SaleRepository.php
│   │   ├── PermissionRepository.php
│   │   └── PredictionRepository.php
│   │
│   ├── Services/                  # Business logic
│   │   ├── Auth/
│   │   │   ├── AuthService.php
│   │   │   ├── JwtService.php
│   │   │   └── PasswordService.php
│   │   ├── Product/
│   │   │   ├── ProductService.php
│   │   │   ├── ProductUploadService.php     # REQ-008
│   │   │   └── ProductSearchService.php     # REQ-011, 012
│   │   ├── Permission/
│   │   │   ├── PermissionService.php
│   │   │   └── RoleService.php
│   │   ├── Prediction/
│   │   │   ├── PredictionService.php
│   │   │   └── MLGatewayService.php         # Communicates with ML service
│   │   └── External/
│   │       ├── ExternalDataService.php      # REQ-019
│   │       └── DataSyncService.php
│   │
│   ├── Middleware/                # Request middleware
│   │   ├── AuthMiddleware.php     # Verify JWT tokens
│   │   ├── RoleMiddleware.php     # Check user roles
│   │   ├── PermissionMiddleware.php # Check permissions
│   │   ├── CorsMiddleware.php     # Handle CORS
│   │   ├── RateLimitMiddleware.php
│   │   ├── ValidationMiddleware.php
│   │   └── LoggingMiddleware.php
│   │
│   ├── Validators/                # Input validation
│   │   ├── AuthValidator.php
│   │   ├── ProductValidator.php
│   │   ├── EmployeeValidator.php
│   │   └── PermissionValidator.php
│   │
│   ├── Utils/                     # Helper utilities
│   │   ├── Response.php           # Standardized API responses
│   │   ├── Logger.php             # Logging utility
│   │   ├── FileUploader.php       # CSV/Excel file handler
│   │   ├── HijriCalendar.php      # Hijri date utilities
│   │   └── Encryption.php         # Data encryption
│   │
│   ├── Routes/                    # API routes
│   │   ├── api.php                # Main route file
│   │   ├── auth.routes.php
│   │   ├── merchant.routes.php
│   │   ├── admin.routes.php
│   │   ├── employee.routes.php
│   │   └── prediction.routes.php
│   │
│   ├── Exceptions/                # Custom exceptions
│   │   ├── AuthException.php
│   │   ├── NotFoundException.php
│   │   ├── ValidationException.php
│   │   ├── PermissionException.php
│   │   └── DatabaseException.php
│   │
│   └── Traits/                    # Reusable traits
│       ├── HasTimestamps.php
│       ├── SoftDeletes.php
│       └── Searchable.php
│
├── storage/                       # Storage directory
│   ├── logs/
│   │   ├── app.log
│   │   ├── error.log
│   │   └── access.log
│   ├── uploads/
│   │   ├── products/
│   │   └── csv/
│   └── cache/
│       └── predictions/
│
├── tests/                         # Backend tests
│   ├── Unit/
│   │   ├── Services/
│   │   ├── Models/
│   │   └── Utils/
│   └── Integration/
│       └── Controllers/
│
├── composer.json                  # PHP dependencies
├── composer.lock
├── .htaccess
└── phpunit.xml                    # PHPUnit config
```

---

## 3. ML Service Structure (`/ml-service`)

```
ml-service/
├── app/
│   ├── __init__.py
│   ├── main.py                    # FastAPI entry point
│   │
│   ├── api/                       # API endpoints
│   │   ├── __init__.py
│   │   ├── v1/
│   │   │   ├── __init__.py
│   │   │   ├── prediction.py     # Prediction endpoints
│   │   │   ├── training.py       # Model training endpoints
│   │   │   └── health.py         # Health check
│   │   └── dependencies.py       # Dependency injection
│   │
│   ├── models/                    # ML models
│   │   ├── __init__.py
│   │   ├── base_model.py
│   │   ├── arima_model.py        # ARIMA time series
│   │   ├── prophet_model.py      # Facebook Prophet
│   │   ├── lstm_model.py         # LSTM neural network
│   │   └── ensemble_model.py     # Ensemble predictions
│   │
│   ├── services/                  # Business logic
│   │   ├── __init__.py
│   │   ├── prediction_service.py  # REQ-004
│   │   ├── training_service.py
│   │   ├── data_processor.py      # REQ-019 data processing
│   │   └── feature_engineering.py
│   │
│   ├── data/                      # Data processing
│   │   ├── __init__.py
│   │   ├── preprocessor.py
│   │   ├── seasonal_detector.py
│   │   ├── hijri_converter.py
│   │   └── outlier_handler.py
│   │
│   ├── schemas/                   # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── prediction.py
│   │   ├── training.py
│   │   └── response.py
│   │
│   ├── core/                      # Core utilities
│   │   ├── __init__.py
│   │   ├── config.py             # Configuration
│   │   ├── security.py           # API key validation
│   │   └── exceptions.py         # Custom exceptions
│   │
│   └── utils/                     # Helper utilities
│       ├── __init__.py
│       ├── logger.py
│       ├── metrics.py            # Model performance metrics
│       └── visualization.py      # Chart generation
│
├── models/                        # Saved ML models
│   ├── arima/
│   ├── prophet/
│   ├── lstm/
│   └── ensemble/
│
├── data/                          # Training data
│   ├── raw/
│   ├── processed/
│   └── external/
│       ├── weather/
│       └── trends/
│
├── notebooks/                     # Jupyter notebooks
│   ├── exploratory_analysis.ipynb
│   ├── model_training.ipynb
│   └── evaluation.ipynb
│
├── tests/                         # ML tests
│   ├── test_models.py
│   ├── test_services.py
│   └── test_preprocessing.py
│
├── requirements.txt               # Python dependencies
├── Dockerfile
└── .env.example
```

---

## 4. Database Structure (`/database`)

```
database/
├── schemas/
│   ├── mawsim_schema.sql         # Complete database schema
│   ├── tables/
│   │   ├── users.sql
│   │   ├── merchants.sql
│   │   ├── employees.sql
│   │   ├── permissions.sql
│   │   ├── permission_user.sql
│   │   ├── shops.sql
│   │   ├── products.sql
│   │   ├── sales.sql
│   │   ├── inventory.sql
│   │   ├── seasons.sql
│   │   ├── predictions.sql
│   │   └── external_data.sql
│   ├── views/
│   │   ├── v_season_sales.sql
│   │   └── v_product_analytics.sql
│   └── procedures/
│       ├── sp_calculate_demand.sql
│       └── sp_generate_forecast.sql
│
├── migrations/
│   ├── 001_create_users_table.sql
│   ├── 002_create_permissions_table.sql
│   ├── 003_create_products_table.sql
│   ├── 004_create_sales_table.sql
│   ├── 005_create_predictions_table.sql
│   └── rollback/
│       ├── 001_rollback.sql
│       └── 002_rollback.sql
│
├── seeds/
│   ├── permissions_seed.sql
│   ├── test_users_seed.sql
│   └── sample_products_seed.sql
│
└── docs/
    ├── ERD.png                    # Entity Relationship Diagram
    ├── data_dictionary.md         # Table and column descriptions
    └── relationships.md           # Table relationships documentation
```

---

## 5. Docker Configuration (`/docker`)

```
docker/
├── nginx/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── ssl/
│       ├── cert.pem
│       └── key.pem
│
├── php/
│   ├── Dockerfile
│   ├── php.ini
│   └── php-fpm.conf
│
├── mysql/
│   ├── Dockerfile
│   └── my.cnf
│
├── redis/
│   ├── Dockerfile
│   └── redis.conf
│
└── python/
    ├── Dockerfile
    └── requirements.txt
```

---

## 6. Documentation (`/docs`)

```
docs/
├── requirements/
│   ├── SRS.md                     # Software Requirements Specification
│   ├── functional_requirements.md
│   └── non_functional_requirements.md
│
├── design/
│   ├── system_architecture.md
│   ├── database_design.md
│   ├── api_design.md
│   └── ui_ux_design.md
│
├── api/
│   ├── API_REFERENCE.md           # Complete API documentation
│   ├── endpoints/
│   │   ├── auth.md
│   │   ├── products.md
│   │   ├── predictions.md
│   │   └── permissions.md
│   └── postman/
│       └── Mawsim_Collection.json
│
├── user_guides/
│   ├── merchant_guide.md
│   ├── admin_guide.md
│   └── employee_guide.md
│
├── developer/
│   ├── setup_guide.md
│   ├── coding_standards.md
│   ├── git_workflow.md
│   └── deployment_guide.md
│
└── diagrams/
    ├── system_architecture.png
    ├── data_flow.png
    ├── use_case_diagrams.png
    └── sequence_diagrams/
```

---

## 7. Scripts (`/scripts`)

```
scripts/
├── setup/
│   ├── install.sh                 # Initial setup script
│   ├── setup_database.sh
│   └── setup_env.sh
│
├── deployment/
│   ├── deploy.sh                  # Production deployment
│   ├── deploy_staging.sh
│   └── rollback.sh
│
├── database/
│   ├── migrate.sh                 # Run migrations
│   ├── seed.sh                    # Seed database
│   ├── backup.sh                  # Database backup
│   └── restore.sh                 # Database restore
│
└── utils/
    ├── clear_cache.sh
    ├── generate_jwt_key.sh
    └── check_health.sh
```

---

## 8. Tests (`/tests`)

```
tests/
├── integration/
│   ├── api/
│   │   ├── auth.test.js
│   │   ├── products.test.js
│   │   └── predictions.test.js
│   └── database/
│       └── connection.test.js
│
├── e2e/
│   ├── user_flows/
│   │   ├── merchant_flow.test.js
│   │   ├── admin_flow.test.js
│   │   └── employee_flow.test.js
│   └── scenarios/
│       ├── product_management.test.js
│       └── permission_management.test.js
│
└── performance/
    ├── load_test.js
    └── stress_test.js
```

---

## 9. CI/CD (`/.github`)

```
.github/
├── workflows/
│   ├── ci.yml                     # Continuous Integration
│   ├── cd.yml                     # Continuous Deployment
│   ├── test.yml                   # Run tests
│   └── security-scan.yml          # Security scanning
│
└── ISSUE_TEMPLATE/
    ├── bug_report.md
    └── feature_request.md
```

---

## Configuration Files (Root Level)

### docker-compose.yml
```yaml
version: '3.8'
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
  backend:
    build: ./backend
    ports:
      - "8000:8000"
  ml-service:
    build: ./ml-service
    ports:
      - "8001:8001"
  database:
    image: mariadb:latest
    ports:
      - "3306:3306"
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
```

### .env.example
```env
# Application
APP_NAME=Mawsim
APP_ENV=production
APP_DEBUG=false
APP_URL=https://mawsim.sa

# Database
DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=mawsim
DB_USERNAME=mawsim_user
DB_PASSWORD=

# JWT
JWT_SECRET=
JWT_EXPIRATION=3600

# ML Service
ML_SERVICE_URL=http://ml-service:8001
ML_API_KEY=

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# External APIs
WEATHER_API_KEY=
TRENDS_API_KEY=
```

### .gitignore
```
# Dependencies
node_modules/
vendor/
__pycache__/

# Environment
.env
.env.local

# Logs
*.log
logs/

# Build
dist/
build/
*.pyc

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Uploads
storage/uploads/*
!storage/uploads/.gitkeep
```

---

## Key Interfaces and Their Locations

### 1. Authentication Interfaces
- **Frontend**: `/frontend/src/pages/auth/`
- **Backend**: `/backend/src/Controllers/Auth/`
- **Requirements**: REQ-001 (Register), REQ-002 (Login)

### 2. Product Management Interfaces
- **Frontend**: `/frontend/src/pages/merchant/Products/`
- **Backend**: `/backend/src/Controllers/Merchant/ProductController.php`
- **Requirements**: REQ-005 to REQ-012

### 3. Timeline/Status Visualization
- **Frontend**: `/frontend/src/pages/merchant/Timeline/StatusTimeline.jsx`
- **Backend**: `/backend/src/Controllers/Merchant/TimelineController.php`
- **Requirement**: REQ-003

### 4. AI Forecasting Interface
- **Frontend**: `/frontend/src/pages/merchant/Analytics/`
- **Backend**: `/backend/src/Controllers/Prediction/ForecastController.php`
- **ML Service**: `/ml-service/app/api/v1/prediction.py`
- **Requirement**: REQ-004

### 5. Permission Management Interface
- **Frontend**: `/frontend/src/pages/admin/Permissions/`
- **Backend**: `/backend/src/Controllers/Admin/PermissionController.php`
- **Requirements**: REQ-013 to REQ-018

### 6. External Data Sync
- **Backend**: `/backend/src/Services/External/DataSyncService.php`
- **ML Service**: `/ml-service/app/services/data_processor.py`
- **Requirement**: REQ-019

---

## Technology Stack Summary

### Frontend
- **Framework**: React.js 18+
- **UI Library**: Ant Design / Material-UI
- **State Management**: React Context API / Redux
- **Charts**: Recharts / Chart.js
- **HTTP Client**: Axios
- **Routing**: React Router v6
- **i18n**: react-i18next
- **Build Tool**: Vite / Webpack

### Backend
- **Language**: PHP 8.2+
- **Web Server**: Apache / Nginx
- **Database**: MariaDB 10.x
- **Cache**: Redis
- **Authentication**: JWT (JSON Web Tokens)
- **File Processing**: PhpSpreadsheet (for Excel/CSV)

### ML Service
- **Language**: Python 3.9+
- **Framework**: FastAPI
- **ML Libraries**: 
  - scikit-learn
  - pandas
  - numpy
  - Prophet (Facebook)
  - statsmodels (ARIMA)
- **API Documentation**: Swagger/OpenAPI

### DevOps
- **Containerization**: Docker
- **Orchestration**: Docker Compose
- **CI/CD**: GitHub Actions
- **Version Control**: Git

---

## Development Workflow

1. **Clone Repository**
   ```bash
   git clone https://github.com/your-org/mawsim.git
   cd mawsim
   ```

2. **Setup Environment**
   ```bash
   ./scripts/setup/install.sh
   ```

3. **Start Development Servers**
   ```bash
   docker-compose up -d
   ```

4. **Access Applications**
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:8000
   - ML Service: http://localhost:8001
   - API Docs: http://localhost:8001/docs

---

## Notes

- All dates and times should support both Gregorian and Hijri calendars
- RTL (Right-to-Left) support is mandatory for Arabic interface
- All API responses follow standard JSON format
- File uploads are validated for CSV/Excel format
- Security middleware enforces HTTPS in production
- Rate limiting applied to all public endpoints
- Comprehensive logging for audit trails
- Performance monitoring for REQ-2.1.2.3.4 compliance

---

**Document Version**: 1.0  
**Last Updated**: December 2024  
**Project**: Mawsim - AI Seasonal Demand Forecasting  
**Author**: Development Team
