

 Airbnb Clone – Backend Requirement Specifications

 🔐 1. User Authentication

 Overview

Manages user registration, login, and session handling using JWT.

 API Endpoints

| Method | Endpoint             | Description                  | Auth Required |
| ------ | -------------------- | ---------------------------- | ------------- |
| POST   | `/api/auth/register` | Register new guest/host      | No            |
| POST   | `/api/auth/login`    | User login and token issue   | No            |
| GET    | `/api/auth/profile`  | Fetch logged-in user profile | Yes           |
| PUT    | `/api/auth/profile`  | Update profile info          | Yes           |

 Input / Output Specs

 Register

   Input: `{ "email": "", "password": "", "role": "guest|host" }`
   Output: `{ "message": "User created", "token": "" }`

 Login

   Input: `{ "email": "", "password": "" }`
   Output: `{ "token": "", "user": { ... } }`

 Validation Rules

 Email must be valid and unique.
 Password must be ≥ 8 characters, including one special character.
 Role must be either `guest` or `host`.

 Performance Criteria

 Response time ≤ 300ms
 Token expiration configurable (default: 24h)

 🏠 2. Property Management

 Overview

Allows hosts to manage their listings: create, update, delete, and retrieve property data.

 API Endpoints

| Method | Endpoint               | Description            | Auth Required | Roles Allowed |
| ------ | ---------------------- | ---------------------- | ------------- | ------------- |
| POST   | `/api/properties/`     | Create property        | Yes           | Host          |
| GET    | `/api/properties/`     | List all properties    | No            | All           |
| GET    | `/api/properties/:id/` | View specific property | No            | All           |
| PUT    | `/api/properties/:id/` | Update property        | Yes           | Host (Owner)  |
| DELETE | `/api/properties/:id/` | Delete property        | Yes           | Host (Owner)  |

 Input / Output Specs

 Add Listing

   Input: `{ "title": "", "description": "", "price": 120, "amenities": [], "location": "" }`
   Output: `{ "message": "Listing created", "id": 123 }`

 Get Listings

   Output: Array of property objects with filters applied (location, price, guests)

 Validation Rules

 Price must be a positive number.
 Title must be ≤ 100 characters.
 Only authenticated hosts can create/edit/delete.

 Performance Criteria

 DB reads optimized with indexing on location, price.
 Response time ≤ 500ms for most queries.


 📆 3. Booking System

 Overview

Handles booking requests from guests and keeps track of availability and booking history.

 API Endpoints

| Method | Endpoint                   | Description                | Auth Required | Roles Allowed |
| ------ | -------------------------- | -------------------------- | ------------- | ------------- |
| POST   | `/api/bookings/`           | Create new booking         | Yes           | Guest         |
| GET    | `/api/bookings/`           | Get user’s bookings        | Yes           | Guest/Host    |
| PUT    | `/api/bookings/:id/cancel` | Cancel an existing booking | Yes           | Guest/Host    |

 Input / Output Specs

 Create Booking

   Input: `{ "property_id": 12, "check_in": "2025-07-10", "check_out": "2025-07-15" }`
   Output: `{ "message": "Booking confirmed", "booking_id": 889 }`

 Cancel Booking

   Input: `{ "reason": "change of plans" }`
   Output: `{ "status": "canceled" }`

 Validation Rules

 Booking dates must not overlap with existing bookings.
 Check-out must be after check-in.
 Only the user who created the booking can cancel it.

 Performance Criteria

 Availability check in ≤ 250ms.
 Prevent race conditions on concurrent bookings (transactional logic).

