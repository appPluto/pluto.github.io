# 📘 Pluto Auth Backend – Documentation

## 📌 Overview

The Pluto Auth Backend provides secure user authentication and identity management for the Pluto app. It supports:

- Influencer onboarding via social platforms (Instagram/Twitter)
- Brand partner registration/login with email and Google
- JWT-based authentication with refresh token support
- Token revocation and logout
- Role-based login redirection
- REST API, designed for integration with a React Native mobile frontend

---

## ⚙️ Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-org/pluto-auth-backend
cd pluto-auth-backend
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure `.env` 

```.env
PORT=5000
MONGODB_URI=postgres://newuser:password@localhost:5432/plutoApp
JWT_SECRET=super_secret_key
JWT_REFRESH_SECRET=refresh_secret
JWT_EXPIRES_IN=2d
JWT_REFRESH_EXPIRES_IN=7d
```

### 4. Start the server 

```bash
npm run dev
```

## 👥 User Types

### 1. Influencer

- Registers via Twitter or Instagram
- Login using platform and social ID
- Receives a referral code after signup
- Only requires `platform` and `socialId` fields

### 2. Brand Partner

- Registers with email, business name, and location
- Can use Google OAuth (optional)
- Login with email + password

## 🔐 Authentication Flow (JWT-based) 

### ✅ On Login

- Server returns:
     - `accessToken` (short-lived)
     - `refreshToken` (long-lived)
- Tokens are stored on the mobile app

### 🔄 Token Refresh

- Send `refreshToken` to `/api/auth/refresh`
- Server returns a new `accessToken`

### 🔓 Logout

- Send `refreshToken` to `/api/auth/logout`
- Server blacklists the token

## 🔌 API Endpoints

### 🚀 Influencer Routes

#### POST `/api/influencer/register`

```json
{
  "platform": "instagram",
  "socialId": "abc123"
}
```

Response:

```json
{
  "code": "PLUTO-UT0XE",
  "message": "Registered successfully"
}
```

#### POST `/api/influencer/login`

```json
{
  "platform": "instagram",
  "socialId": "abc123"
}
```

Response:

```json
{
  "message": "Login successful"
  "accessToken": "...",
  "refreshToken": "...",
  "user": {
    "id": "id",
    "role": "influencer",
    "code": "PLUTO-UT0XE",
    "created_at": "2025-06-07T08:23:26.148Z"
  }
}
```

### 🔐 Auth Routes

#### POST `/api/auth/refresh`

```json
{
  "refreshToken": "..."
}
```

Returns:

```json
{
  "accessToken": "..."
}
```

#### POST `/api/auth/logout`

```json
{
  "refreshToken": "..."
}
```

## 🛡️ Middleware

### `authMiddleware.js`

- Validates `accessToken` from `Authorization` header
- Adds `req.user` to access `userId` and `role`
- Used in protected routes like `/payments`, `/profile`, etc.

## 🔑 Token Utility

### `generateToken.js`

Exports functions to generate:

- generateAccessToken(userId)
- generateRefreshToken(userId)
- Tokens are signed with JWT_SECRET and include exp fields

## 🗂️ Models

### Influencer

```javascript
{
  id: Serial,
  socialId: String,
  platform: String,
  code: String,
  createdAt: Date
}
```

### BrandPartner

```javascript
{
  id: Serial
  brand_name: String,
  email: String,
  password: Hashed,
  phone: String,
  google_id: String,
  createdAt: Date
}
```

### Token (Blacklist/Revoked)

```javascript
id: Serial, 
token: String,
user_id: Int,
createdAt: Date
```

## ✅ Security Practices

- JWT secrets and expiration configured in `.env`
- Refresh tokens stored and validated securely
- Logout revokes refresh tokens via DB
- Passwords hashed using bcrypt
- Helmet, CORS, and rate limiting can be added for production

## 🧪 Testing

Use tools like Postman or Insomnia to test:

- Register influencer & brand
- Login and receive access/refresh tokens
- Use access token for protected route
- Refresh token to get new access token
- Logout and revoke refresh token

## 📱 Mobile Integration (React Native)

- Use `SecureStore` to persist tokens.
- Send `accessToken` in `Authorization` header: `Authorization: Bearer <accessToken>`
- Detect expired token and call `/api/auth/refresh`
- Logout clears all tokens


✨ Built for the Pluto ecosystem with love and scalability in mind.
