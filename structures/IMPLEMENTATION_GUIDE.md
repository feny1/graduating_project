# Mawsim Implementation Guide

## Quick Start Guide

### Prerequisites
- Node.js >= 18.x
- PHP >= 8.2
- MariaDB >= 10.5
- Python >= 3.9
- Redis >= 6.x
- Docker & Docker Compose (recommended)

---

## Installation Methods

### Method 1: Docker (Recommended)

#### Step 1: Clone Repository
```bash
git clone https://github.com/your-org/mawsim.git
cd mawsim
```

#### Step 2: Environment Setup
```bash
# Copy environment file
cp .env.example .env

# Edit .env with your configurations
nano .env
```

#### Step 3: Start Services
```bash
# Build and start all containers
docker-compose up -d --build

# Check container status
docker-compose ps
```

#### Step 4: Initialize Database
```bash
# Run migrations
docker-compose exec backend php artisan migrate

# Seed initial data
docker-compose exec backend php artisan db:seed
```

#### Step 5: Access Application
- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:8000/api/v1
- **ML Service**: http://localhost:8001
- **API Docs**: http://localhost:8001/docs

---

### Method 2: Manual Installation

#### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create environment file
cp .env.example .env.local

# Edit environment variables
REACT_APP_API_URL=http://localhost:8000/api/v1
REACT_APP_ML_SERVICE_URL=http://localhost:8001

# Start development server
npm start

# Build for production
npm run build
```

#### Backend Setup

```bash
cd backend

# Install PHP dependencies
composer install

# Copy environment file
cp .env.example .env

# Generate JWT secret
php -r "echo bin2hex(random_bytes(32));" > jwt_secret.txt

# Update .env with JWT secret
# JWT_SECRET=<content_of_jwt_secret.txt>

# Create required directories
mkdir -p storage/logs storage/uploads storage/cache

# Set permissions
chmod -R 775 storage
chmod -R 775 public

# Run migrations
php migrate.php

# Start PHP development server
php -S localhost:8000 -t public
```

#### ML Service Setup

```bash
cd ml-service

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp .env.example .env

# Train initial models (optional)
python scripts/train_models.py

# Start FastAPI server
uvicorn app.main:app --host 0.0.0.0 --port 8001 --reload
```

#### Database Setup

```bash
# Login to MariaDB
mysql -u root -p

# Create database
CREATE DATABASE mawsim_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# Create user
CREATE USER 'mawsim_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON mawsim_db.* TO 'mawsim_user'@'localhost';
FLUSH PRIVILEGES;

# Import schema
mysql -u mawsim_user -p mawsim_db < database/schemas/mawsim_schema.sql

# Import seed data
mysql -u mawsim_user -p mawsim_db < database/seeds/permissions_seed.sql
```

---

## Configuration

### Frontend Configuration (.env.local)
```env
# API Endpoints
REACT_APP_API_URL=http://localhost:8000/api/v1
REACT_APP_ML_SERVICE_URL=http://localhost:8001

# Feature Flags
REACT_APP_ENABLE_ANALYTICS=true
REACT_APP_ENABLE_NOTIFICATIONS=true

# Localization
REACT_APP_DEFAULT_LANGUAGE=ar
REACT_APP_SUPPORTED_LANGUAGES=ar,en

# Performance
REACT_APP_ENABLE_SERVICE_WORKER=true
```

### Backend Configuration (.env)
```env
# Application
APP_NAME=Mawsim
APP_ENV=development
APP_DEBUG=true
APP_URL=http://localhost:8000

# Database
DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=mawsim_db
DB_USERNAME=mawsim_user
DB_PASSWORD=your_secure_password

# JWT Configuration
JWT_SECRET=your_generated_secret_key
JWT_EXPIRATION=3600
JWT_REFRESH_EXPIRATION=86400

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

# ML Service
ML_SERVICE_URL=http://localhost:8001
ML_API_KEY=your_ml_api_key

# External APIs
WEATHER_API_KEY=your_weather_api_key
WEATHER_API_URL=https://api.weatherapi.com/v1

# File Upload
MAX_UPLOAD_SIZE=10485760  # 10MB
ALLOWED_FILE_TYPES=csv,xlsx,xls

# Logging
LOG_LEVEL=debug
LOG_FILE=storage/logs/app.log

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:3000,https://mawsim.sa
```

### ML Service Configuration (.env)
```env
# Application
APP_NAME=Mawsim ML Service
ENV=development
DEBUG=true

# Model Configuration
MODEL_PATH=./models
MODEL_VERSION=1.0.0
DEFAULT_MODEL=ensemble

# Training
BATCH_SIZE=32
EPOCHS=100
LEARNING_RATE=0.001

# API Security
API_KEY=your_ml_api_key
ALLOWED_HOSTS=localhost,backend

# Database (for fetching training data)
DB_HOST=localhost
DB_PORT=3306
DB_NAME=mawsim_db
DB_USER=mawsim_user
DB_PASSWORD=your_secure_password

# External Data Sources
WEATHER_API_KEY=your_weather_api_key
TRENDS_API_KEY=your_trends_api_key
```

---

## Development Workflow

### Git Branch Strategy

```
main (production)
  ├── develop (staging)
  │   ├── feature/product-management
  │   ├── feature/ai-predictions
  │   └── feature/permissions-system
  └── hotfix/critical-bug-fix
```

### Branch Naming Convention
- Features: `feature/feature-name`
- Bug fixes: `bugfix/bug-description`
- Hotfixes: `hotfix/issue-description`
- Releases: `release/v1.0.0`

### Commit Message Format
```
type(scope): subject

body (optional)

footer (optional)
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code formatting
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Example:**
```
feat(products): add bulk product upload functionality

Implemented CSV/Excel file parser for bulk product creation.
Added validation for file structure and data types.

Closes #123
```

---

## Testing

### Frontend Tests

```bash
cd frontend

# Run all tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run specific test file
npm test -- ProductList.test.jsx

# Run e2e tests
npm run test:e2e
```

### Backend Tests

```bash
cd backend

# Run PHPUnit tests
./vendor/bin/phpunit

# Run specific test class
./vendor/bin/phpunit tests/Unit/Services/ProductServiceTest.php

# Run with coverage
./vendor/bin/phpunit --coverage-html coverage
```

### ML Service Tests

```bash
cd ml-service
source venv/bin/activate

# Run pytest
pytest

# Run with coverage
pytest --cov=app --cov-report=html

# Run specific test file
pytest tests/test_models.py
```

---

## Database Management

### Migrations

#### Create New Migration
```bash
# Backend
cd backend
php scripts/create_migration.php "create_notifications_table"
```

#### Run Migrations
```bash
# Apply all pending migrations
php scripts/migrate.php

# Rollback last migration
php scripts/migrate.php --rollback

# Reset database (caution!)
php scripts/migrate.php --reset
```

### Backup and Restore

#### Backup Database
```bash
# Full backup
./scripts/database/backup.sh

# Backup to specific file
mysqldump -u mawsim_user -p mawsim_db > backup_$(date +%Y%m%d).sql
```

#### Restore Database
```bash
# Restore from backup
./scripts/database/restore.sh backup_20241217.sql

# Or manually
mysql -u mawsim_user -p mawsim_db < backup_20241217.sql
```

---

## Deployment

### Production Deployment Checklist

- [ ] Update environment variables
- [ ] Set `APP_ENV=production`
- [ ] Set `APP_DEBUG=false`
- [ ] Enable HTTPS/SSL
- [ ] Configure proper CORS origins
- [ ] Set strong JWT secrets
- [ ] Configure Redis for caching
- [ ] Set up database backups
- [ ] Configure log rotation
- [ ] Enable rate limiting
- [ ] Set up monitoring
- [ ] Configure CDN for assets
- [ ] Optimize database indexes
- [ ] Enable gzip compression
- [ ] Set up firewall rules

### Deploy to Production

```bash
# Pull latest code
git pull origin main

# Install dependencies
cd frontend && npm install --production
cd ../backend && composer install --no-dev --optimize-autoloader
cd ../ml-service && pip install -r requirements.txt

# Build frontend
cd frontend
npm run build

# Run migrations
cd ../backend
php scripts/migrate.php

# Clear caches
php scripts/clear_cache.php

# Restart services
sudo systemctl restart php-fpm
sudo systemctl restart nginx
pm2 restart ml-service
```

### Using Deployment Script

```bash
# Deploy to staging
./scripts/deployment/deploy_staging.sh

# Deploy to production
./scripts/deployment/deploy.sh

# Rollback deployment
./scripts/deployment/rollback.sh
```

---

## Monitoring and Logging

### Application Logs

```bash
# Backend logs
tail -f backend/storage/logs/app.log

# ML Service logs
tail -f ml-service/logs/app.log

# Nginx access logs
tail -f /var/log/nginx/access.log

# Nginx error logs
tail -f /var/log/nginx/error.log
```

### Health Checks

```bash
# Check backend health
curl http://localhost:8000/health

# Check ML service health
curl http://localhost:8001/health

# Check database connection
mysql -u mawsim_user -p -e "SELECT 1"

# Check Redis connection
redis-cli ping
```

### Performance Monitoring

```bash
# Monitor system resources
htop

# Check MySQL performance
mysql -u root -p -e "SHOW PROCESSLIST"

# Check Redis statistics
redis-cli info stats

# Monitor API response times
./scripts/utils/check_health.sh
```

---

## Troubleshooting

### Common Issues

#### Issue 1: CORS Errors
**Problem**: Frontend cannot connect to backend API

**Solution**:
```php
// backend/src/Middleware/CorsMiddleware.php
header('Access-Control-Allow-Origin: http://localhost:3000');
header('Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS');
header('Access-Control-Allow-Headers: Content-Type, Authorization');
```

#### Issue 2: Database Connection Failed
**Problem**: Cannot connect to MariaDB

**Solution**:
```bash
# Check if MariaDB is running
sudo systemctl status mariadb

# Start MariaDB
sudo systemctl start mariadb

# Check connection
mysql -u mawsim_user -p -e "SELECT 1"

# Verify credentials in .env file
```

#### Issue 3: JWT Token Invalid
**Problem**: Authentication fails with "Invalid token" error

**Solution**:
```bash
# Regenerate JWT secret
php -r "echo bin2hex(random_bytes(32));"

# Update JWT_SECRET in .env
# Clear all sessions/tokens
redis-cli FLUSHALL
```

#### Issue 4: ML Model Not Found
**Problem**: Prediction endpoint returns "Model not found"

**Solution**:
```bash
cd ml-service

# Train models
python scripts/train_models.py

# Verify model files exist
ls -la models/

# Check model path in .env
MODEL_PATH=./models
```

#### Issue 5: Slow Query Performance
**Problem**: Database queries taking too long

**Solution**:
```sql
-- Analyze slow queries
SHOW PROCESSLIST;

-- Check table indexes
SHOW INDEX FROM sales;

-- Optimize tables
OPTIMIZE TABLE sales, products, inventory;

-- Update table statistics
ANALYZE TABLE sales, products;
```

---

## Security Best Practices

### 1. Authentication & Authorization
- Use strong password hashing (Bcrypt/Argon2)
- Implement JWT with short expiration times
- Use refresh tokens for extended sessions
- Implement rate limiting on auth endpoints
- Enable CSRF protection

### 2. Data Protection
- Encrypt sensitive data at rest
- Use HTTPS/TLS for all communications
- Sanitize all user inputs
- Use prepared statements to prevent SQL injection
- Implement proper access controls

### 3. API Security
- Validate all API inputs
- Implement rate limiting
- Use API keys for external services
- Log all security events
- Implement request signing

### 4. Infrastructure Security
- Keep all software updated
- Use firewall rules
- Disable unused services
- Regular security audits
- Implement intrusion detection

---

## Performance Optimization

### Frontend Optimization

```javascript
// 1. Code splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));

// 2. Memoization
const ProductCard = memo(({ product }) => {
  // Component logic
});

// 3. Virtual scrolling for long lists
import { FixedSizeList } from 'react-window';

// 4. Image optimization
<img
  src={product.image}
  loading="lazy"
  srcSet={`${product.image_sm} 300w, ${product.image_md} 768w`}
/>
```

### Backend Optimization

```php
// 1. Database query optimization
$products = Product::with('inventory', 'sales')
    ->where('shop_id', $shopId)
    ->select(['id', 'name', 'price']) // Only needed columns
    ->limit(20)
    ->get();

// 2. Redis caching
$predictions = Cache::remember(
    "predictions:{$productId}",
    3600,
    fn() => Prediction::where('product_id', $productId)->get()
);

// 3. Eager loading to prevent N+1 queries
$shops = Shop::with(['products', 'sales'])->get();
```

### Database Optimization

```sql
-- 1. Add composite indexes for frequent queries
CREATE INDEX idx_sales_composite 
ON sales(shop_id, product_id, sale_date);

-- 2. Partition large tables
ALTER TABLE sales PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026)
);

-- 3. Use covering indexes
CREATE INDEX idx_covering 
ON products(shop_id, category, price, stock);
```

---

## API Usage Examples

### Authentication Example

```javascript
// Login
const login = async (email, password) => {
  const response = await fetch('http://localhost:8000/api/v1/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  
  const data = await response.json();
  
  if (data.success) {
    localStorage.setItem('token', data.data.token);
    return data.data;
  }
  
  throw new Error(data.message);
};
```

### Product Management Example

```javascript
// Create product
const createProduct = async (productData) => {
  const token = localStorage.getItem('token');
  
  const response = await fetch('http://localhost:8000/api/v1/merchant/products', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`
    },
    body: JSON.stringify(productData)
  });
  
  return await response.json();
};

// Upload CSV
const uploadProducts = async (file) => {
  const token = localStorage.getItem('token');
  const formData = new FormData();
  formData.append('file', file);
  
  const response = await fetch(
    'http://localhost:8000/api/v1/merchant/products/upload',
    {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${token}` },
      body: formData
    }
  );
  
  return await response.json();
};
```

### Prediction Example

```javascript
// Generate forecast
const generateForecast = async (productId) => {
  const token = localStorage.getItem('token');
  
  const response = await fetch(
    'http://localhost:8000/api/v1/predictions/forecast',
    {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({
        product_id: productId,
        forecast_horizon: '6_months',
        include_weather: true,
        include_trends: true
      })
    }
  );
  
  return await response.json();
};
```

---

## Maintenance Tasks

### Daily Tasks
- [ ] Check application logs for errors
- [ ] Monitor API response times
- [ ] Verify database backup completed
- [ ] Check disk space usage
- [ ] Review security alerts

### Weekly Tasks
- [ ] Analyze slow queries
- [ ] Review user feedback
- [ ] Update documentation
- [ ] Check for dependency updates
- [ ] Run security scans

### Monthly Tasks
- [ ] Database optimization
- [ ] Retrain ML models
- [ ] Review and rotate logs
- [ ] Performance audit
- [ ] Security audit

---

## Support and Resources

### Documentation
- API Documentation: http://localhost:8001/docs
- User Guide: `/docs/user_guides/`
- Developer Guide: `/docs/developer/`

### Community
- GitHub Issues: https://github.com/your-org/mawsim/issues
- Slack Channel: #mawsim-support
- Email Support: support@mawsim.sa

### Useful Commands

```bash
# Clear all caches
./scripts/utils/clear_cache.sh

# Generate API documentation
cd backend && php scripts/generate_api_docs.php

# Run code quality checks
cd frontend && npm run lint
cd backend && ./vendor/bin/phpcs

# Generate test coverage report
npm test -- --coverage
```

---

**Implementation Guide Version**: 1.0  
**Last Updated**: December 2024  
**Project**: Mawsim - AI Seasonal Demand Forecasting
