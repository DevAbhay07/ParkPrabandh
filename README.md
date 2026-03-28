# Park-Prabandh Smart Parking Management System

Park-Prabandh is a smart parking platform with:

- A React + TypeScript frontend for users and admins
- A Flask + OpenCV + YOLO backend for live parking analytics and camera streaming
- Real-time updates through Socket.IO

This README is intentionally detailed and aligned with the current repository state.

## What You Get Today

| Area | Status | Notes |
|---|---|---|
| Public pages | Implemented | Homepage, Login, Register |
| User dashboard | Implemented | User Home route and booking-related components |
| Admin dashboard | Implemented | Dashboard, Slots, Analytics, Users, Violations |
| Live slot/system stats | Implemented | Pulled from Flask endpoints + pushed via Socket.IO |
| CCTV feed streaming | Implemented | MJPEG streams from backend video sources |
| AI overlays on feeds | Implemented | YOLO-based occupied/free detection boxes |
| Booking workflow | Partially simulated | Frontend service currently uses simulated latency/errors |
| Full auth backend | Partially integrated | Frontend supports token flow; backend auth APIs are not part of current Flask app |
| Tests | Not configured in scripts | No test scripts in frontend package scripts yet |

## Core Features

- Real-time parking overview with slot occupancy and system metrics
- Multi-role frontend flows (public, user, admin)
- Admin monitoring experience with analytics, alerts, and AI activity dashboard
- OpenCV + YOLO inference overlays for parking footage
- Socket.IO connection lifecycle and live event handling
- Mobile-friendly responsive UI with Tailwind CSS
- PWA support via Vite PWA plugin

## Tech Stack

### Frontend

- React 18 + TypeScript
- Vite 4
- Tailwind CSS
- Redux Toolkit + React Redux
- TanStack React Query
- React Router v6 (lazy loaded pages)
- Socket.IO client
- React Hook Form + Zod
- Recharts
- Three.js + react-three-fiber + drei

### Backend

- Flask
- Flask-CORS
- Flask-SocketIO
- OpenCV (opencv-python-headless)
- Ultralytics YOLO
- cvzone
- python-dotenv
- Gunicorn (production server)

## Repository Structure

```text
ParkPrabandh/
├── backend/
│   ├── app.py
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── admin/
│   │   │   ├── booking/
│   │   │   ├── layout/
│   │   │   ├── routing/
│   │   │   └── shared/
│   │   ├── pages/
│   │   │   ├── admin/
│   │   │   ├── auth/
│   │   │   └── user/
│   │   ├── services/
│   │   ├── store/
│   │   ├── types/
│   │   ├── hooks/
│   │   ├── config/
│   │   ├── utils/
│   │   ├── validation/
│   │   └── styles/
│   ├── public/
│   ├── package.json
│   ├── vite.config.ts
│   ├── .env.example
│   └── .env.production
├── netlify.toml
└── README.md
```

## Frontend Routes (Current)

| Route | Access | Component |
|---|---|---|
| `/` | Public | Homepage |
| `/login` | Public | LoginPage |
| `/register` | Public | RegisterPage |
| `/user-home` | Auth required | UserHome |
| `/admin` | Admin only | AdminDashboard |
| `/admin/slots` | Admin only | SlotManagement |
| `/admin/analytics` | Admin only | AnalyticsDashboard |
| `/admin/users` | Admin only | UserManagement |
| `/admin/violations` | Admin only | ViolationDetection |
| `*` | Any | 404 fallback |

## Backend API (Current)

### REST Endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/health` | Liveness check |
| GET | `/slot_stats` | Current occupancy counters |
| GET | `/system_metrics` | Revenue, active vehicles, violations |
| GET | `/video_feed_1` | MJPEG stream for camera/video source 1 |
| GET | `/video_feed_2` | MJPEG stream for camera/video source 2 |

### Socket.IO Events

Server-side implemented events:

- `connect` (server emits initial `slot_updates`)
- `subscribe` (client -> server)
- `disconnect`
- `slot_updates` (server -> client)
- `stats-update` (server -> client)

Frontend client also listens to additional business events such as `booking-update`, `violation-alert`, and `payment-status`; those need corresponding backend emitters to be fully active.

## Environment Variables

### Frontend

Use `frontend/.env.example` as baseline.

```bash
VITE_API_URL=http://localhost:5000
VITE_API_MARKET_KEY=your_api_market_key_here
```

Production example exists in `frontend/.env.production`.

### Backend

The backend reads env vars via `python-dotenv` if a `.env` file exists in `backend/`, or from shell environment.

Supported keys:

- `PORT` (default: `5000`)
- `FRONTEND_URL` (default: `*`)
- `MODEL_PATH` (default: `final_model.pt`)
- `VIDEO_PATH_1` (default: `Parking_Lot_CCTV_1.mp4`)
- `VIDEO_PATH_2` (default: `Parking_Lot_CCTV_2.1.mp4`)

## Local Development Setup

## 1. Clone

```bash
git clone <repository-url>
cd ParkPrabandh
```

## 2. Start Backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Optional: create backend/.env for overrides
# Example:
# PORT=5000
# FRONTEND_URL=http://localhost:3006

python app.py
```

Backend URLs once running:

- http://localhost:5000/health
- http://localhost:5000/slot_stats
- http://localhost:5000/system_metrics
- http://localhost:5000/video_feed_1
- http://localhost:5000/video_feed_2

## 3. Start Frontend

Open a new terminal:

```bash
cd frontend
npm install

# Optional: create frontend/.env from frontend/.env.example
# cp .env.example .env

npm run dev
```

Frontend dev server default: http://localhost:3006

## 4. Build Frontend

```bash
cd frontend
npm run build
npm run preview
```

## Frontend Scripts (Current)

From `frontend/package.json`:

- `npm run dev`
- `npm run build`
- `npm run preview`
- `npm run lint`
- `npm run type-check`

Note: test scripts (`test`, `test:e2e`, etc.) are not currently defined.

## AI and Vision Pipeline

- YOLO model is loaded once at backend startup
- Frames are resized and inferred for class labels:
  - `occupied_slot`
  - `free_slot`
- Bounding boxes are drawn with cvzone
- Background sampler periodically updates slot and system metrics
- If model/video is unavailable, backend gracefully falls back to placeholder frames and retained/default stats

## Deployment Notes

## Frontend (Netlify)

Repository already contains a Netlify configuration in `netlify.toml`:

- Build base: `frontend`
- Build command: `npm run build`
- Publish directory: `dist`
- Node version: `18`
- SPA redirect configured: `/* -> /index.html`

## Backend

Frontend production env points to a Render-hosted backend URL pattern in `frontend/.env.production`.
Deploy backend to your preferred Python host (Render recommended by existing project comments), then set `VITE_API_URL` accordingly.

## UI and Media Gallery

### Branding

![Park Prabandh Logo](frontend/public/images/image.png)

### Venue and Parking Context Samples

![Ambience Mall](frontend/src/pages/user/venue-images/ambience-mall.jpg)
![Apollo Hospital](frontend/src/pages/user/venue-images/apollo.jpg)
![Cyber Hub](frontend/src/pages/user/venue-images/dlf-cyberhub.jpg)
![GVK One](frontend/src/pages/user/venue-images/gvk.jpg)
![Inorbit](frontend/src/pages/user/venue-images/inorbit.jpg)
![Manyata Tech Park](frontend/src/pages/user/venue-images/manyata.jpg)
![Phoenix Marketcity](frontend/src/pages/user/venue-images/phoenix.jpg)
![Select Citywalk](frontend/src/pages/user/venue-images/select-citywalk.jpg)

These assets can be used in user-facing venue selection, demos, and landing page storytelling.

## Security and Access Model

- Frontend has role-based route guards using Redux auth state
- Token is attached to outgoing API calls through Axios interceptors
- On unauthorized responses, client clears auth state and redirects to login
- Netlify security headers are configured in `netlify.toml`

Important: Current Flask backend in this repository does not yet implement full JWT auth endpoints. Treat frontend auth as partially integrated until backend auth routes are added.

## Known Limitations

- Several frontend service modules still use simulated data/latency for parts of booking flow
- Additional websocket business events are expected by frontend but not emitted by current backend
- No automated test suite scripts are wired in package scripts
- No `LICENSE` file is present at repository root yet

## Suggested Next Improvements

1. Add backend auth endpoints and token validation for Socket.IO connections
2. Replace simulated booking/payment service calls with real backend APIs
3. Add automated tests (unit + integration + e2e) and CI workflow
4. Add a root backend env template (`backend/.env.example`)
5. Add API contract docs (OpenAPI/Swagger) for frontend-backend alignment

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make focused changes with clear commit messages
4. Open a pull request with reproduction steps and screenshots (if UI changes)

## Support

If you are evaluating or extending this project, open an issue in the repository and include:

- Environment details (OS, Node, Python)
- Steps to reproduce
- Logs/screenshots
- Expected vs actual behavior

---

Park-Prabandh aims to make parking operations more intelligent, observable, and user-friendly through real-time software + AI-assisted monitoring.