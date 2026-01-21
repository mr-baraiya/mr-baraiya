# Zustand Guide (React + Vite + JWT Auth)

Zustand is a lightweight state management library for React.  
It is useful for storing global values like:

✔ JWT token  
✔ Logged-in user  
✔ Theme / UI state  
✔ Filters  
✔ Pagination  

---

## 📦 Install Zustand

```

npm install zustand

```

---

## 🗂 Folder Structure (Recommended)

```

src/
├─ store/
│   └─ auth.store.js
├─ api/
├─ pages/
├─ App.jsx
└─ main.jsx

````

---

# 🔐 Basic Auth Store (JWT Token + User)

`src/store/auth.store.js`

```js
import { create } from "zustand";

export const useAuth = create((set) => ({
  token: null,
  user: null,

  setToken: (token) => set({ token }),

  setUser: (user) => set({ user }),

  logout: () => set({ token: null, user: null }),
}));
````

---

## 🧪 Usage Example (Login Page)

```jsx
import { useAuth } from "../store/auth.store";
import { login } from "../api/auth.api";

export default function Login() {
  const { setToken, setUser } = useAuth();

  function handleLogin() {
    login({ email, password })
      .then((res) => {
        setToken(res.token);
        setUser(res.user);
      });
  }

  return <button onClick={handleLogin}>Login</button>;
}
```

---

## 📡 Usage with Protected Fetch (Token Header)

`src/api/users.api.js`

```js
import { useAuth } from "../store/auth.store";

export async function getUsers() {
  const token = useAuth.getState().token;

  const res = await fetch(`${BASE_URL}/users`, {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  return res.json();
}
```

---

## 🚪 Logout

Anywhere:

```js
const { logout } = useAuth();
logout();
```

This clears:

✔ token
✔ user

---

## 🔄 Access Without Re-render

Sometimes you just need token directly:

```
const token = useAuth.getState().token;
```

Useful for:

✔ fetch
✔ interceptors
✔ external helpers

---

## 🧩 Persist Token (Optional)

To persist across refresh:

```
npm install zustand/middleware
```

Update store:

```js
import { create } from "zustand";
import { persist } from "zustand/middleware";

export const useAuth = create(
  persist(
    (set) => ({
      token: null,
      user: null,
      setToken: (token) => set({ token }),
      setUser: (user) => set({ user }),
      logout: () => set({ token: null, user: null }),
    }),
    { name: "auth-store" }
  )
);
```

Now stored in **localStorage** automatically.

---

## 🔐 Protected Route Example

```jsx
import { useAuth } from "../store/auth.store";
import { Navigate } from "react-router-dom";

export function Protected({ children }) {
  const token = useAuth((s) => s.token);

  if (!token) return <Navigate to="/login" />;

  return children;
}
```

Usage:

```jsx
<Route path="/dashboard" element={
  <Protected>
    <Dashboard />
  </Protected>
} />
```

---

## 🎯 Benefits of Zustand (For This Project)

✔ No boilerplate
✔ Tiny bundle
✔ Fast to apply in 6 hours
✔ Cleaner than Context
✔ Perfect for JWT auth + CRUD
✔ Works well with fetch
✔ Easy to document in submission

---

## ✔ Summary

Use Zustand for:

✔ token
✔ user
✔ login/logout
✔ protected fetch
✔ protected routes
