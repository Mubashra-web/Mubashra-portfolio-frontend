# Portfolio Frontend

Static frontend for the `portfolio-backend` API — a public portfolio site plus a
protected admin panel to manage its content (profile, projects, skills,
experience, education).

```
portfolio-frontend/
│
├── index.html                # Public portfolio homepage
├── README.md
│
├── css/
│   └── style.css              # Public website CSS
│
├── js/
│   ├── config.js              # API_BASE_URL + shared helpers
│   └── main.js                # Public website JS (fetches & renders content)
│
├── images/
│   ├── projects/               # optional local project images
│   └── icons/                  # optional skill icons
│
└── admin/
    │
    ├── index.html              # Admin login + dashboard
    │
    ├── css/
    │   └── admin.css
    │
    └── js/
        ├── api.js               # fetch wrapper for the backend REST API
        └── app.js                # dashboard UI logic (forms, lists, CRUD)
```

## 1. Start the backend

From `portfolio-backend/`:

```bash
cp .env.example .env      # fill in MONGO_URI, JWT_SECRET, FRONTEND_URL
npm install
npm run seed               # optional: creates the first admin user
npm run dev                 # starts on http://localhost:5000
```

Make sure `FRONTEND_URL` in the backend's `.env` matches the URL you'll serve
this frontend from (CORS is locked to that one origin).

## 2. Serve this frontend

Any static file server works, e.g. from inside `portfolio-frontend/`:

```bash
npx serve .
# or
python3 -m http.server 5500
```

Then open `http://localhost:5500` (public site) and
`http://localhost:5500/admin/` (admin login).

## 3. Point the frontend at your backend

By default everything calls `http://localhost:5000` (see `js/config.js`).
To change it:

- Open the admin panel → **Settings** → set **Backend API URL**, or
- From the browser console: `setApiBase('https://your-api.example.com')`

The value is stored in `localStorage`, shared by the public site and the
admin panel.

## 4. Log in to the admin panel

Use the admin username/password created via the backend's `npm run seed`
script (or however you created your first `Admin` document). The admin panel
talks to:

- `POST /api/auth/login`, `/logout`, `/change-password`
- `GET/PUT /api/profile`, `POST/DELETE /api/profile/picture`
- `GET/POST/PUT/DELETE /api/projects`, `/api/skills`, `/api/experience`, `/api/education`

All write operations send the JWT from login as `Authorization: Bearer <token>`.

## Notes

- The public homepage falls back to placeholder demo content if the backend
  is unreachable or has no data yet, so the page never looks broken.
- Profile pictures and any other uploaded files are served by the backend at
  `/uploads/...`; the frontend resolves those relative paths against the
  configured API URL automatically (see `assetUrl()` in `js/config.js`).
