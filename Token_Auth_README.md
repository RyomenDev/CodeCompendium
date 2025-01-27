
# Access Token and Refresh Token Implementation

## Overview
This project demonstrates how to implement and manage access tokens and refresh tokens for secure authentication in a web application. It ensures seamless token renewal when the access token expires.

## Features
- Short-lived access tokens for secure API requests.
- Long-lived refresh tokens for renewing access tokens.
- Token validation and rotation.
- Secure storage and transmission of tokens.

## How It Works
1. **Access Token**: 
   - Short-lived token (e.g., 15 minutes).
   - Used for authenticating API requests.

2. **Refresh Token**:
   - Long-lived token (e.g., 7 days).
   - Used to obtain a new access token when it expires.

3. **Token Flow**:
   - Client requests an API endpoint with the access token.
   - If the access token is expired, the server returns a 401 Unauthorized error.
   - The client sends a request to the refresh token endpoint to obtain a new access token.
   - A new access token (and optionally a refresh token) is issued.

4. **Server Endpoint for Refresh Token**
Create a dedicated endpoint on the server to handle token refreshing, e.g., /refresh-token.
When the refresh token is sent to this endpoint:
Validate the refresh token.
Issue a new access token (and optionally, a new refresh token).
Invalidate the old refresh token if you're rotating refresh tokens.

5. **Refresh Token Security**
Store the refresh token securely:
For web apps: Use HttpOnly and Secure cookies to prevent XSS attacks.
For mobile apps: Use secure storage (e.g., Keychain for iOS, Keystore for Android).
Do not expose the refresh token to client-side JavaScript.

6. **Implement Token Expiry Check**
On the client side, monitor the exp (expiry) field of the access token payload.
Proactively refresh the token shortly before it expires to avoid disruption.
7. **Token Rotation (Optional)**
Issue a new refresh token whenever you issue a new access token.
Revoke old refresh tokens to prevent misuse if they are leaked.

## Endpoints
### Refresh Token Endpoint
#### Request
```http
POST /refresh-token
```
**Headers**:  
- `Content-Type: application/json`  

**Cookies**:  
- `refreshToken`

#### Response
```json
{
  "accessToken": "new-access-token"
}
```

## Implementation
### Server Code
#### Refresh Token Endpoint
```javascript
app.post('/refresh-token', async (req, res) => {
    const refreshToken = req.cookies.refreshToken;
    if (!refreshToken) {
        return res.status(403).send({ message: 'Refresh token missing' });
    }

    try {
        const user = jwt.verify(refreshToken, REFRESH_TOKEN_SECRET);
        const newAccessToken = jwt.sign({ id: user.id }, ACCESS_TOKEN_SECRET, { expiresIn: '15m' });
        res.send({ accessToken: newAccessToken });
    } catch (error) {
        res.status(403).send({ message: 'Invalid or expired refresh token' });
    }
});
```

### Client Code
#### Token Refresh Workflow
```javascript
async function makeApiRequest() {
    try {
        const response = await fetch('API_ENDPOINT', {
            method: 'GET',
            headers: { Authorization: Bearer ${accessToken} },
        });
        if (response.status === 401) {
            // Access token expired; refresh token to get a new one
            const newAccessToken = await refreshAccessToken();
            // Retry the original request with the new access token
            return makeApiRequest(newAccessToken);
        }
        return await response.json();
    } catch (error) {
        console.error('Request failed', error);
    }
}

async function refreshAccessToken() {
    const response = await fetch('/refresh-token', {
        method: 'POST',
        credentials: 'include', // Include cookies for refresh token
    });
    if (response.ok) {
        const data = await response.json();
        accessToken = data.accessToken; // Update access token
        return data.accessToken;
    } else {
        throw new Error('Failed to refresh access token');
    }
}
```

## Security Best Practices
- Store refresh tokens securely (e.g., HttpOnly cookies for web apps).
- Rotate refresh tokens to reduce the risk of misuse.
- Validate tokens properly on the server side.
- Use HTTPS to encrypt token transmission.

## License
This project is licensed under the MIT License.
