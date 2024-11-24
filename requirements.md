# Backend Requirement Specifications

## 1. User Authentication
### Functional Requirements
- Allow users to register as a guest or host.
- Enable secure login using email/password and OAuth providers (e.g., Google, Facebook).
- Provide JWT-based authentication for session management.
- Allow users to reset their password.

### API Endpoints
1. **POST** `/api/auth/register`
   - **Input**:
     ```json
     {
       "name": "John Doe",
       "email": "john.doe@example.com",
       "password": "securepassword",
       "role": "guest" // or "host"
     }
     ```
   - **Output**:
     - Success: `201 Created`
       ```json
       {
         "message": "User registered successfully",
         "userId": "12345"
       }
       ```
     - Failure: `400 Bad Request`
       ```json
       {
         "error": "Email already exists"
       }
       ```

2. **POST** `/api/auth/login`
   - **Input**:
     ```json
     {
       "email": "john.doe@example.com",
       "password": "securepassword"
     }
     ```
   - **Output**:
     - Success: `200 OK`
       ```json
       {
         "token": "JWT_TOKEN",
         "expiresIn": 3600
       }
       ```
     - Failure: `401 Unauthorized`
       ```json
       {
         "error": "Invalid credentials"
       }
       ```

3. **POST** `/api/auth/reset-password`
   - **Input**:
     ```json
     {
       "email": "john.doe@example.com"
     }
     ```
   - **Output**:
     - Success: `200 OK`
       ```json
       {
         "message": "Password reset email sent"
       }
       ```

### Validation Rules
- **Email**: Must be a valid email format.
- **Password**: Minimum 8 characters, at least one uppercase letter, one number, and one special character.

### Performance Criteria
- Response time: <300ms for authentication requests.
- Scalability: Support up to 10,000 concurrent logins.

---

## 2. Property Management
### Functional Requirements
- Allow hosts to create, edit, and delete property listings.
- Validate property details before saving.
- Store property data in the database.

### API Endpoints
1. **POST** `/api/properties`
   - **Input**:
     ```json
     {
       "title": "Cozy Apartment",
       "description": "A lovely 2-bedroom apartment.",
       "location": "New York, NY",
       "price": 150,
       "amenities": ["Wi-Fi", "Kitchen"],
       "availability": ["2024-11-25", "2024-11-26"]
     }
     ```
   - **Output**:
     - Success: `201 Created`
       ```json
       {
         "message": "Property listed successfully",
         "propertyId": "98765"
       }
       ```
     - Failure: `400 Bad Request`
       ```json
       {
         "error": "Price must be a positive number"
       }
       ```

2. **PUT** `/api/properties/:id`
   - **Input**: Same as `POST` endpoint.
   - **Output**:
     - Success: `200 OK`
       ```json
       {
         "message": "Property updated successfully"
       }
       ```
     - Failure: `404 Not Found`
       ```json
       {
         "error": "Property not found"
       }
       ```

3. **DELETE** `/api/properties/:id`
   - **Output**:
     - Success: `200 OK`
       ```json
       {
         "message": "Property deleted successfully"
       }
       ```
     - Failure: `404 Not Found`
       ```json
       {
         "error": "Property not found"
       }
       ```

### Validation Rules
- **Title**: Required, max 100 characters.
- **Price**: Must be a positive number.
- **Location**: Required, valid geolocation format.

### Performance Criteria
- Create or update listings within 500ms.
- Handle up to 5,000 listings added per hour.

---

## 3. Booking System
### Functional Requirements
- Allow guests to book properties for specified dates.
- Prevent double bookings.
- Enable cancellation of bookings based on policy.

### API Endpoints
1. **POST** `/api/bookings`
   - **Input**:
     ```json
     {
       "propertyId": "98765",
       "guestId": "12345",
       "dates": ["2024-11-25", "2024-11-26"]
     }
     ```
   - **Output**:
     - Success: `201 Created`
       ```json
       {
         "message": "Booking created successfully",
         "bookingId": "56789"
       }
       ```
     - Failure: `409 Conflict`
       ```json
       {
         "error": "Property not available for selected dates"
       }
       ```

2. **DELETE** `/api/bookings/:id`
   - **Output**:
     - Success: `200 OK`
       ```json
       {
         "message": "Booking canceled successfully"
       }
       ```
     - Failure: `404 Not Found`
       ```json
       {
         "error": "Booking not found"
       }
       ```

3. **GET** `/api/bookings`
   - **Output**:
     ```json
     [
       {
         "bookingId": "56789",
         "propertyId": "98765",
         "guestId": "12345",
         "dates": ["2024-11-25", "2024-11-26"],
         "status": "Confirmed"
       }
     ]
     ```

### Validation Rules
- **Dates**: Must not overlap with existing bookings.
- **Guest ID and Property ID**: Must exist in the database.

### Performance Criteria
- Process booking requests within 300ms.
- Ensure no double bookings through concurrency checks.

---
