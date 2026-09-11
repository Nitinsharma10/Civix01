# Civix — Civic Issue Reporting Platform

A full-stack civic platform where residents report local issues and admins triage, manage, and resolve them.

> This README reflects the latest **Admin Issue List UI update** — image thumbnails, a click-to-preview modal, stronger hover clarity, and high-severity highlighting.

-----

## Table of Contents

- [What's New (Admin UI Update)](#whats-new-admin-ui-update)
- [Core Features](#core-features)
- [Tech Stack](#tech-stack)
- [Architecture (High-Level)](#architecture-high-level)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [API Reference](#api-reference)
- [Roles & Permissions](#roles--permissions)
- [Scripts](#scripts)
- [Contributing](#contributing)
- [License](#license)

---

## What's New (Admin UI Update)

### 1. Admin Issue List Item Redesign

Each issue item now uses a clean two-column layout:

- **Left:** image thumbnail (`~96x96`), rounded corners, `object-cover`.
- **Right:** title, description, and a meta row (`Category | Department | Severity | Time`).

### 2. Hover Clarity Improvement

On issue-card hover:

- All issue text (title, description, category, department, severity, timestamp) transitions to white.
- Background darkens for stronger contrast.
- Transitions are smooth (`transition-colors`, `duration-200/300`).

### 3. Click-to-Preview Image (New Modal)

Admins can click an issue thumbnail to open a larger preview:

- Dark overlay backdrop
- Centered large image (`object-contain`, aspect ratio preserved)
- Close button
- Click-outside-to-close
- Escape key to close
- Smooth fade/zoom-in animation

**New reusable component:**
`frontend/components/admin/image-preview-modal.tsx` → `ImagePreviewModal`

### 4. High-Severity Visual Priority

For issues where `severityScore > 8.5`:

- Dark red gradient border wrapper
- Stronger hover treatment
- Hover text still turns white

### Updated Components

- `frontend/components/admin/issue-list-item.tsx`
- `frontend/components/admin/image-preview-modal.tsx`
- `frontend/app/admin/issues/page.tsx` (now passes an `imageUrl` prop)

---

## Core Features

### Resident Features

- Report issues with title, description, image, and location.
- AI enrichment for category, department, and severity.
- Feed with upvotes and comments.
- Map view of reported issues.
- Resolution verification via proof upvotes.

### Admin Features

- Manage the issue lifecycle (`reported` → `approved` → `in_progress` → `resolved`).
- Edit issue metadata (severity, department, status).
- Resolve issues with a proof image upload.
- Admin map and analytics dashboards.
- Enhanced issue list UI (this update).

---

## Tech Stack

### Frontend

- Next.js (App Router)
- React + TypeScript
- Tailwind CSS
- Clerk (Auth)
- Lucide React (Icons)
- Leaflet (Maps)

### Backend

- Node.js + Express
- MongoDB + Mongoose
- Clerk Express SDK
- Cloudinary (image hosting)
- n8n + ML service integration (issue enrichment)

---

## Architecture (High-Level)

1. Client submits a reported issue.
2. Backend enriches the data (AI + rules) and stores it in MongoDB.
3. Admin reviews and updates the issue status.
4. Admin resolves the issue with a proof image.
5. Residents verify the resolution.

---

## Project Structure

```text
Micro-Task/
├── backend/                  # Express API + models + controllers
├── frontend/                 # Next.js app
│   ├── app/                  # App Router pages
│   ├── components/           # Reusable UI components
│   │   └── admin/            # Admin-specific components
│   │       ├── issue-list-item.tsx
│   │       └── image-preview-modal.tsx
│   └── lib/                  # Helpers/utilities
└── README.md
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm 9+
- MongoDB database
- Clerk API keys
- Cloudinary credentials

### One-Time Setup (Monorepo Root)

```bash
npm install
npm run install:all
```

Create your env files:

- `backend/.env` from `backend/.env.example`
- `frontend/.env` from `frontend/.env.example`

### Run Full Stack Together (Monorepo Root)

```bash
npm run dev
```

This starts:

- Backend on `http://localhost:5500`
- Frontend on `http://localhost:3000`

### Backend Only

```bash
cd backend
npm install
npm run dev
```

Default backend URL: `http://localhost:5500`

### Frontend Only

```bash
cd frontend
npm install
npm run dev
```

Default frontend URL: `http://localhost:3000`

---

## Environment Variables

### Backend (`backend/.env`)

```env
PORT=5500
MONGO_URI=...
CLERK_PUBLISHABLE_KEY=...
CLERK_SECRET_KEY=...
CLOUDINARY_URL=...
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
N8N_WEBHOOK_URL=...
ML_SERVICE_URL=http://127.0.0.1:8000/analyze-severity
ML_CONFIDENCE_THRESHOLD=0.80
```

### Frontend (`frontend/.env`)

```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=...
CLERK_SECRET_KEY=...
NEXT_PUBLIC_CLERK_SIGN_IN_FALLBACK_REDIRECT_URL=/feed
NEXT_PUBLIC_CLERK_SIGN_UP_FALLBACK_REDIRECT_URL=/feed
NEXT_PUBLIC_BACKEND_URL=http://localhost:5500
SARVAM_API_KEY=...
```

---

## API Reference

### Issues

| Method | Endpoint | Description |
|--------|----------|--------------|
| `POST` | `/api/issues/preview` | AI-enriched preview |
| `POST` | `/api/issues` | Create issue |
| `GET` | `/api/issues` | List issues |
| `PATCH` | `/api/issues/:id` | Admin update |
| `PATCH` | `/api/issues/:id/resolve` | Admin resolves with proof |
| `PATCH` | `/api/issues/:id/upvote` | Upvote toggle |
| `PATCH` | `/api/issues/:id/verify-upvote` | Verification upvote |
| `GET` | `/api/issues/:id/comments` | List comments |
| `POST` | `/api/issues/:id/comments` | Add comment |

### Users

| Method | Endpoint | Description |
|--------|----------|--------------|
| `POST` | `/api/users/sync` | Sync user from Clerk |
| `GET` | `/api/users/me` | Get current user |

### Events

| Method | Endpoint | Description |
|--------|----------|--------------|
| `GET` | `/api/events` | List events |
| `POST` | `/api/events` | Create event |
| `PATCH` | `/api/events/:id/interested` | Toggle interest |
| `PATCH` | `/api/events/:id/participating` | Toggle participation |

---

## Roles & Permissions

| Role | Permissions |
|------|-------------|
| **Resident** | Report, view, comment, upvote, verify |
| **Admin** | Everything a resident can do, plus edit/update/resolve issues and access admin dashboards |

---

## Scripts

### Backend

```bash
npm run dev
npm start
```

### Frontend

```bash
npm run dev
npm run build
npm start
npm run lint
```

---

## Contributing

Contributions are welcome! Please open an issue or submit a pull request describing your changes.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Notes

- This update is UI-focused and does not change backend API contracts.
- For the best admin UX, ensure issue records include a valid `imageUrl`.
