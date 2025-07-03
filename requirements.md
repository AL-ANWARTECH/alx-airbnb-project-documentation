# Airbnb Clone Backend – Technical & Functional Requirements

This document outlines detailed backend requirement specifications for three core features of the Airbnb Clone system:

- User Authentication  
- Property Management  
- Booking System

---

## 1. 🔐 User Authentication

### 🎯 Functional Requirements
- Users can register, login, and logout securely.
- System supports roles: `guest`, `host`, and `admin`.
- Each user must have a unique email and secure password.

### 🔧 API Endpoints

| Method | Endpoint         | Description                  |
|--------|------------------|------------------------------|
| POST   | /api/register    | Register a new user account  |
| POST   | /api/login       | Log in using email/password  |
| POST   | /api/logout      | Logout and revoke token      |
| GET    | /api/profile     | Fetch authenticated user info|

### 🧾 Input Specification
```json
{
  "email": "user@example.com",
  "password": "securepassword123",
  "role": "guest"
}

📤 Output Example
{
  "user_id": "uuid",
  "email": "user@example.com",
  "token": "JWT-token",
  "role": "host"
}
✅ Validation Rules
Email must be valid and unique

Password: Minimum 8 characters, must be hashed

Role must be one of: guest, host, admin

JWT tokens expire in 24 hours

⚙️ Performance Criteria
Response time < 300ms

Login attempts throttled: max 5/min per IP

Passwords encrypted using bcrypt (or Argon2)

2. 🏘️ Property Management
🎯 Functional Requirements
Hosts can create, update, and delete property listings.

Guests can view and search available listings.

Listings include: title, description, price, location, availability, images.

🔧 API Endpoints
Method	Endpoint	Description
POST	/api/properties	Host creates a new listing
GET	/api/properties	List all properties (paginated)
GET	/api/properties/:id	Get a specific property
PUT	/api/properties/:id	Update property (host only)
DELETE	/api/properties/:id	Delete property (host only)

🧾 Input Specification
{
  "title": "Beachfront Villa",
  "location": "Lagos",
  "price_per_night": 200.00,
  "amenities": ["Wi-Fi", "Pool", "AC"],
  "availability": ["2025-07-10", "2025-07-30"]
}
📤 Output Example
{
  "property_id": "uuid",
  "host_id": "uuid",
  "title": "Beachfront Villa",
  "location": "Lagos",
  "price_per_night": 200.00
}
✅ Validation Rules
Only users with role host can create/update/delete

Price must be a positive float

Location must be a valid city

Upload limit: max 10 images, 5MB each

⚙️ Performance Criteria
Paginated results (20 per page)

Search response time < 500ms

Optimized with indexes on location, price

3. 📅 Booking System
🎯 Functional Requirements
Guests can book available properties for specific dates.

Prevent overlapping bookings.

Booking statuses: pending, confirmed, cancelled.

🔧 API Endpoints
Method	Endpoint	Description
POST	/api/bookings	Guest creates a booking
GET	/api/bookings	List guest’s bookings
GET	/api/bookings/:id	Get booking details
DELETE	/api/bookings/:id	Cancel booking (guest or host)

🧾 Input Specification
{
  "property_id": "uuid",
  "check_in": "2025-08-01",
  "check_out": "2025-08-05"
}
📤 Output Example
{
  "booking_id": "uuid",
  "status": "confirmed",
  "total_price": 800.00,
  "check_in": "2025-08-01",
  "check_out": "2025-08-05"
}
✅ Validation Rules
check_in must be before check_out

No overlapping bookings for same property

Only users with role guest can create bookings

⚙️ Performance Criteria
Date conflict validation < 100ms

Total price auto-calculated = days × price_per_night

Booking confirmation email sent within 2s

🔐 Authentication Note
All endpoints (except /register and /login) require JWT-based authentication.

📤 Status Codes
200 OK – Successful operation

201 Created – New resource created

400 Bad Request – Validation failed

401 Unauthorized – Missing or invalid token

403 Forbidden – Action not allowed for user role

404 Not Found – Resource not found

500 Internal Server Error – Unexpected error