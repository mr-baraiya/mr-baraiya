# Vite + React + Fetch + CRUD + ENV Base URL

This project demonstrates how to perform **CRUD operations** in React using **Vite**, the native `fetch` API, and a `BASE_URL` loaded from `.env`.

---

## Project Structure

```

src/
├─ api/
│   └─ users.api.js
├─ pages/
│   ├─ UsersList.jsx
│   ├─ UserCreate.jsx
│   ├─ UserUpdate.jsx
├─ App.jsx
└─ main.jsx

```

---

## 🔧 Environment Variables (Vite)

Create `.env` at project root:

```

VITE_BASE_URL=[https://api.example.com](https://api.example.com)

````

Access in code:

```js
const BASE_URL = import.meta.env.VITE_BASE_URL;
````

---

## CRUD API Layer (Fetch)

`src/api/users.api.js`

```js
const BASE_URL = import.meta.env.VITE_BASE_URL;

// GET /users (list)
export async function getUsers() {
  const res = await fetch(`${BASE_URL}/users`);
  if (!res.ok) throw new Error("Failed to fetch users");
  return res.json();
}

// GET /users/:id
export async function getUser(id) {
  const res = await fetch(`${BASE_URL}/users/${id}`);
  if (!res.ok) throw new Error("Failed to fetch user");
  return res.json();
}

// POST /users (create)
export async function createUser(data) {
  const res = await fetch(`${BASE_URL}/users`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error("Failed to create user");
  return res.json();
}

// PUT /users/:id (update)
export async function updateUser(id, data) {
  const res = await fetch(`${BASE_URL}/users/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error("Failed to update user");
  return res.json();
}

// DELETE /users/:id
export async function deleteUser(id) {
  const res = await fetch(`${BASE_URL}/users/${id}`, {
    method: "DELETE",
  });
  if (!res.ok) throw new Error("Failed to delete user");
  return res.json();
}
```

---

## Component Usage (Examples)

### 1. GET List

```jsx
import { useState, useEffect } from "react";
import { getUsers, deleteUser } from "../api/users.api";

export default function UsersList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    getUsers().then(setUsers).catch(console.error);
  }, []);

  return (
    <div>
      <h2>Users List</h2>
      <ul>
        {users.map(u => (
          <li key={u.id}>
            {u.name}
            <button onClick={() => deleteUser(u.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

### 2. POST Create

```jsx
import { useState } from "react";
import { createUser } from "../api/users.api";

export default function UserCreate() {
  const [name, setName] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    createUser({ name })
      .then(() => alert("User created"))
      .catch(console.error);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        placeholder="Name"
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button>Create</button>
    </form>
  );
}
```

---

### 3. PUT Update

```jsx
import { useState, useEffect } from "react";
import { getUser, updateUser } from "../api/users.api";

export default function UserUpdate({ id }) {
  const [name, setName] = useState("");

  useEffect(() => {
    getUser(id).then(d => setName(d.name));
  }, [id]);

  function handleSubmit(e) {
    e.preventDefault();
    updateUser(id, { name })
      .then(() => alert("User updated"))
      .catch(console.error);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button>Update</button>
    </form>
  );
}
```

---

### 4. DELETE (Handled in List)

```jsx
<button onClick={() => deleteUser(id)}>Delete</button>
```

---

## Optional: Auth Token Support

```js
function authHeaders() {
  const token = localStorage.getItem("token");
  return token ? { Authorization: `Bearer ${token}` } : {};
}
```

Use:

```js
headers: {
  "Content-Type": "application/json",
  ...authHeaders(),
}
```

---

## Running the Project

Install dependencies:

```
npm install
```

Start dev:

```
npm run dev
```

Build for production:

```
npm run build
```
