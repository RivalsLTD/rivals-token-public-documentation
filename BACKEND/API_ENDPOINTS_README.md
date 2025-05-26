# API Endpoints Documentation

This document provides a comprehensive overview of all API endpoints available in the Rivals Token Backend application, organized by module with example queries.

## Table of Contents

- [Admin Endpoints](#admin-endpoints)
- [Clerk Webhook Endpoints](#clerk-webhook-endpoints)
- [Credit Management Endpoints](#credit-management-endpoints)
- [Payment Endpoints](#payment-endpoints)
- [Product Management Endpoints](#product-management-endpoints)
- [User Management Endpoints](#user-management-endpoints)

---

## Admin Endpoints

**Base Path:** `/api/admin`

### GET /admin/settings

- **Description:** Retrieve admin settings (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
GET /api/admin/settings
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** Admin settings configuration

---

## Clerk Webhook Endpoints

**Base Path:** `/api/webhook`

### POST /webhook/clerk

- **Description:** Handle Clerk webhook events for user authentication and management
- **Authentication:** Webhook signature verification
- **Example Query:**

```
POST /api/webhook/clerk
Headers:
  svix-id: evt_2Xh8bHQbpPZ4OjLwSiJlHnVzY3M
  svix-timestamp: 1661804946
  svix-signature: v1,g0hM9SsE+OTPJTGt/TMkDdsMlYKnWkmfP+o5g0lmKBo=
Body: {
  "data": {
    "id": "user_2Xh8bHQbpPZ4OjLwSiJlHnVzY3M",
    "email_addresses": [{"email_address": "user@example.com"}],
    "username": "testuser"
  },
  "type": "user.created"
}
```

- **Response:** Success confirmation

---

## Credit Management Endpoints

**Base Path:** `/api/credit`

### User Credit Operations

#### GET /credit/history

- **Description:** Get paginated and filtered credit history for the current user
- **Authentication:** User authentication required
- **Query Parameters:**
  - `page` (int): Page number (default: 1)
  - `size` (int): Items per page (1-100, default: 10)
  - `status` (string): Filter by credit status
  - `start_date` (datetime): Filter by start date
  - `end_date` (datetime): Filter by end date
  - `min_amount` (float): Filter by minimum amount
  - `max_amount` (float): Filter by maximum amount
- **Example Query:**

```
GET /api/credit/history?page=1&size=20&status=completed&min_amount=10.0&max_amount=1000.0
Headers: Authorization: Bearer <jwt_token>
```

- **Response:** Paginated credit history

#### POST /credit/transfer/email

- **Description:** Transfer credits to another user by email
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/credit/transfer/email
Headers: Authorization: Bearer <jwt_token>
Body: {
  "email": "recipient@example.com",
  "amount": 50.0,
  "note": "Payment for services"
}
```

- **Response:** Transfer confirmation

#### POST /credit/verify-transfer/email

- **Description:** Verify user exists before transfer by email
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/credit/verify-transfer/email
Headers: Authorization: Bearer <jwt_token>
Body: {
  "email": "recipient@example.com"
}
```

- **Response:** User verification status

### Admin Credit Operations

#### GET /credit/admin/history

- **Description:** Get credit history for all users (admin only)
- **Authentication:** Admin role required
- **Query Parameters:**
  - `page` (int): Page number (default: 1)
  - `size` (int): Items per page (1-100, default: 10)
  - `email` (string): Filter by user email
- **Example Query:**

```
GET /api/credit/admin/history?page=1&size=50&email=john@example.com
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** Paginated credit history for all users

#### POST /credit/admin/add-credit/email

- **Description:** Add credits to user account by email (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
POST /api/credit/admin/add-credit/email
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "email": "user@example.com",
  "amount": 100.0,
  "reason": "Promotional bonus"
}
```

- **Response:** Updated credit details

#### POST /credit/admin/deduct-credit/email

- **Description:** Deduct credits from user account by email (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
POST /api/credit/admin/deduct-credit/email
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "email": "user@example.com",
  "amount": 50.0,
  "reason": "Policy violation penalty"
}
```

- **Response:** Updated credit details

#### POST /credit/admin/verify/email

- **Description:** Verify user by email (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
POST /api/credit/admin/verify/email
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "email": "user@example.com"
}
```

- **Response:** User verification details

---

## Payment Endpoints

**Base Path:** `/api/payment`

### General Payment Operations

#### GET /payment/currencies

- **Description:** Get list of available payment currencies
- **Authentication:** Not required
- **Example Query:**

```
GET /api/payment/currencies
```

- **Response:** Available currencies list

#### GET /payment/min-payment

- **Description:** Get minimum payment amount information
- **Authentication:** Not required
- **Example Query:**

```
GET /api/payment/min-payment
```

- **Response:** Minimum payment amount details

#### POST /payment/estimate

- **Description:** Get estimated price for currency conversion
- **Authentication:** Not required
- **Example Query:**

```
POST /api/payment/estimate
Body: {
  "currency_from": "usd",
  "currency_to": "btc",
  "amount": 100.0
}
```

- **Response:** Estimated conversion price

#### GET /payment/payment-history

- **Description:** Get payment history for the current user
- **Authentication:** User authentication required
- **Example Query:**

```
GET /api/payment/payment-history
Headers: Authorization: Bearer <jwt_token>
```

- **Response:** User's payment history

### BBPay Integration

#### POST /payment/bbpay/create-payment

- **Description:** Create a new payment via BBPay API
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/payment/bbpay/create-payment
Headers: Authorization: Bearer <jwt_token>
Body: {
  "amount": 100.0,
  "currency": "USD",
  "order_id": "ORDER_123456",
  "description": "Token package purchase"
}
```

- **Response:** BBPay payment response with payment URL

#### POST /payment/bbpay/check-payment

- **Description:** Check the status of a BBPay payment
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/payment/bbpay/check-payment
Headers: Authorization: Bearer <jwt_token>
Body: {
  "order_id": "ORDER_123456"
}
```

- **Response:** Current payment status

#### POST /payment/bbpay-webhook

- **Description:** Handle BBPay webhook notifications
- **Authentication:** Webhook signature verification
- **Example Query:**

```
POST /api/payment/bbpay-webhook
Headers: BBPay-Signature: signature_here
Body: {
  "order_id": "ORDER_123456",
  "status": "completed",
  "amount": 100.0,
  "currency": "USD"
}
```

- **Response:** Processing confirmation
- **Note:** Not included in API schema

### Paddle Integration

#### POST /payment/paddle/create-payment

- **Description:** Create a new payment via Paddle API
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/payment/paddle/create-payment
Headers: Authorization: Bearer <jwt_token>
Body: {
  "amount": 100.0,
  "currency": "USD",
  "order_id": "ORDER_123456",
  "description": "Token package purchase"
}
```

- **Response:** Paddle payment response with checkout URL

#### POST /payment/paddle/check-payment

- **Description:** Check the status of a Paddle payment
- **Authentication:** User authentication required
- **Example Query:**

```
POST /api/payment/paddle/check-payment
Headers: Authorization: Bearer <jwt_token>
Body: {
  "order_id": "ORDER_123456"
}
```

- **Response:** Current payment status

#### POST /payment/paddle-webhook

- **Description:** Handle Paddle webhook notifications
- **Authentication:** Webhook signature verification
- **Example Query:**

```
POST /api/payment/paddle-webhook
Headers: Paddle-Signature: signature_here
Body: {
  "alert_id": "123456789",
  "alert_name": "payment_succeeded",
  "order_id": "ORDER_123456",
  "status": "completed",
  "amount": 100.0,
  "currency": "USD"
}
```

- **Response:** Processing confirmation
- **Note:** Not included in API schema

---

## Product Management Endpoints

**Base Path:** `/api/products`

### Admin Product Operations

#### GET /products/token-packages

- **Description:** Get token packages with filtering options (admin only)
- **Authentication:** Admin role required
- **Query Parameters:**
  - `package_type` (PackageType): Filter by package type ('fiat' or 'crypto')
  - `is_active` (bool): Filter by active status
- **Example Query:**

```
GET /api/products/token-packages?package_type=fiat&is_active=true
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** List of token packages

#### GET /products/token-packages/{package_id}

- **Description:** Get specific token package by ID (admin only)
- **Authentication:** Admin role required
- **Path Parameters:**
  - `package_id` (int): ID of the package to retrieve
- **Example Query:**

```
GET /api/products/token-packages/123
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** Token package details

#### POST /products/token-packages

- **Description:** Create a new token package (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
POST /api/products/token-packages
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "name": "Starter Package",
  "description": "Perfect for new users",
  "token_count": 100,
  "price": 9.99,
  "package_type": "fiat",
  "is_active": true
}
```

- **Response:** Created token package

#### PUT /products/token-packages/{package_id}

- **Description:** Update an existing token package (admin only)
- **Authentication:** Admin role required
- **Path Parameters:**
  - `package_id` (int): ID of the package to update
- **Example Query:**

```
PUT /api/products/token-packages/123
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "name": "Updated Starter Package",
  "price": 12.99,
  "is_active": true
}
```

- **Response:** Updated token package

#### DELETE /products/token-packages/{package_id}

- **Description:** Delete a token package (soft delete) (admin only)
- **Authentication:** Admin role required
- **Path Parameters:**
  - `package_id` (int): ID of the package to delete
- **Example Query:**

```
DELETE /api/products/token-packages/123
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** Delete confirmation

### Public Product Operations

#### GET /products/public/token-packages

- **Description:** Get available token packages for public viewing
- **Authentication:** Not required
- **Query Parameters:**
  - `package_type` (PackageType): Filter by package type ('fiat' or 'crypto')
- **Example Query:**

```
GET /api/products/public/token-packages?package_type=crypto
```

- **Response:** List of public token packages

---

## User Management Endpoints

**Base Path:** `/api/user`

### User Balance

#### GET /user/balance

- **Description:** Get user's credit balance
- **Authentication:** User authentication required
- **Example Query:**

```
GET /api/user/balance
Headers: Authorization: Bearer <jwt_token>
```

- **Response:** User credit balance

### Admin User Operations

#### POST /user/update-role

- **Description:** Update user role (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
POST /api/user/update-role
Headers: Authorization: Bearer <admin_jwt_token>
Body: {
  "user_id": 123,
  "new_role": "admin"
}
```

- **Response:** Role update confirmation

#### GET /user/list

- **Description:** Get paginated list of users with filtering (admin only)
- **Authentication:** Admin role required
- **Query Parameters:**
  - `email` (string): Filter by email
  - `role` (string): Filter by role
  - `include_stats` (bool): Include user statistics
  - `page` (int): Page number
  - `page_size` (int): Items per page
- **Example Query:**

```
GET /api/user/list?page=1&page_size=20&role=user&include_stats=true
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** Paginated user list

#### GET /user/statistics

- **Description:** Get user statistics (admin only)
- **Authentication:** Admin role required
- **Example Query:**

```
GET /api/user/statistics
Headers: Authorization: Bearer <admin_jwt_token>
```

- **Response:** User statistics summary

---

## Authentication Notes

- **User Authentication**: Most user endpoints require valid JWT token authentication
- **Admin Authentication**: Admin endpoints require both authentication and admin role verification
- **Webhook Authentication**: Webhook endpoints verify signatures from respective payment providers

## Response Format

All endpoints (except webhooks) return responses in the following format:

```json
{
  "message": "Success message",
  "data": {}, // Response data object
  "error": false // Boolean indicating if there was an error
}
```

## Common Request Examples

### Authentication Headers

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Common Query Parameters

- **Pagination**: `?page=1&page_size=20`
- **Filtering**: `?status=active&created_after=2024-01-01`
- **Sorting**: `?sort_by=created_at&order=desc`

## Error Handling

The API uses standard HTTP status codes and returns error details in the response body when applicable. Common status codes include:

- `200` - Success
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `500` - Internal Server Error

### Example Error Response

```json
{
  "message": "Invalid request parameters",
  "data": null,
  "error": true,
  "details": {
    "field": "email",
    "issue": "Invalid email format"
  }
}
```
