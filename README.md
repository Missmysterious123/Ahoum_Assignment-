# Ahoum Sessions Marketplace

A full-stack web application built for the Ahoum hiring assignment. Users can browse and book spiritual sessions, and creators can list and manage their own sessions.

---

## Tech Stack

- **Frontend:** React 18 + Vite, React Router v6
- **Backend:** Django 4.2 + Django REST Framework
- **Database:** PostgreSQL 15
- **Auth:** Google OAuth 2.0 with JWT
- **Infrastructure:** Docker, docker-compose, Nginx

> The assignment mentioned Next.js but I used React with Vite. All required features are implemented and I can explain my decisions in the interview.

---

## Getting Started

### What you need
- Docker Desktop installed and running
- Google OAuth Client ID and Secret

### Steps

1. Clone the repo
```
git clone <your-repo-url>
cd ahoum
```

2. Create your env file
```
cp .env.example .env
```
Open .env and fill in your Google credentials and a secret key.

3. Run the app
```
docker-compose up --build
```

Open http://localhost in your browser once you see "Starting Gunicorn" in the logs.

---

## Google OAuth Setup

1. Go to https://console.cloud.google.com
2. Create a project
3. Go to APIs & Services → Credentials
4. Click Create Credentials → OAuth 2.0 Client ID
5. Select Web application
6. Add this as an authorized redirect URI:
   http://localhost/auth/callback
7. Copy the Client ID and Secret into your .env file
8. Go to OAuth consent screen and add your Gmail as a test user

---

## Folder Structure

```
ahoum/
├── backend/
│   ├── ahoum_project/      # Django settings and main URLs
│   ├── apps/
│   │   ├── users/          # User model, Google OAuth, JWT, profile API
│   │   ├── sessions_app/   # Sessions CRUD (creator side)
│   │   └── bookings/       # Booking and cancellation logic
│   ├── entrypoint.sh       # Runs migrations then starts Gunicorn
│   └── requirements.txt
│
├── frontend/
│   ├── src/
│   │   ├── api/            # Axios client with JWT auto-refresh
│   │   ├── context/        # Global auth state
│   │   ├── components/     # Navbar, session cards
│   │   └── pages/          # All pages
│   └── Dockerfile
│
├── nginx/
│   └── nginx.conf          # /api/ goes to Django, everything else to React
│
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## API Endpoints

### Auth
- POST /api/auth/google/callback/ — exchange Google code for JWT
- POST /api/auth/token/refresh/ — refresh access token
- GET /api/auth/profile/ — get current user
- PATCH /api/auth/profile/ — update profile
- POST /api/auth/switch-role/ — switch between user and creator

### Sessions
- GET /api/sessions/ — public list, supports search and category filter
- GET /api/sessions/:id/ — session detail
- GET /api/sessions/my/ — creator: list own sessions
- POST /api/sessions/my/ — creator: create a session
- PATCH /api/sessions/my/:id/ — creator: update a session
- DELETE /api/sessions/my/:id/ — creator: delete a session
- GET /api/sessions/my/bookings/ — creator: see bookings on their sessions

### Bookings
- GET /api/bookings/ — list my bookings
- POST /api/bookings/ — book a session
- POST /api/bookings/:id/cancel/ — cancel a booking

---

## What I Built

**Auth**
- Google OAuth login flow
- Backend issues JWT access and refresh tokens
- Tokens auto-refresh when expired
- Two roles: User and Creator
- Users can switch roles from their profile page

**Sessions**
- Public catalog with search and category filter
- Session detail page
- Creators can create, edit and delete sessions
- Sessions can be draft or published

**Bookings**
- Book any published session
- Prevents duplicate bookings
- Checks if session is full before booking
- Creators cannot book their own sessions
- Users can cancel upcoming bookings
- Dashboard shows upcoming and past bookings separately

**Dashboards**
- User dashboard: view and cancel bookings
- Creator dashboard: manage sessions, see who booked

---

## How to Test It

**As a User:**
1. Open http://localhost
2. Sign in with Google
3. Click any session and hit Book Now
4. Go to My Bookings to see it

**As a Creator:**
1. Sign in and go to Profile
2. Click Switch to Creator
3. Go to Creator Dashboard
4. Click New Session, fill the form, save it
5. Go back to home page and your session will be listed

---

## Useful Commands

```
# Start
docker-compose up --build

# Stop
docker-compose down

# Reset database
docker-compose down -v

# See backend logs
docker-compose logs -f backend

# Create admin account
docker-compose exec backend python manage.py createsuperuser
```

---

## Environment Variables

Check .env.example for the full list. The important ones are:

- DJANGO_SECRET_KEY — any long random string
- GOOGLE_OAUTH2_CLIENT_ID — from Google Cloud Console
- GOOGLE_OAUTH2_CLIENT_SECRET — from Google Cloud Console
- POSTGRES_PASSWORD — set any password you want
