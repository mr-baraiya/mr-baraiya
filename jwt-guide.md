# JWT Authentication Guide (React + Vite + Fetch)

This guide explains how to implement **JWT-based authentication** in a Vite + React project using:

- Login with JWT
- Storing token securely
- Sending token in Authorization headers
- Fetching protected data
- Logout & token cleanup

---

## What is JWT?

**JWT (JSON Web Token)** is used for authentication.  
After login, backend returns a token that must be included in future API requests.

Typical login response:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
````

---

## Environment Variable (Base URL)

`.env`

```
VITE_BASE_URL=https://api.example.com
```

Access:

```js
const BASE_URL = import.meta.env.VITE_BASE_URL;
```

---

## Login (POST /auth/login)

`src/api/auth.api.js`

```js
const BASE_URL = import.meta.env.VITE_BASE_URL;

export async function login(data) {
  const res = await fetch(`${BASE_URL}/auth/login`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });

  if (!res.ok) throw new Error("Login failed");

  return res.json(); // expects { token }
}
```

---

## Token Storage

JWT can be stored in:

* `localStorage` (simple)
* `sessionStorage` (session only)
* memory store (secure but resets on refresh)

Placement-friendly method:

```js
localStorage.setItem("token", response.token);
```

Retrieve:

```js
const token = localStorage.getItem("token");
```

---

## Auth Headers for Protected APIs

Reusable helper:

```js
function authHeaders() {
  const token = localStorage.getItem("token");
  return token ? { Authorization: `Bearer ${token}` } : {};
}
```

Usage:

```js
fetch(`${BASE_URL}/users`, {
  headers: {
    ...authHeaders(),
  }
});
```

---

## Example Protected API (GET /users)

`src/api/users.api.js`

```js
export async function getUsers() {
  const res = await fetch(`${BASE_URL}/users`, {
    headers: {
      ...authHeaders(),
    },
  });

  if (!res.ok) throw new Error("Unauthorized or Failed");
  return res.json();
}
```

If token invalid → backend sends `401 Unauthorized`.

---

## Logout

Clear token:

```js
localStorage.removeItem("token");
```

Then redirect to login page.

---

## Protected Routes (React Router)

Example:

```jsx
function ProtectedRoute({ children }) {
  const token = localStorage.getItem("token");

  if (!token) {
    return <Navigate to="/login" />;
  }

  return children;
}
```

Usage:

```jsx
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  }
/>
```

---

## Token Refresh (Optional)

Some backends return:

* `accessToken` (short life)
* `refreshToken` (long life)

Flow:

```
login -> get access + refresh -> use access -> refresh when 401
```

Not required for basic placement tasks unless given by Swagger.

---

## Swagger Compatibility Notes

Check login endpoint for response pattern:

```
{
  "token": "...",
  "user": { ... }
}
```

Or sometimes:

```
{
  "accessToken": "...",
  "refreshToken": "..."
}
```

Adjust according to API spec.

---

## Security Notes (Short)

**Avoid storing JWT in:**
✔ cookies without httpOnly
✔ query params
✔ localStorage for sensitive production apps (but ok for college + placement use)

For enterprise use, consider:

* httpOnly cookies
* CSRF protection

---

## Summary

✔ Login → store JWT
✔ Add token in Authorization header for protected APIs
✔ Protect routes → redirect if no token
✔ Logout → remove token
✔ Works with Swagger & placement tests
