# Hospital Management API

Assignment 2 — Ashutosh Pawar (150096725130)

A hospital management REST API built with Express and MongoDB, with local authentication
(Passport + bcryptjs), plus a React frontend that consumes the API.

## Tech stack

**Backend (`server/`)**

- Express 5
- MongoDB via Mongoose
- Passport (`passport-local`) for login
- bcryptjs for password hashing
- CORS enabled for the frontend

**Frontend (`frontend/hospital-webapp/`)**

- React 19 + Vite
- React Router for pages
- SweetAlert2 for alerts

## Project structure

```
Ashutosh_Pawar_150096725130/
├── server/
│   ├── config/
│   │   ├── db.js             # mongoose connection
│   │   └── passport.js       # local strategy
│   ├── models/
│   │   ├── Hospitals.js      # hospital schema
│   │   └── Users.js          # user schema
│   ├── router/
│   │   ├── authThang.js      # /register, /login
│   │   └── hospitalThang.js  # /hospitals CRUD
│   ├── requests.http         # sample requests
│   └── server.js             # app entry, port 4000
└── frontend/
    └── hospital-webapp/
        └── src/
            ├── api.js        # API_BASE_URL
            ├── App.jsx       # routes
            └── components/   # Home, Register, Login, Hospitals, AddHospital, EditHospital
```

## Setup

Requires Node.js and a local MongoDB running on `mongodb://localhost:27017`
(database `itm_hospital_api`, created automatically).

**Backend**

```bash
cd server
npm install
node server.js
```

Server runs at `http://localhost:4000`.

**Frontend**

```bash
cd frontend/hospital-webapp
npm install
npm run dev
```

Vite prints the dev URL (default `http://localhost:5173`). The frontend talks to
`http://localhost:4000`, set in `src/api.js`.

## Data models

**Hospital**

| Field | Type | Notes |
|---|---|---|
| `name` | String | required |
| `city` | String | required |
| `totalBeds` | Number | required, min 0 |
| `availableBeds` | Number | required, min 0, cannot exceed `totalBeds` |

**User**

| Field | Type | Notes |
|---|---|---|
| `username` | String | required, unique |
| `email` | String | required, unique |
| `password` | String | required, stored as a bcrypt hash |

## API endpoints

Base URL: `http://localhost:4000`

### Auth

| Method | Endpoint | Description |
|---|---|---|
| POST | `/register` | Create a user. Body: `username`, `email`, `password`. Rejects duplicate username or email. |
| POST | `/login` | Log in with `username` and `password`. Returns the user on success, `401` otherwise. |

### Hospitals

| Method | Endpoint | Description |
|---|---|---|
| GET | `/hospitals` | List all hospitals. |
| GET | `/hospitals/available` | List hospitals with `availableBeds > 0`. |
| GET | `/hospitals/:id` | Get one hospital by id. `404` if missing. |
| POST | `/hospitals` | Create a hospital. Body: `name`, `city`, `totalBeds`, `availableBeds`. |
| PUT | `/hospitals/:id` | Update a hospital. Same body as create. |
| DELETE | `/hospitals/:id` | Delete a hospital. |

### Example

```http
POST http://localhost:4000/hospitals
Content-Type: application/json

{
  "name": "City Care Hospital",
  "city": "Nagpur",
  "totalBeds": 120,
  "availableBeds": 45
}
```

Response `201`:

```json
{
  "message": "hospital created successfully!!!!",
  "hospital": {
    "_id": "...",
    "name": "City Care Hospital",
    "city": "Nagpur",
    "totalBeds": 120,
    "availableBeds": 45
  }
}
```

## Status codes

| Code | When |
|---|---|
| 200 | Successful read, update, delete, or login |
| 201 | Resource created |
| 400 | Missing field, invalid id, `availableBeds > totalBeds`, duplicate username/email |
| 401 | Invalid login credentials |
| 404 | Hospital not found |
| 500 | Server or database error |

More sample requests are in `server/requests.http`.
