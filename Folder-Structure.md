```md
# Basic Folder Structure (Vite + React)

```

```
project-root/
├─ src/
│  ├─ components/      # reusable UI components (Table, Form, etc.)
│  ├─ pages/           # route-based pages (Login, Dashboard, CRUD pages)
│  ├─ api/             # fetch() API service functions
│  ├─ hooks/           # custom hooks (optional)
│  ├─ utils/           # helpers (auth, formatters, etc.)
│  ├─ store/           # zustand/context store (optional)
│  ├─ App.jsx          # app entry + router
│  ├─ main.jsx         # Vite entry file
│  └─ index.css        # Tailwind/global styles
│
├─ public/             # public assets (images, logo, etc.)
│
├─ .env                # base URL and secret configs (VITE_BASE_URL)
├─ package.json        # dependencies + scripts
├─ vite.config.js      # Vite config
├─ README.md           # main documentation
└─ node_modules/       # auto-generated
```

---

## 🔹 **Explanation of Important Folders**

| Folder            | Purpose                                          |
| ----------------- | ------------------------------------------------ |
| `src/components/` | Buttons, Table, Form, Loader, etc                |
| `src/pages/`      | Login, Dashboard, UsersList, Create, Update      |
| `src/api/`        | fetch CRUD functions (users.api.js, auth.api.js) |
| `src/store/`      | zustand or context (optional)                    |
| `src/utils/`      | auth helpers, date utils, tokens                 |
| `public/`         | static assets                                    |
| `.env`            | VITE_BASE_URL or JWT config                      |

---

## 🔹 **Minimal Vite Folder (smallest version)**

If you want ultra small:

```
src/
├─ App.jsx
├─ main.jsx
└─ index.css
```

This is fine for learning but not for 15-page CRUD.

---

## 🔹 **For Your Placement 15-page CRUD**

Recommended expanded structure:

```
src/
├─ components/
│  ├─ Table.jsx
│  ├─ Form.jsx
│  └─ Loader.jsx
│
├─ pages/
│  ├─ Login.jsx
│  ├─ Dashboard.jsx
│  ├─ UsersList.jsx
│  ├─ UserCreate.jsx
│  ├─ UserUpdate.jsx
│  ├─ ProductsList.jsx
│  ├─ ProductCreate.jsx
│  └─ ProductUpdate.jsx
│
├─ api/
│  ├─ auth.api.js
│  ├─ users.api.js
│  └─ products.api.js
│
├─ utils/
│  └─ auth.js
│
├─ App.jsx
├─ main.jsx
└─ index.css
```
