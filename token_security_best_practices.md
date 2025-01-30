# Secure Token Storage and Retrieval Best Practices

## 1. Secure Storage
### Frontend (Client-Side)
- **Access Token**: Store in **memory (RAM)** to avoid XSS attacks.
- **Refresh Token**: Store in **HTTP-only, Secure, SameSite=Strict Cookies** to prevent XSS and CSRF attacks.

### Backend (Server-Side)
- Store refresh tokens **securely in the database** (hashed using HMAC or AES encryption).
- Associate refresh tokens with user IDs and device fingerprints for added security.

## 2. Token Lifecycle Management
- **Short-lived Access Tokens (e.g., 5-15 minutes)**: Reduces the impact if compromised.
- **Long-lived Refresh Tokens (e.g., 7-30 days)**: Used to obtain new access tokens when expired.

## 3. Authentication Flow
### Access Token Usage
- Sent in the **Authorization** header:  
  ```http
  Authorization: Bearer <access_token>
  ```

### Refresh Token Usage
- Sent in an **HTTP-only Secure Cookie**.
- When the access token expires, the client silently refreshes it using the refresh token.

## 4. Token Rotation
- **Rotate refresh tokens** with each usage to prevent replay attacks.
- Store only the latest refresh token in the database.
- Revoke old refresh tokens when a new one is issued.

## 5. Revoke & Logout Handling
- Maintain a **denylist** of revoked refresh tokens (hashed for security).
- Implement **device-specific token tracking** (i.e., bind refresh tokens to device fingerprints).
- Provide a **logout endpoint** to invalidate tokens.

## 6. Example Implementation
### Frontend (React)
```javascript
const login = async (credentials) => {
  const res = await fetch('/api/login', {
    method: 'POST',
    body: JSON.stringify(credentials),
    credentials: 'include', // Ensures cookies are sent
  });

  const data = await res.json();
  localStorage.setItem('accessToken', data.accessToken);
};

const refreshAccessToken = async () => {
  const res = await fetch('/api/refresh-token', {
    method: 'POST',
    credentials: 'include', // Sends refresh token stored in cookies
  });

  const data = await res.json();
  localStorage.setItem('accessToken', data.accessToken);
};
```

### Backend (Node.js - Express & JWT)
```javascript
const jwt = require('jsonwebtoken');

// Generate Tokens
const generateTokens = (userId) => {
  const accessToken = jwt.sign({ userId }, process.env.ACCESS_SECRET, { expiresIn: '15m' });
  const refreshToken = jwt.sign({ userId }, process.env.REFRESH_SECRET, { expiresIn: '7d' });
  return { accessToken, refreshToken };
};

// Login API
app.post('/api/login', async (req, res) => {
  const { userId } = req.body;
  const tokens = generateTokens(userId);

  res.cookie('refreshToken', tokens.refreshToken, {
    httpOnly: true,
    secure: true,
    sameSite: 'Strict',
  });

  res.json({ accessToken: tokens.accessToken });
});

// Refresh Token API
app.post('/api/refresh-token', async (req, res) => {
  const refreshToken = req.cookies.refreshToken;
  if (!refreshToken) return res.sendStatus(401);

  jwt.verify(refreshToken, process.env.REFRESH_SECRET, (err, decoded) => {
    if (err) return res.sendStatus(403);
    const tokens = generateTokens(decoded.userId);
    
    res.cookie('refreshToken', tokens.refreshToken, {
      httpOnly: true,
      secure: true,
      sameSite: 'Strict',
    });

    res.json({ accessToken: tokens.accessToken });
  });
});
```

## 7. Summary of Best Practices

| Component       | Storage Location | Security Features |
|---------------|----------------|----------------|
| **Access Token** | **Memory (RAM)** | Prevents XSS |
| **Refresh Token** | **HTTP-only Secure Cookie** | Prevents XSS & CSRF |
| **Backend Storage** | **Database (Hashed/Encrypted Refresh Tokens)** | Prevents token theft |
| **Token Expiry** | **Access: Short (5-15 min), Refresh: Long (7-30 days)** | Reduces impact of leaks |
| **Token Rotation** | **Replace Refresh Token on use** | Prevents replay attacks |
| **Logout Handling** | **Revoke refresh tokens** | Prevents misuse |

---

This ensures a **secure, scalable, and efficient** token management system. 🚀
