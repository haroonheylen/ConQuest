# ConQuest 🎭

> Full-stack event management platform for comic con organizers. Built with React, Ruby on Rails, and PostgreSQL — featuring JWT auth, role-based access control, venue slot booking, and a live business analytics dashboard.

**[Live Demo](https://conquest.vercel.app)** · **[API](https://conquest-api.onrender.com)**

> 🔐 Demo credentials  
> Attendee: `attendee@conquest.com` / `demo1234`  
> Business: `business@conquest.com` / `demo1234`

-----

## Overview

ConQuest is a comic con event platform where attendees buy tickets and join competitions, while businesses manage venue rentals and track engagement — all from one dashboard.

The project was built to demonstrate full-stack ownership across a real-world domain: relational data modeling, role-based authorization, server-state management, and a production deployment across two platforms.

-----

## Features

### Attendee

- Register and log in as an attendee
- Purchase a standard or VIP ticket
- Browse and enroll in competitions created by businesses
- View enrollment status and ticket details from a personal dashboard

### Business

- Register and log in as a business
- Browse and rent venue slots (booth, stage, or hall) across available time slots
- Create, edit, and delete competitions for attendees to join
- Access a live dashboard showing:
  - All rented venues with time slots and costs
  - Total rental expenditure
  - All competitions with real-time enrollment counts and fill percentages
  - Overall event engagement summary

-----

## Tech Stack

|Layer           |Technology                   |
|----------------|-----------------------------|
|Frontend        |React 18 + Vite + TailwindCSS|
|Routing         |React Router v6              |
|Server State    |TanStack Query v5            |
|HTTP Client     |Axios                        |
|Forms           |react-hook-form + Zod        |
|Backend         |Ruby on Rails 7.1 (API mode) |
|Authentication  |Devise + devise-jwt          |
|Authorization   |Pundit                       |
|Serialization   |Blueprinter                  |
|Pagination      |Kaminari                     |
|Database        |PostgreSQL 16                |
|Frontend Hosting|Vercel                       |
|Backend Hosting |Render                       |

-----

## Entity Relationship Diagram

```
User (role: attendee | business)
├── AttendeeProfile
│   ├── Ticket (type: standard | vip)
│   └── CompetitionEnrollment(s) ──→ Competition(s)
└── BusinessProfile
    ├── VenueRental(s) ──→ VenueSlot(s) (type: booth | stage | hall)
    └── Competition(s)
          └── CompetitionEnrollment(s)
```

> A full ERD diagram is available in `/docs/erd.png`, generated via the `rails-erd` gem.

-----

## Architecture Notes

**Authentication** — JWT tokens are issued on login and sent via the `Authorization: Bearer` header on every subsequent request. Token revocation on logout is handled via a `JwtDenylist` table.

**Authorization** — Pundit policies govern access at the controller level. Role checks are centralized in policy classes rather than scattered across controllers.

**Double-booking prevention** — A database-level unique index on `VenueSlot` prevents concurrent bookings from creating duplicate rentals, regardless of application-layer race conditions.

**Dashboard endpoint** — The business dashboard is driven by a single `GET /api/v1/businesses/dashboard` endpoint that returns pre-aggregated rentals, competitions, and summary stats in one response — minimizing round trips and keeping the frontend thin.

**Frontend auth** — An `AuthContext` stores the current user and JWT. A `RoleGuard` wrapper component protects routes by role, redirecting unauthorized users before rendering.

-----

## Local Setup

### Prerequisites

- Ruby 3.3+
- Rails 7.1+
- Node 20+
- PostgreSQL 16+

### Backend

```bash
git clone https://github.com/yourusername/conquest.git
cd conquest/conquest-api

bundle install
cp .env.example .env        # add your DB credentials and JWT secret
rails db:create db:migrate db:seed
rails server                # runs on http://localhost:3000
```

### Frontend

```bash
cd conquest/conquest-client

npm install
cp .env.example .env        # set VITE_API_URL=http://localhost:3000/api/v1
npm run dev                 # runs on http://localhost:5173
```

### Environment Variables

**Backend (`.env`)**

```
DATABASE_URL=postgresql://localhost/conquest_development
JWT_SECRET_KEY=your_secret_here
FRONTEND_URL=http://localhost:5173
```

**Frontend (`.env`)**

```
VITE_API_URL=http://localhost:3000/api/v1
```

-----

## API Reference

```
POST   /api/v1/auth/sign_up
POST   /api/v1/auth/sign_in
DELETE /api/v1/auth/sign_out

GET    /api/v1/businesses/dashboard
GET    /api/v1/businesses/venue_slots
POST   /api/v1/businesses/venue_rentals
DELETE /api/v1/businesses/venue_rentals/:id
GET    /api/v1/businesses/competitions
POST   /api/v1/businesses/competitions
PUT    /api/v1/businesses/competitions/:id
DELETE /api/v1/businesses/competitions/:id
GET    /api/v1/businesses/competitions/:id/enrollments

GET    /api/v1/attendees/me
POST   /api/v1/attendees/ticket
GET    /api/v1/attendees/competitions
POST   /api/v1/attendees/competitions/:id/enroll
DELETE /api/v1/attendees/competitions/:id/unenroll
```

A full Postman collection is available in `/docs/conquest.postman_collection.json`.

-----

## Seed Data

The seed file generates a realistic dataset for demo purposes:

- **84 venue slots** across booths, stages, and halls with 3 time slots each
- **15 business users** each with 1–3 venue rentals and 1–2 competitions
- **100 attendee users** with randomized ticket purchases and competition enrollments

```bash
rails db:seed
```

-----

## License

[MIT](./LICENSE) © Your Name
