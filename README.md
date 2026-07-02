# ✈️ On the Fly

A backend API for planning group trips — create trips, assign destinations, and vote on activities. Built for CodePath WEB103 Unit 5 Lab.

## Features

- View, create, update, and delete trips
- Assign a cost, date range, and description to a trip
- Assign destinations to a trip via a many-to-many join table
- View all trips associated with a given destination, and all destinations for a given trip
- Add and upvote activities for a trip

## Tech Stack

- Node.js / Express
- PostgreSQL (hosted on [Render](https://render.com))
- `pg` for database queries
- `nodemon` for local dev auto-reload

## Project Structure

```
onthefly/
├── render.yaml                  # Render Blueprint deploy config
├── onthefly.postman_collection.json
├── onthefly.postman_environment.local.json
├── onthefly.postman_environment.render.json
└── server/
    ├── server.js                # Express app entry point
    ├── config/
    │   ├── dotenv.js
    │   ├── database.js          # pg connection pool
    │   ├── reset.js             # creates tables + seeds trips
    │   └── data/data.json       # seed data
    ├── controllers/
    │   ├── trips.js
    │   ├── destinations.js
    │   ├── activities.js
    │   └── tripsDestinations.js
    └── routes/
        ├── trips.js
        ├── destinations.js
        ├── activities.js
        └── tripsDestinations.js
```

## Database Schema

- **trips** — `id, title, description, img_url, num_days, start_date, end_date, total_cost`
- **destinations** — `id, destination, description, city, country, img_url, flag_img_url`
- **activities** — `id, trip_id (FK), activity, num_votes`
- **trips_destinations** — join table, `trip_id (FK), destination_id (FK)`
- **users** — `id, githubid, username, avatarurl, accesstoken`
- **trips_users** — join table, `trip_id (FK), user_id (FK)`

## Getting Started

### 1. Install dependencies

```bash
cd server
npm install
```

### 2. Set up environment variables

Create `server/.env` with your Render PostgreSQL credentials (from the database's **Connections** tab):

```
PGDATABASE=
PGHOST=
PGPASSWORD=
PGPORT=
PGUSER=
```

### 3. Create tables and seed data

```bash
npm run reset
```

### 4. Run the server locally

```bash
npm run dev      # resets DB + starts with nodemon (auto-reload)
# or
npm start        # starts server only, no reset (used in production)
```

The API runs at `http://localhost:3001` by default (or `process.env.PORT`).

## API Endpoints

### Trips — `/api/trips`
| Method | Route | Description |
|---|---|---|
| GET | `/` | Get all trips |
| GET | `/:id` | Get a trip by id |
| POST | `/` | Create a trip |
| PATCH | `/:id` | Update a trip |
| DELETE | `/:id` | Delete a trip (and its activities/destination links) |

### Destinations — `/api/destinations`
| Method | Route | Description |
|---|---|---|
| GET | `/` | Get all destinations |
| GET | `/:id` | Get a destination by id |
| POST | `/` | Create a destination |
| PATCH | `/:id` | Update a destination |
| DELETE | `/:id` | Delete a destination |

### Activities — `/api/activities`
| Method | Route | Description |
|---|---|---|
| GET | `/` | Get all activities |
| GET | `/:trip_id` | Get all activities for a trip |
| POST | `/:trip_id` | Add an activity to a trip |
| PATCH | `/:id` | Upvote an activity |
| DELETE | `/:id` | Delete an activity |

### Trips-Destinations — `/api/trips-destinations`
| Method | Route | Description |
|---|---|---|
| GET | `/` | Get all trip-destination links |
| GET | `/trips/:destination_id` | Get all trips for a destination |
| GET | `/destinations/:trip_id` | Get all destinations for a trip |
| POST | `/` | Link a trip to a destination |

## Testing with Postman

Import `onthefly.postman_collection.json` along with the environment files (`onthefly.postman_environment.local.json` for local dev, `onthefly.postman_environment.render.json` for the deployed API). Switch environments from the dropdown in the top-right of Postman — all requests use a shared `{{baseUrl}}` variable.

## Deploying to Render

This repo includes a `render.yaml` Blueprint:

1. Push this repo to GitHub.
2. In Render, click **New → Blueprint** and connect the repo.
3. Render reads `render.yaml` and creates a free web service rooted at `server/`.
4. Fill in the 5 database env vars (`PGDATABASE`, `PGHOST`, `PGPASSWORD`, `PGPORT`, `PGUSER`) when prompted.
5. Deploy — Render will build with `npm install` and start with `npm start`.