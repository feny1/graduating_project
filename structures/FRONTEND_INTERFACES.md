# Mawsim Frontend Interface Specifications

## Design System Overview

### Color Palette
```css
/* Primary Colors (Saudi Brand Identity) */
--primary-green: #006C35;      /* Saudi flag green */
--primary-gold: #FFD700;        /* Gold accent */
--primary-dark: #004D26;        /* Dark green */
--primary-light: #E8F5E9;       /* Light green background */

/* Semantic Colors */
--success: #4CAF50;
--warning: #FF9800;
--error: #F44336;
--info: #2196F3;

/* Neutral Colors */
--text-primary: #212121;
--text-secondary: #757575;
--text-disabled: #BDBDBD;
--background: #FAFAFA;
--surface: #FFFFFF;
--divider: #E0E0E0;

/* Seasonal Colors */
--ramadan-purple: #6A1B9A;
--eid-gold: #F9A825;
--hajj-blue: #1565C0;
```

### Typography
```css
/* Arabic Font (Primary) */
--font-arabic: 'Noto Sans Arabic', 'Tajawal', sans-serif;

/* English Font (Secondary) */
--font-english: 'Inter', 'Roboto', sans-serif;

/* Font Sizes */
--text-xs: 12px;
--text-sm: 14px;
--text-base: 16px;
--text-lg: 18px;
--text-xl: 20px;
--text-2xl: 24px;
--text-3xl: 30px;
--text-4xl: 36px;

/* Font Weights */
--font-light: 300;
--font-regular: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

### Spacing System
```css
--spacing-xs: 4px;
--spacing-sm: 8px;
--spacing-md: 16px;
--spacing-lg: 24px;
--spacing-xl: 32px;
--spacing-2xl: 48px;
--spacing-3xl: 64px;
```

### Breakpoints
```css
--screen-mobile: 320px;
--screen-tablet: 768px;
--screen-desktop: 1024px;
--screen-wide: 1440px;
```

---

## Page Interfaces

### 1. Login Page (REQ-002)

**Route**: `/login`

**Layout**:
```
┌─────────────────────────────────────┐
│          [Mawsim Logo]              │
│                                     │
│     ┌─────────────────────────┐    │
│     │  Login to Your Account  │    │
│     │                         │    │
│     │  [Email Input Field]    │    │
│     │  [Password Input Field] │    │
│     │                         │    │
│     │  [ ] Remember Me        │    │
│     │                         │    │
│     │  [Login Button]         │    │
│     │                         │    │
│     │  Forgot Password?       │    │
│     │  Don't have account?    │    │
│     │  [Register]             │    │
│     └─────────────────────────┘    │
│                                     │
│  [Language Switcher: AR | EN]      │
└─────────────────────────────────────┘
```

**Components**:
```jsx
<LoginPage>
  <Container maxWidth="sm">
    <Logo />
    <Card elevation={3}>
      <CardContent>
        <Typography variant="h4">تسجيل الدخول</Typography>
        
        <Form onSubmit={handleLogin}>
          <TextField
            label="البريد الإلكتروني"
            type="email"
            required
            fullWidth
          />
          
          <TextField
            label="كلمة المرور"
            type="password"
            required
            fullWidth
          />
          
          <FormControlLabel
            control={<Checkbox />}
            label="تذكرني"
          />
          
          <Button
            type="submit"
            variant="contained"
            fullWidth
            loading={isLoading}
          >
            تسجيل الدخول
          </Button>
          
          <Link href="/forgot-password">
            نسيت كلمة المرور؟
          </Link>
          
          <Divider />
          
          <Link href="/register">
            ليس لديك حساب؟ سجل الآن
          </Link>
        </Form>
      </CardContent>
    </Card>
    
    <LanguageSwitcher />
  </Container>
</LoginPage>
```

**Validation Rules**:
- Email: Must be valid email format
- Password: Required, minimum 8 characters
- Show error messages below each field
- Disable submit button during API call

**Error Handling**:
```jsx
{error && (
  <Alert severity="error">
    {error === 'invalid_credentials' 
      ? 'البريد الإلكتروني أو كلمة المرور غير صحيحة'
      : 'حدث خطأ. يرجى المحاولة مرة أخرى'}
  </Alert>
)}
```

---

### 2. Register Page (REQ-001)

**Route**: `/register`

**Layout**:
```
┌──────────────────────────────────────┐
│      Create Merchant Account         │
│                                      │
│  Step 1: Personal Information        │
│  ├─ Full Name                        │
│  ├─ Email                            │
│  ├─ Password                         │
│  └─ Confirm Password                 │
│                                      │
│  Step 2: Business Information        │
│  ├─ Business Name                    │
│  ├─ Business Type                    │
│  ├─ Location                         │
│  └─ Phone Number                     │
│                                      │
│  [Previous] [Next/Register]          │
└──────────────────────────────────────┘
```

**Multi-Step Form**:
```jsx
<RegisterPage>
  <Stepper activeStep={currentStep}>
    <Step>
      <StepLabel>المعلومات الشخصية</StepLabel>
    </Step>
    <Step>
      <StepLabel>معلومات العمل</StepLabel>
    </Step>
  </Stepper>
  
  {currentStep === 0 && (
    <PersonalInfoForm>
      <TextField name="name" label="الاسم الكامل" required />
      <TextField name="email" label="البريد الإلكتروني" type="email" required />
      <TextField name="password" label="كلمة المرور" type="password" required />
      <TextField name="password_confirmation" label="تأكيد كلمة المرور" type="password" required />
    </PersonalInfoForm>
  )}
  
  {currentStep === 1 && (
    <BusinessInfoForm>
      <TextField name="business_name" label="اسم المتجر" required />
      <Select name="business_type" label="نوع العمل">
        <MenuItem value="supermarket">سوبر ماركت</MenuItem>
        <MenuItem value="hypermarket">هايبر ماركت</MenuItem>
      </Select>
      <TextField name="location" label="الموقع" required />
      <TextField name="phone" label="رقم الهاتف" />
    </BusinessInfoForm>
  )}
  
  <Box display="flex" justifyContent="space-between">
    <Button onClick={handleBack} disabled={currentStep === 0}>
      السابق
    </Button>
    <Button onClick={handleNext} variant="contained">
      {currentStep === 1 ? 'إنشاء الحساب' : 'التالي'}
    </Button>
  </Box>
</RegisterPage>
```

**Success Message**:
After registration, show success modal:
```jsx
<Modal open={registrationSuccess}>
  <Box>
    <CheckCircleIcon color="success" fontSize="large" />
    <Typography variant="h5">
      تم إنشاء الحساب بنجاح!
    </Typography>
    <Typography>
      يمكنك الآن تسجيل الدخول باستخدام بريدك الإلكتروني.
    </Typography>
    <Button onClick={() => navigate('/login')}>
      تسجيل الدخول
    </Button>
  </Box>
</Modal>
```

---

### 3. Merchant Dashboard (Main)

**Route**: `/merchant/dashboard`

**Layout**:
```
┌─────────────────────────────────────────────────────────┐
│  [☰] Mawsim  [Search]        [🔔] [👤 Ahmed] [⚙]     │
├─────────────────────────────────────────────────────────┤
│ │                                                       │
│ │  Quick Stats                                         │
│S│  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐       │
│i│  │Products│ │ Sales  │ │Forecast│ │Alerts  │       │
│d│  │  245   │ │ 12,450 │ │ +15%   │ │   3    │       │
│e│  └────────┘ └────────┘ └────────┘ └────────┘       │
│b│                                                       │
│a│  Upcoming Seasonal Events                            │
│r│  ┌─────────────────────────────────────────────┐   │
│ │  │ 🌙 Ramadan 1446 - في 74 يوم               │   │
│ │  │ Action: زيادة مخزون التمور والعصائر       │   │
│ │  └─────────────────────────────────────────────┘   │
│ │                                                       │
│ │  Recent Predictions                                  │
│ │  ┌─────────────────────────────────────────────┐   │
│ │  │ Product      │ Season   │ Demand  │ Action  │   │
│ │  ├──────────────┼──────────┼─────────┼─────────┤   │
│ │  │ Vimto 1L     │ Ramadan  │ ⬆ 350% │ [View]  │   │
│ │  │ Dates Box    │ Ramadan  │ ⬆ 280% │ [View]  │   │
│ │  └─────────────────────────────────────────────┘   │
│ │                                                       │
│ │  Sales Performance Chart                             │
│ │  [Interactive Chart showing monthly trends]          │
└─────────────────────────────────────────────────────────┘
```

**Sidebar Navigation**:
```jsx
<Sidebar>
  <List>
    <ListItem button selected>
      <DashboardIcon />
      <ListItemText primary="لوحة التحكم" />
    </ListItem>
    
    <ListItem button onClick={() => navigate('/merchant/products')}>
      <InventoryIcon />
      <ListItemText primary="المنتجات" />
    </ListItem>
    
    <ListItem button onClick={() => navigate('/merchant/timeline')}>
      <TimelineIcon />
      <ListItemText primary="الجدول الزمني" />
    </ListItem>
    
    <ListItem button onClick={() => navigate('/merchant/analytics')}>
      <AnalyticsIcon />
      <ListItemText primary="التحليلات" />
    </ListItem>
    
    <Divider />
    
    <ListItem button onClick={() => navigate('/merchant/settings')}>
      <SettingsIcon />
      <ListItemText primary="الإعدادات" />
    </ListItem>
  </List>
</Sidebar>
```

**Quick Stats Widgets**:
```jsx
<Grid container spacing={3}>
  <Grid item xs={12} sm={6} md={3}>
    <StatCard
      title="إجمالي المنتجات"
      value="245"
      icon={<InventoryIcon />}
      color="primary"
    />
  </Grid>
  
  <Grid item xs={12} sm={6} md={3}>
    <StatCard
      title="المبيعات اليومية"
      value="12,450 ر.س"
      trend="+8.5%"
      icon={<TrendingUpIcon />}
      color="success"
    />
  </Grid>
  
  <Grid item xs={12} sm={6} md={3}>
    <StatCard
      title="التوقعات القادمة"
      value="+15%"
      subtitle="زيادة متوقعة"
      icon={<ForecastIcon />}
      color="info"
    />
  </Grid>
  
  <Grid item xs={12} sm={6} md={3}>
    <StatCard
      title="التنبيهات"
      value="3"
      subtitle="تحتاج لانتباه"
      icon={<NotificationsIcon />}
      color="warning"
    />
  </Grid>
</Grid>
```

---

### 4. Products Page (REQ-009, REQ-010, REQ-011, REQ-012)

**Route**: `/merchant/products`

**Layout**:
```
┌─────────────────────────────────────────────────────────┐
│  المنتجات                                   [+ إضافة]  │
├─────────────────────────────────────────────────────────┤
│  [🔍 Search...]  [Filter ▼]  [Sort ▼]  [📤 Upload CSV] │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐  │
│  │ [Image]  │ │ [Image]  │ │ [Image]  │ │ [Image]  │  │
│  │ Vimto 1L │ │ Dates Box│ │ Rice 5kg │ │ Flour    │  │
│  │ 15.50 SR │ │ 45.00 SR │ │ 32.00 SR │ │ 18.00 SR │  │
│  │ Stock:500│ │ Stock:200│ │ Stock:150│ │ Stock:300│  │
│  │ [Edit]🗑  │ │ [Edit]🗑  │ │ [Edit]🗑  │ │ [Edit]🗑  │  │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘  │
│                                                          │
│  [Pagination: 1 2 3 ... 10]                            │
└─────────────────────────────────────────────────────────┘
```

**Product Card Component**:
```jsx
<Card className="product-card">
  <CardMedia
    component="img"
    height="200"
    image={product.image_url || '/placeholder.png'}
    alt={product.product_name}
  />
  
  <CardContent>
    <Typography variant="h6" noWrap>
      {product.product_name}
    </Typography>
    
    <Typography variant="body2" color="text.secondary">
      {product.category}
    </Typography>
    
    <Box display="flex" justifyContent="space-between" mt={2}>
      <Typography variant="h5" color="primary">
        {product.price} ر.س
      </Typography>
      
      <Chip
        label={`المخزون: ${product.stock}`}
        color={product.stock > 50 ? 'success' : 'warning'}
        size="small"
      />
    </Box>
    
    {product.seasonal_tag && (
      <Chip
        label={product.seasonal_tag}
        icon={<EventIcon />}
        color="secondary"
        size="small"
      />
    )}
  </CardContent>
  
  <CardActions>
    <IconButton onClick={() => handleEdit(product.product_id)}>
      <EditIcon />
    </IconButton>
    <IconButton onClick={() => handleDelete(product.product_id)} color="error">
      <DeleteIcon />
    </IconButton>
    <Button size="small" onClick={() => handleViewDetails(product.product_id)}>
      عرض التفاصيل
    </Button>
  </CardActions>
</Card>
```

**Search & Filter Bar**:
```jsx
<Toolbar>
  <TextField
    placeholder="ابحث عن منتج..."
    value={searchQuery}
    onChange={handleSearch}
    InputProps={{
      startAdornment: <SearchIcon />
    }}
    fullWidth
  />
  
  <Button
    variant="outlined"
    startIcon={<FilterListIcon />}
    onClick={handleOpenFilters}
  >
    تصفية
  </Button>
  
  <Select value={sortBy} onChange={handleSort}>
    <MenuItem value="name">الاسم</MenuItem>
    <MenuItem value="price_asc">السعر (الأقل أولاً)</MenuItem>
    <MenuItem value="price_desc">السعر (الأعلى أولاً)</MenuItem>
    <MenuItem value="stock">المخزون</MenuItem>
  </Select>
  
  <Button
    variant="contained"
    startIcon={<UploadIcon />}
    onClick={handleUploadCSV}
  >
    رفع CSV
  </Button>
</Toolbar>
```

**Filter Drawer**:
```jsx
<Drawer anchor="right" open={filtersOpen} onClose={handleCloseFilters}>
  <Box p={3} width={300}>
    <Typography variant="h6">تصفية المنتجات</Typography>
    
    <FormControl fullWidth margin="normal">
      <InputLabel>الفئة</InputLabel>
      <Select multiple value={selectedCategories} onChange={handleCategoryChange}>
        <MenuItem value="beverages">مشروبات</MenuItem>
        <MenuItem value="food">طعام</MenuItem>
        <MenuItem value="dairy">ألبان</MenuItem>
      </Select>
    </FormControl>
    
    <FormControl fullWidth margin="normal">
      <InputLabel>الموسم</InputLabel>
      <Select multiple value={selectedSeasons} onChange={handleSeasonChange}>
        <MenuItem value="ramadan">رمضان</MenuItem>
        <MenuItem value="eid">عيد</MenuItem>
        <MenuItem value="hajj">حج</MenuItem>
      </Select>
    </FormControl>
    
    <Typography gutterBottom>نطاق السعر</Typography>
    <Slider
      value={priceRange}
      onChange={handlePriceChange}
      valueLabelDisplay="auto"
      min={0}
      max={500}
    />
    
    <FormControl fullWidth margin="normal">
      <InputLabel>حالة المخزون</InputLabel>
      <Select value={stockStatus} onChange={handleStockChange}>
        <MenuItem value="all">الكل</MenuItem>
        <MenuItem value="in_stock">متوفر</MenuItem>
        <MenuItem value="low_stock">مخزون منخفض</MenuItem>
        <MenuItem value="out_of_stock">غير متوفر</MenuItem>
      </Select>
    </FormControl>
    
    <Box mt={3} display="flex" gap={2}>
      <Button variant="outlined" onClick={handleResetFilters} fullWidth>
        إعادة تعيين
      </Button>
      <Button variant="contained" onClick={handleApplyFilters} fullWidth>
        تطبيق
      </Button>
    </Box>
  </Box>
</Drawer>
```

---

### 5. Product Details Page (REQ-010)

**Route**: `/merchant/products/:id`

**Layout**:
```jsx
<Container maxWidth="lg">
  <Grid container spacing={4}>
    <Grid item xs={12} md={6}>
      <Box>
        <img
          src={product.image_url}
          alt={product.product_name}
          style={{ width: '100%', borderRadius: 8 }}
        />
        
        <Box mt={2} display="flex" gap={1}>
          {/* Thumbnail images if multiple available */}
        </Box>
      </Box>
    </Grid>
    
    <Grid item xs={12} md={6}>
      <Typography variant="h3">{product.product_name}</Typography>
      
      <Box display="flex" alignItems="center" gap={2} my={2}>
        <Typography variant="h4" color="primary">
          {product.price} ر.س
        </Typography>
        
        <Chip
          label={`المخزون: ${product.stock}`}
          color={product.stock > 50 ? 'success' : 'warning'}
        />
        
        {product.seasonal_tag && (
          <Chip
            label={product.seasonal_tag}
            icon={<EventIcon />}
            color="secondary"
          />
        )}
      </Box>
      
      <Divider />
      
      <Box my={3}>
        <Typography variant="h6">تفاصيل المنتج</Typography>
        <List>
          <ListItem>
            <ListItemText
              primary="الفئة"
              secondary={product.category}
            />
          </ListItem>
          <ListItem>
            <ListItemText
              primary="الوصف"
              secondary={product.description}
            />
          </ListItem>
          <ListItem>
            <ListItemText
              primary="تاريخ الإضافة"
              secondary={formatDate(product.created_at)}
            />
          </ListItem>
        </List>
      </Box>
      
      <Box display="flex" gap={2}>
        <Button
          variant="contained"
          startIcon={<EditIcon />}
          onClick={handleEdit}
          fullWidth
        >
          تعديل المنتج
        </Button>
        <Button
          variant="outlined"
          color="error"
          startIcon={<DeleteIcon />}
          onClick={handleDelete}
        >
          حذف
        </Button>
      </Box>
    </Grid>
    
    {/* Demand History Chart */}
    <Grid item xs={12}>
      <Card>
        <CardHeader title="تاريخ الطلب" />
        <CardContent>
          <LineChart
            data={product.demand_history}
            xAxis="month"
            yAxis="sales"
            height={300}
          />
        </CardContent>
      </Card>
    </Grid>
  </Grid>
</Container>
```

---

### 6. Product Status Timeline (REQ-003)

**Route**: `/merchant/timeline/:productId`

**Layout**:
```jsx
<Container maxWidth="xl">
  <Box mb={4}>
    <Typography variant="h4">الجدول الزمني لحالة المنتج</Typography>
    <Typography variant="body1" color="text.secondary">
      {selectedProduct.product_name}
    </Typography>
  </Box>
  
  {/* Timeline Visualization */}
  <Card elevation={3}>
    <CardContent>
      <Box position="relative" height={400}>
        {/* Horizontal Timeline Chart */}
        <ResponsiveContainer width="100%" height="100%">
          <ComposedChart data={timelineData}>
            <CartesianGrid strokeDasharray="3 3" />
            <XAxis
              dataKey="period"
              label={{ value: 'الفترة الزمنية', position: 'insideBottom', offset: -5 }}
            />
            <YAxis
              label={{ value: 'مستوى الطلب', angle: -90, position: 'insideLeft' }}
            />
            <Tooltip content={<CustomTooltip />} />
            <Legend />
            
            {/* Demand Line */}
            <Line
              type="monotone"
              dataKey="demand_level"
              stroke="#006C35"
              strokeWidth={3}
              dot={{ fill: '#006C35', r: 6 }}
            />
            
            {/* Peak Indicators */}
            <Area
              type="monotone"
              dataKey="isPeak"
              fill="#FFD700"
              fillOpacity={0.3}
            />
            
            {/* Season Markers */}
            {seasonMarkers.map((marker, index) => (
              <ReferenceLine
                key={index}
                x={marker.period}
                stroke="#6A1B9A"
                strokeDasharray="5 5"
                label={{
                  value: marker.season,
                  position: 'top',
                  fill: '#6A1B9A'
                }}
              />
            ))}
          </ComposedChart>
        </ResponsiveContainer>
      </Box>
      
      {/* Legend */}
      <Box mt={3} display="flex" justifyContent="center" gap={3}>
        <Chip
          icon={<CircleIcon style={{ color: '#FFD700' }} />}
          label="ذروة الموسم"
          variant="outlined"
        />
        <Chip
          icon={<CircleIcon style={{ color: '#006C35' }} />}
          label="طلب عادي"
          variant="outlined"
        />
        <Chip
          icon={<CircleIcon style={{ color: '#F44336' }} />}
          label="انخفاض الطلب"
          variant="outlined"
        />
      </Box>
    </CardContent>
  </Card>
  
  {/* Current Status Card */}
  <Grid container spacing={3} mt={2}>
    <Grid item xs={12} md={4}>
      <Card>
        <CardHeader title="الحالة الحالية" />
        <CardContent>
          <Box textAlign="center">
            <Typography variant="h3" color="primary">
              {currentStatus.demand_level}
            </Typography>
            <Typography variant="body2" color="text.secondary">
              مستوى الطلب الحالي
            </Typography>
          </Box>
        </CardContent>
      </Card>
    </Grid>
    
    <Grid item xs={12} md={4}>
      <Card>
        <CardHeader title="الذروة القادمة" />
        <CardContent>
          <Box textAlign="center">
            <Typography variant="h5" color="secondary">
              {nextPeak.season}
            </Typography>
            <Typography variant="body2" color="text.secondary">
              {nextPeak.hijri_date}
            </Typography>
            <Chip
              label={`بعد ${nextPeak.days_until} يوم`}
              color="warning"
              style={{ marginTop: 8 }}
            />
          </Box>
        </CardContent>
      </Card>
    </Grid>
    
    <Grid item xs={12} md={4}>
      <Card>
        <CardHeader title="التوصية" />
        <CardContent>
          <Alert severity="info">
            <Typography variant="body2">
              يُنصح بزيادة المخزون بنسبة 350% قبل أسبوعين من الموسم
            </Typography>
          </Alert>
        </CardContent>
      </Card>
    </Grid>
  </Grid>
  
  {/* Historical Data Table */}
  <Card mt={3}>
    <CardHeader title="البيانات التاريخية" />
    <CardContent>
      <TableContainer>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>الفترة</TableCell>
              <TableCell>الموسم</TableCell>
              <TableCell>الشهر الهجري</TableCell>
              <TableCell>حجم المبيعات</TableCell>
              <TableCell>مستوى الطلب</TableCell>
              <TableCell>الثقة</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {timelineData.map((row, index) => (
              <TableRow key={index}>
                <TableCell>{row.period}</TableCell>
                <TableCell>{row.season}</TableCell>
                <TableCell>{row.hijri_month}</TableCell>
                <TableCell>{row.sales_volume}</TableCell>
                <TableCell>
                  <Chip
                    label={row.demand_level}
                    color={row.status === 'Peak' ? 'warning' : 'default'}
                    size="small"
                  />
                </TableCell>
                <TableCell>{(row.confidence * 100).toFixed(0)}%</TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>
    </CardContent>
  </Card>
</Container>
```

**Performance Note**: Must load within 2-4 seconds (REQ-2.1.2.3.4)

---

### 7. AI Forecasting Interface (REQ-004)

**Route**: `/merchant/forecast/:productId`

**Layout**:
```jsx
<Container maxWidth="lg">
  <Box mb={4} display="flex" justifyContent="space-between" alignItems="center">
    <Typography variant="h4">التنبؤ الموسمي بالذكاء الاصطناعي</Typography>
    <Button
      variant="contained"
      startIcon={<RefreshIcon />}
      onClick={handleGenerateForecast}
      disabled={isGenerating}
    >
      {isGenerating ? 'جارٍ التوليد...' : 'توليد توقع جديد'}
    </Button>
  </Box>
  
  {isGenerating && (
    <Box textAlign="center" py={8}>
      <CircularProgress size={60} />
      <Typography variant="h6" mt={2}>
        جارٍ تحليل البيانات وإنشاء التوقعات...
      </Typography>
      <Typography variant="body2" color="text.secondary">
        هذا قد يستغرق 6-10 ثوانٍ
      </Typography>
    </Box>
  )}
  
  {forecast && !isGenerating && (
    <>
      {/* Forecast Summary Cards */}
      <Grid container spacing={3} mb={4}>
        <Grid item xs={12} md={3}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                الكمية الموصى بها
              </Typography>
              <Typography variant="h3" color="primary">
                {forecast.recommended_quantity}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                وحدة
              </Typography>
            </CardContent>
          </Card>
        </Grid>
        
        <Grid item xs={12} md={3}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                أفضل وقت للتخزين
              </Typography>
              <Typography variant="h4" color="secondary">
                {formatDate(forecast.best_time_to_stock)}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                {formatHijriDate(forecast.best_time_to_stock)}
              </Typography>
            </CardContent>
          </Card>
        </Grid>
        
        <Grid item xs={12} md={3}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                دقة النموذج
              </Typography>
              <Box display="flex" alignItems="center">
                <Typography variant="h3" color="success.main">
                  {(forecast.model_accuracy * 100).toFixed(0)}%
                </Typography>
                <CheckCircleIcon color="success" sx={{ ml: 1 }} />
              </Box>
            </CardContent>
          </Card>
        </Grid>
        
        <Grid item xs={12} md={3}>
          <Card>
            <CardContent>
              <Typography variant="h6" gutterBottom>
                تاريخ التوليد
              </Typography>
              <Typography variant="body1">
                {formatDateTime(forecast.generated_at)}
              </Typography>
            </CardContent>
          </Card>
        </Grid>
      </Grid>
      
      {/* Forecast Chart */}
      <Card elevation={3} mb={3}>
        <CardHeader title="توقعات الطلب للأشهر القادمة" />
        <CardContent>
          <ResponsiveContainer width="100%" height={400}>
            <ComposedChart data={forecast.forecast}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey="period" />
              <YAxis />
              <Tooltip content={<CustomForecastTooltip />} />
              <Legend />
              
              {/* Predicted Demand */}
              <Line
                type="monotone"
                dataKey="predicted_demand"
                stroke="#006C35"
                strokeWidth={2}
                name="الطلب المتوقع"
              />
              
              {/* Confidence Interval */}
              <Area
                type="monotone"
                dataKey="confidence_interval.upper"
                fill="#006C35"
                fillOpacity={0.1}
                stroke="none"
                name="الحد الأعلى"
              />
              <Area
                type="monotone"
                dataKey="confidence_interval.lower"
                fill="#006C35"
                fillOpacity={0.1}
                stroke="none"
                name="الحد الأدنى"
              />
              
              {/* Season Indicators */}
              {forecast.forecast
                .filter(f => f.season !== 'Regular')
                .map((season, idx) => (
                  <ReferenceDot
                    key={idx}
                    x={season.period}
                    y={season.predicted_demand}
                    r={10}
                    fill="#FFD700"
                    stroke="#F9A825"
                  />
                ))}
            </ComposedChart>
          </ResponsiveContainer>
        </CardContent>
      </Card>
      
      {/* Detailed Forecast Table */}
      <Card>
        <CardHeader title="تفاصيل التوقعات الشهرية" />
        <CardContent>
          <TableContainer>
            <Table>
              <TableHead>
                <TableRow>
                  <TableCell>الفترة</TableCell>
                  <TableCell>الموسم</TableCell>
                  <TableCell>الطلب المتوقع</TableCell>
                  <TableCell>نطاق الثقة</TableCell>
                  <TableCell>التوصية</TableCell>
                </TableRow>
              </TableHead>
              <TableBody>
                {forecast.forecast.map((row, index) => (
                  <TableRow
                    key={index}
                    style={{
                      backgroundColor: row.season !== 'Regular' ? '#FFF3E0' : 'white'
                    }}
                  >
                    <TableCell>{row.period}</TableCell>
                    <TableCell>
                      <Chip
                        label={row.season}
                        color={row.season !== 'Regular' ? 'warning' : 'default'}
                        size="small"
                      />
                    </TableCell>
                    <TableCell>
                      <Typography variant="h6">
                        {row.predicted_demand}
                      </Typography>
                    </TableCell>
                    <TableCell>
                      <Typography variant="body2" color="text.secondary">
                        {row.confidence_interval.lower} - {row.confidence_interval.upper}
                      </Typography>
                    </TableCell>
                    <TableCell>
                      <Alert severity="info" variant="outlined">
                        {row.recommendation}
                      </Alert>
                    </TableCell>
                  </TableRow>
                ))}
              </TableBody>
            </Table>
          </TableContainer>
        </CardContent>
      </Card>
      
      {/* Export Options */}
      <Box mt={3} display="flex" gap={2}>
        <Button
          variant="outlined"
          startIcon={<DownloadIcon />}
          onClick={() => handleExport('pdf')}
        >
          تصدير كـ PDF
        </Button>
        <Button
          variant="outlined"
          startIcon={<DownloadIcon />}
          onClick={() => handleExport('excel')}
        >
          تصدير كـ Excel
        </Button>
      </Box>
    </>
  )}
</Container>
```

**Performance Requirement**: Must generate within 6-10 seconds (REQ-2.1.2.3.4)

---

### 8. Admin Permissions Page (REQ-013 to REQ-018)

**Route**: `/admin/permissions`

**Layout**:
```jsx
<Container maxWidth="xl">
  <Box mb={4} display="flex" justifyContent="space-between" alignItems="center">
    <Typography variant="h4">إدارة الصلاحيات</Typography>
    <Button
      variant="contained"
      startIcon={<AddIcon />}
      onClick={handleRegisterEmployee}
    >
      إضافة موظف جديد
    </Button>
  </Box>
  
  {/* Employees List */}
  <Card>
    <CardContent>
      <TableContainer>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>اسم الموظف</TableCell>
              <TableCell>البريد الإلكتروني</TableCell>
              <TableCell>عدد الصلاحيات</TableCell>
              <TableCell>الحالة</TableCell>
              <TableCell>الإجراءات</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {employees.map((employee) => (
              <TableRow key={employee.user_id}>
                <TableCell>
                  <Box display="flex" alignItems="center">
                    <Avatar src={employee.avatar} sx={{ mr: 2 }}>
                      {employee.name.charAt(0)}
                    </Avatar>
                    <Typography>{employee.name}</Typography>
                  </Box>
                </TableCell>
                <TableCell>{employee.email}</TableCell>
                <TableCell>
                  <Chip
                    label={`${employee.permissions_count} صلاحيات`}
                    color="primary"
                    size="small"
                  />
                </TableCell>
                <TableCell>
                  <Chip
                    label={employee.status === 'active' ? 'نشط' : 'غير نشط'}
                    color={employee.status === 'active' ? 'success' : 'default'}
                    size="small"
                  />
                </TableCell>
                <TableCell>
                  <IconButton
                    onClick={() => handleViewPermissions(employee.user_id)}
                    title="عرض الصلاحيات"
                  >
                    <VisibilityIcon />
                  </IconButton>
                  <IconButton
                    onClick={() => handleEditPermissions(employee.user_id)}
                    title="تعديل الصلاحيات"
                  >
                    <EditIcon />
                  </IconButton>
                  <IconButton
                    onClick={() => handleRemovePermissions(employee.user_id)}
                    color="error"
                    title="إزالة الصلاحيات"
                  >
                    <DeleteIcon />
                  </IconButton>
                </TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>
    </CardContent>
  </Card>
  
  {/* Employee Permission Modal */}
  <Dialog
    open={permissionModalOpen}
    onClose={handleClosePermissionModal}
    maxWidth="md"
    fullWidth
  >
    <DialogTitle>
      إدارة صلاحيات: {selectedEmployee?.name}
    </DialogTitle>
    <DialogContent>
      <Typography variant="subtitle2" gutterBottom>
        اختر الصلاحيات المسموح بها:
      </Typography>
      
      {permissionCategories.map((category) => (
        <Box key={category.name} mb={3}>
          <Typography variant="h6" gutterBottom>
            {category.label}
          </Typography>
          <FormGroup>
            {category.permissions.map((perm) => (
              <FormControlLabel
                key={perm.permission_id}
                control={
                  <Checkbox
                    checked={selectedPermissions.includes(perm.permission_id)}
                    onChange={(e) => handlePermissionToggle(perm.permission_id, e.target.checked)}
                  />
                }
                label={
                  <Box>
                    <Typography variant="body1">{perm.name}</Typography>
                    <Typography variant="caption" color="text.secondary">
                      {perm.description}
                    </Typography>
                  </Box>
                }
              />
            ))}
          </FormGroup>
          <Divider sx={{ mt: 2 }} />
        </Box>
      ))}
    </DialogContent>
    <DialogActions>
      <Button onClick={handleClosePermissionModal}>
        إلغاء
      </Button>
      <Button
        onClick={handleSavePermissions}
        variant="contained"
        startIcon={<SaveIcon />}
      >
        حفظ التغييرات
      </Button>
    </DialogActions>
  </Dialog>
  
  {/* Register Employee Modal (REQ-013) */}
  <Dialog
    open={registerModalOpen}
    onClose={handleCloseRegisterModal}
    maxWidth="sm"
    fullWidth
  >
    <DialogTitle>إضافة موظف جديد</DialogTitle>
    <DialogContent>
      <TextField
        label="الاسم الكامل"
        name="name"
        fullWidth
        margin="normal"
        required
      />
      <TextField
        label="البريد الإلكتروني"
        name="email"
        type="email"
        fullWidth
        margin="normal"
        required
      />
      <TextField
        label="كلمة المرور"
        name="password"
        type="password"
        fullWidth
        margin="normal"
        required
      />
      <TextField
        label="رقم الهاتف"
        name="phone"
        fullWidth
        margin="normal"
      />
      <TextField
        label="القسم"
        name="department"
        fullWidth
        margin="normal"
      />
    </DialogContent>
    <DialogActions>
      <Button onClick={handleCloseRegisterModal}>
        إلغاء
      </Button>
      <Button
        onClick={handleCreateEmployee}
        variant="contained"
      >
        إنشاء الحساب
      </Button>
    </DialogActions>
  </Dialog>
</Container>
```

---

## Responsive Design Breakpoints

### Mobile (320px - 767px)
- Single column layout
- Sidebar collapses to hamburger menu
- Product cards stack vertically
- Charts adjust to mobile-friendly format
- Font sizes scale down appropriately

### Tablet (768px - 1023px)
- Two-column grid for product cards
- Sidebar shows icons only
- Charts remain full width

### Desktop (1024px+)
- Three to four column grid for products
- Full sidebar with text labels
- Multi-column dashboard layout
- Charts at optimal size

---

## Accessibility (WCAG 2.1 Level AA)

### Color Contrast
- Text: Minimum 4.5:1 ratio
- Large text: Minimum 3:1 ratio
- Interactive elements: Clear focus indicators

### Keyboard Navigation
- All interactive elements accessible via Tab
- Logical tab order
- Skip navigation links
- Keyboard shortcuts for common actions

### Screen Readers
- Proper ARIA labels
- Alt text for all images
- Semantic HTML structure
- Form labels properly associated

### Arabic RTL Support
- Proper text direction (`dir="rtl"`)
- Mirrored layouts
- Right-aligned text
- RTL-aware icons and navigation

---

## Performance Optimization

### Code Splitting
```javascript
// Lazy load pages
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Products = lazy(() => import('./pages/Products'));
const Timeline = lazy(() => import('./pages/Timeline'));
```

### Image Optimization
- Lazy loading for images
- Responsive images with srcset
- WebP format with fallback
- Image compression

### Caching Strategy
- Service Worker for offline support
- LocalStorage for user preferences
- IndexedDB for large datasets
- Cache API responses

---

**UI/UX Version**: 1.0  
**Last Updated**: December 2024  
**Design System**: Mawsim Design System
