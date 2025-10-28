# Airbnb Clone Backend – Requirement Specifications

This document outlines the **technical and functional requirements** for the core backend features of the Airbnb Clone project.  
It covers three key features:
1. **User Authentication**
2. **Property Management**
3. **Booking System**

---

## 1. 🧑‍💻 User Authentication

### **Overview**
Handles secure user registration, login, and session management for Guests, Hosts, and Admins.

### **API Endpoints**

| Method | Endpoint | Description |
|:-------|:----------|:-------------|
| `POST` | `/api/auth/register` | Register a new user (Guest or Host). |
| `POST` | `/api/auth/login` | Authenticate user and return JWT token. |
| `GET` | `/api/auth/profile` | Retrieve logged-in user profile (JWT required). |
| `PUT` | `/api/auth/profile` | Update user profile details. |

### **Input Specifications**

#### `/api/auth/register`
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com",
  "password": "Secret123!",
  "phone_number": "+233501234567",
  "role": "host"
}
```

#### `/api/auth/login`
```json
{
  "email": "john@example.com",
  "password": "Secret123!"
}
```

#### `/api/auth/profile` (PUT)
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "phone_number": "+233501234567"
}
```

### **Output Specifications**

#### **Successful Registration**
```json
{
  "status": "success",
  "message": "User registered successfully",
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "role": "host",
    "created_at": "2025-10-28T10:30:00Z",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

#### **Failed Registration (Duplicate Email)**
```json
{
  "status": "error",
  "message": "Email already exists",
  "code": "EMAIL_EXISTS"
}
```

#### **Successful Login**
```json
{
  "status": "success",
  "message": "Login successful",
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "john@example.com",
    "role": "host",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400
  }
}
```

#### **Failed Login**
```json
{
  "status": "error",
  "message": "Invalid email or password",
  "code": "INVALID_CREDENTIALS"
}
```

#### **Get Profile**
```json
{
  "status": "success",
  "data": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "phone_number": "+233501234567",
    "role": "host",
    "created_at": "2025-10-28T10:30:00Z"
  }
}
```

### **Validation Rules**

* **Email**: 
  - Must be unique across all users
  - Must be a valid email format
  - Required field
* **Password**: 
  - Minimum 8 characters
  - Must contain at least one uppercase letter
  - Must contain at least one lowercase letter
  - Must contain at least one number
  - Must contain at least one special character
  - Required field
* **First Name & Last Name**: 
  - Required fields
  - Maximum 50 characters each
  - Cannot be empty strings
* **Phone Number**: 
  - Optional field
  - Must be valid international format if provided
* **Role**: 
  - Required field
  - Must be one of: `guest`, `host`, or `admin`
  - Default: `guest`

### **Performance Criteria**

* Authentication requests must complete within **500ms** under normal load
* Password hashing using **bcrypt** with minimum 10 salt rounds
* JWT tokens expire after **24 hours**
* Refresh token mechanism for extended sessions (optional)
* Failed login attempts trigger rate limiting:
  - Maximum 5 attempts per minute per IP address
  - Account lockout after 10 failed attempts within 15 minutes
* Session management with Redis for token blacklisting on logout

### **Security Requirements**

* All passwords must be hashed before storage (never store plain text)
* JWT tokens must be signed with secure secret key
* HTTPS required for all authentication endpoints
* Input sanitization to prevent SQL injection and XSS attacks
* CORS configuration to allow only trusted domains

---

## 2. 🏠 Property Management

### **Overview**

Allows Hosts to create, update, and delete property listings. Guests can retrieve and search for properties with advanced filtering capabilities.

### **API Endpoints**

| Method   | Endpoint              | Description                                        |
| :------- | :-------------------- | :------------------------------------------------- |
| `POST`   | `/api/properties`     | Create a new property listing (Host only).         |
| `GET`    | `/api/properties`     | Retrieve all listings (with filters & pagination). |
| `GET`    | `/api/properties/:id` | Retrieve a single property by ID.                  |
| `PUT`    | `/api/properties/:id` | Update an existing property (Host/Owner only).     |
| `DELETE` | `/api/properties/:id` | Delete a property (Host/Owner only).               |

### **Input Specifications**

#### `/api/properties` (POST)
```json
{
  "name": "Cozy Apartment in Accra",
  "description": "Beautiful 2-bedroom apartment with modern amenities, located in the heart of Accra. Perfect for business travelers and families.",
  "location": "East Legon, Accra, Ghana",
  "price_per_night": 80.00,
  "amenities": ["Wi-Fi", "Air Conditioning", "Parking", "Kitchen", "TV"],
  "max_guests": 4
}
```

#### `/api/properties/:id` (PUT)
```json
{
  "name": "Luxury Apartment in Accra",
  "description": "Updated description with more details",
  "price_per_night": 95.00,
  "amenities": ["Wi-Fi", "Air Conditioning", "Parking", "Kitchen", "TV", "Pool"]
}
```

#### `/api/properties` (GET) - Query Parameters
```
?location=Accra
&min_price=50
&max_price=150
&guests=2
&amenities=Wi-Fi,Pool
&page=1
&limit=10
&sort_by=price_per_night
&order=asc
```

### **Output Specifications**

#### **Property Created**
```json
{
  "status": "success",
  "message": "Property created successfully",
  "data": {
    "property_id": "650e8400-e29b-41d4-a716-446655440001",
    "host_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Cozy Apartment in Accra",
    "description": "Beautiful 2-bedroom apartment...",
    "location": "East Legon, Accra, Ghana",
    "price_per_night": 80.00,
    "created_at": "2025-10-28T10:45:00Z",
    "updated_at": "2025-10-28T10:45:00Z"
  }
}
```

#### **Property List (with pagination)**
```json
{
  "status": "success",
  "data": {
    "properties": [
      {
        "property_id": "650e8400-e29b-41d4-a716-446655440001",
        "name": "Cozy Apartment in Accra",
        "location": "East Legon, Accra, Ghana",
        "price_per_night": 80.00,
        "host": {
          "host_id": "550e8400-e29b-41d4-a716-446655440000",
          "first_name": "John",
          "last_name": "Doe"
        },
        "created_at": "2025-10-28T10:45:00Z"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_items": 48,
      "items_per_page": 10
    }
  }
}
```

#### **Single Property Details**
```json
{
  "status": "success",
  "data": {
    "property_id": "650e8400-e29b-41d4-a716-446655440001",
    "host_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Cozy Apartment in Accra",
    "description": "Beautiful 2-bedroom apartment...",
    "location": "East Legon, Accra, Ghana",
    "price_per_night": 80.00,
    "amenities": ["Wi-Fi", "Air Conditioning", "Parking"],
    "host": {
      "host_id": "550e8400-e29b-41d4-a716-446655440000",
      "first_name": "John",
      "last_name": "Doe",
      "email": "john@example.com"
    },
    "created_at": "2025-10-28T10:45:00Z",
    "updated_at": "2025-10-28T10:45:00Z"
  }
}
```

#### **Property Not Found**
```json
{
  "status": "error",
  "message": "Property not found",
  "code": "PROPERTY_NOT_FOUND"
}
```

#### **Unauthorized Update Attempt**
```json
{
  "status": "error",
  "message": "You are not authorized to modify this property",
  "code": "UNAUTHORIZED"
}
```

### **Validation Rules**

* **Name**: 
  - Required field
  - Maximum 100 characters
  - Cannot be empty string
* **Description**: 
  - Required field
  - Maximum 1000 characters
  - Minimum 50 characters for quality assurance
* **Location**: 
  - Required field
  - Maximum 200 characters
* **Price per Night**: 
  - Required field
  - Must be a positive decimal number
  - Minimum value: 1.00
  - Maximum 2 decimal places
* **Amenities**: 
  - Optional field
  - Must be an array of strings if provided
* **Authorization**: 
  - Only authenticated hosts can create properties
  - Only property owner (host) can update or delete their properties
  - Admin users can modify any property

### **Performance Criteria**

* Support pagination with configurable `limit` (default: 10, max: 100) and `offset`
* Database queries optimized with indexes on:
  - `location` (for location-based searches)
  - `price_per_night` (for price range filters)
  - `created_at` (for sorting by date)
  - `host_id` (for host-specific queries)
* Property listing retrieval ≤ **500ms** under normal load
* Property CRUD operations ≤ **700ms** under normal load
* Full-text search capability on `name` and `description` fields
* Caching strategy for frequently accessed properties (Redis)
* Support for concurrent property updates with optimistic locking

### **Business Rules**

* Hosts can only create properties if their account is verified
* Properties must be approved by admin before going live (optional workflow)
* Soft delete implementation (properties marked as deleted but retained in database)
* Property history/audit trail for compliance

---

## 3. 📅 Booking System

### **Overview**

Enables Guests to book available properties, manage reservations, and track booking status. Includes conflict detection to prevent double-booking.

### **API Endpoints**

| Method   | Endpoint            | Description                              |
| :------- | :------------------ | :--------------------------------------- |
| `POST`   | `/api/bookings`     | Create a new booking (Guest only).       |
| `GET`    | `/api/bookings`     | Retrieve user's bookings (Guest/Host).   |
| `GET`    | `/api/bookings/:id` | View specific booking details.           |
| `PUT`    | `/api/bookings/:id` | Update booking status (cancel/confirm).  |
| `DELETE` | `/api/bookings/:id` | Cancel an existing booking.              |

### **Input Specifications**

#### `/api/bookings` (POST)
```json
{
  "property_id": "650e8400-e29b-41d4-a716-446655440001",
  "start_date": "2025-11-01",
  "end_date": "2025-11-05",
  "total_price": 320.00,
  "status": "pending"
}
```

#### `/api/bookings/:id` (PUT)
```json
{
  "status": "confirmed"
}
```

#### `/api/bookings` (GET) - Query Parameters
```
?status=confirmed
&property_id=650e8400-e29b-41d4-a716-446655440001
&start_date=2025-11-01
&end_date=2025-11-30
&page=1
&limit=10
```

### **Output Specifications**

#### **Booking Created**
```json
{
  "status": "success",
  "message": "Booking created successfully",
  "data": {
    "booking_id": "750e8400-e29b-41d4-a716-446655440002",
    "property_id": "650e8400-e29b-41d4-a716-446655440001",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "start_date": "2025-11-01",
    "end_date": "2025-11-05",
    "total_price": 320.00,
    "status": "pending",
    "created_at": "2025-10-28T11:00:00Z"
  }
}
```

#### **Booking List**
```json
{
  "status": "success",
  "data": {
    "bookings": [
      {
        "booking_id": "750e8400-e29b-41d4-a716-446655440002",
        "property": {
          "property_id": "650e8400-e29b-41d4-a716-446655440001",
          "name": "Cozy Apartment in Accra",
          "location": "East Legon, Accra, Ghana"
        },
        "start_date": "2025-11-01",
        "end_date": "2025-11-05",
        "total_price": 320.00,
        "status": "confirmed",
        "created_at": "2025-10-28T11:00:00Z"
      }
    ],
    "pagination": {
      "current_page": 1,
      "total_pages": 3,
      "total_items": 25,
      "items_per_page": 10
    }
  }
}
```

#### **Single Booking Details**
```json
{
  "status": "success",
  "data": {
    "booking_id": "750e8400-e29b-41d4-a716-446655440002",
    "property": {
      "property_id": "650e8400-e29b-41d4-a716-446655440001",
      "name": "Cozy Apartment in Accra",
      "location": "East Legon, Accra, Ghana",
      "price_per_night": 80.00
    },
    "guest": {
      "user_id": "550e8400-e29b-41d4-a716-446655440000",
      "first_name": "John",
      "last_name": "Doe",
      "email": "john@example.com"
    },
    "start_date": "2025-11-01",
    "end_date": "2025-11-05",
    "total_price": 320.00,
    "status": "confirmed",
    "created_at": "2025-10-28T11:00:00Z"
  }
}
```

#### **Booking Conflict**
```json
{
  "status": "error",
  "message": "Property not available for selected dates",
  "code": "BOOKING_CONFLICT",
  "details": {
    "conflicting_bookings": [
      {
        "booking_id": "850e8400-e29b-41d4-a716-446655440003",
        "start_date": "2025-11-03",
        "end_date": "2025-11-07"
      }
    ]
  }
}
```

#### **Invalid Date Range**
```json
{
  "status": "error",
  "message": "End date must be after start date",
  "code": "INVALID_DATE_RANGE"
}
```

#### **Booking Cancelled**
```json
{
  "status": "success",
  "message": "Booking cancelled successfully",
  "data": {
    "booking_id": "750e8400-e29b-41d4-a716-446655440002",
    "status": "canceled",
    "refund_amount": 320.00,
    "cancelled_at": "2025-10-29T14:30:00Z"
  }
}
```

### **Validation Rules**

* **Property ID**: 
  - Required field
  - Must reference an existing, active property
* **Start Date**: 
  - Required field
  - Must be a valid date in ISO 8601 format (YYYY-MM-DD)
  - Must be in the future (at least 1 day from current date)
* **End Date**: 
  - Required field
  - Must be a valid date in ISO 8601 format
  - Must be after `start_date`
  - Maximum booking duration: 90 days
* **Total Price**: 
  - Required field
  - Must be calculated as: `(number of nights) × (price_per_night)`
  - Must be a positive decimal number
* **Status**: 
  - Required field
  - Must be one of: `pending`, `confirmed`, or `canceled`
  - Default: `pending`
* **Date Overlap Check**: 
  - System must verify no existing bookings overlap with requested dates
  - Overlap includes: same dates, partial overlap, or complete encapsulation
* **Authorization**: 
  - Only authenticated guests can create bookings
  - Only booking owner can view/cancel their booking
  - Property hosts can view all bookings for their properties
  - Admins can view/modify all bookings

### **Performance Criteria**

* **Double-booking prevention**: 
  - Implement database-level transaction locks (pessimistic locking)
  - Use serializable isolation level for booking creation
  - Atomic check-and-insert operation
* **Response Time**: 
  - Booking creation must complete ≤ **1 second** (including conflict checks)
  - Booking retrieval ≤ **500ms** under normal load
* **Query Optimization**: 
  - Index on `property_id`, `start_date`, `end_date` for conflict detection
  - Index on `user_id` and `status` for user booking queries
  - Compound index on `property_id + start_date + end_date` for range queries
* **Concurrency Handling**: 
  - Support for 100+ concurrent booking attempts
  - Retry mechanism for failed transactions (max 3 retries)
* **Automated Status Updates**: 
  - Daily batch job to update `pending` bookings older than 24 hours to `canceled`
  - Automated status change from `confirmed` to `completed` after `end_date`
* **Caching Strategy**: 
  - Cache property availability calendar (Redis)
  - Invalidate cache on new booking or cancellation

### **Business Rules**

* **Cancellation Policy**: 
  - Free cancellation up to 48 hours before `start_date`
  - 50% refund for cancellations 24-48 hours before check-in
  - No refund for cancellations within 24 hours
* **Booking Confirmation**: 
  - Bookings remain `pending` until payment is processed
  - Automatic cancellation of unpaid bookings after 24 hours
* **Minimum Stay**: 
  - Properties can specify minimum nights (default: 1 night)
* **Maximum Stay**: 
  - System-wide maximum of 90 consecutive days
* **Host Approval**: 
  - Optional: Some properties require host approval before confirmation

---

## ⚙️ **General Technical Standards**

### **Technology Stack**
* **Database**: PostgreSQL 14+ with proper indexing and constraints
* **ORM**: Prisma / Sequelize for database abstraction
* **Authentication**: JWT (JSON Web Tokens) with Role-Based Access Control (RBAC)
* **Password Security**: bcrypt with minimum 10 salt rounds
* **File Storage**: AWS S3 / Cloudinary for property images and profile photos
* **Caching**: Redis for session management and frequently accessed data
* **API Documentation**: OpenAPI/Swagger specification

### **Security Requirements**
* **Transport Security**: HTTPS/TLS 1.2+ mandatory for all endpoints
* **Input Validation**: Server-side validation for all user inputs
* **SQL Injection Prevention**: Parameterized queries via ORM
* **XSS Prevention**: Input sanitization and output encoding
* **CSRF Protection**: CSRF tokens for state-changing operations
* **Rate Limiting**: 
  - General API: 100 requests/minute per IP
  - Authentication: 5 requests/minute per IP
* **CORS Configuration**: Whitelist specific domains only
* **API Versioning**: Version all APIs (e.g., `/api/v1/properties`)

### **Testing Requirements**
* **Unit Tests**: Jest / Pytest with minimum 80% code coverage
* **Integration Tests**: API endpoint testing with test database
* **Load Testing**: Support 1000 concurrent users
* **Security Testing**: Regular vulnerability scans and penetration testing

### **Monitoring & Logging**
* **Application Logging**: Structured logging with log levels (ERROR, WARN, INFO, DEBUG)
* **Audit Trail**: Log all CRUD operations with user identification
* **Performance Monitoring**: Track API response times and error rates
* **Error Tracking**: Integration with error monitoring service (e.g., Sentry)

### **Data Management**
* **Backup Strategy**: Daily automated backups with 30-day retention
* **Data Retention**: Comply with GDPR/data protection regulations
* **Soft Deletes**: Implement soft delete for critical entities
* **Database Migrations**: Version-controlled schema migrations

---

## 🧩 **Future Enhancements**

### **Phase 2 Features**
* **Payment Integration**: 
  - Stripe / PayPal payment processing
  - Automatic refund handling
  - Split payments (platform fee + host payout)
* **Real-time Notifications**: 
  - WebSocket integration for instant booking updates
  - Email notifications for booking confirmations and reminders
  - SMS notifications for urgent updates
* **Review & Rating System**: 
  - Guest reviews for properties
  - Host reviews for guests
  - Average rating calculation and display
* **Advanced Search**: 
  - Geolocation-based search with radius filtering
  - AI-powered recommendations
  - Saved searches and alerts
* **Messaging System**: 
  - In-app messaging between guests and hosts
  - Automated message templates
  - Message read receipts

### **Phase 3 Features**
* **Multi-currency Support**: Dynamic currency conversion
* **Multi-language Support**: i18n implementation
* **Admin Dashboard**: 
  - Analytics for revenue and occupancy tracking
  - User management interface
  - Dispute resolution workflow
* **Property Availability Calendar**: Visual calendar with block dates feature
* **Dynamic Pricing**: 
  - Seasonal pricing rules
  - Demand-based pricing algorithms
  - Special event pricing
* **Mobile App API**: Optimized endpoints for mobile applications



