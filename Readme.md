# neoRMS

neoRMS is a multi-service restaurant management platform organized as a root repository with Git submodules.
The root repo coordinates service repositories for backend, AI server, management UI, customer UI, waiter UI, and chef UI.

## Architecture

This repository contains the following submodules:

| Service | Path | Type | Default Local URL |
|---|---|---|---|
| Backend API | `neoRMS-backend` | Node.js + TypeScript + Express + Prisma | `http://localhost:5000` |
| AI Server | `neoRMS_AI_server` | Python + FastAPI | `http://localhost:8888` |
| Management Frontend | `neoRMS_Management` | React + Vite | `http://localhost:5173` |
| Customer Frontend | `neoRMS_frontend_customer` | React + Vite | `http://localhost:3000` |
| Waiter Frontend | `neo-RMS-Waiter` | React + Vite | `http://localhost:5173` |
| Chef Frontend | `neoRMS-Chef` | React + Vite | `http://localhost:5173` |

> Note: Multiple Vite services use port `5173` by default. Run one at a time on defaults, or pass `--port` to avoid collisions.

## Repository Strategy (Submodules)

Each service is maintained in its own repository and linked here as a Git submodule.

### Clone with submodules

```bash
git clone --recurse-submodules <root-repo-url>
cd neoRMS
```

### If already cloned without submodules

```bash
git submodule update --init --recursive
```

### Pull latest code for root + submodules

```bash
git pull
git submodule update --init --recursive --remote
```

## Prerequisites

- Git 2.30+
- Node.js 20+ (recommended)
- npm 9+ (npm 10 preferred)
- Python 3.10+ and `pip` (for AI server)
- Docker + Docker Compose (optional, for backend containerized runs)
- PostgreSQL (if running backend without Docker)

## Quick Start (Local Development)

### 1) Initialize submodules

```bash
git submodule update --init --recursive
```

### 2) Start backend (`neoRMS-backend`)

```bash
cd neoRMS-backend
npm install
npm run prisma:generate
npx prisma migrate dev
npm run dev
```

Backend health endpoint:

- `GET http://localhost:5000/health`

### 3) Start AI server (`neoRMS_AI_server`)

```bash
cd neoRMS_AI_server
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python run.py
```

AI server docs and health:

- `GET http://localhost:8888/docs`
- `GET http://localhost:8888/health`

### 4) Start one or more frontends

Open new terminals and run from each service directory:

```bash
# Management
cd neoRMS_Management && npm install && npm run dev

# Customer
cd neoRMS_frontend_customer && npm install && npm run dev

# Waiter
cd neo-RMS-Waiter && npm install && npm run dev

# Chef
cd neoRMS-Chef && npm install && npm run dev
```

## Environment Variables

Create a `.env` file in each submodule as needed.

### Backend (`neoRMS-backend/.env`)

Minimum common variables:

```dotenv
PORT=5000
NODE_ENV=development
APP_SECRET=change_me
DATABASE_URL=postgresql://<user>:<password>@localhost:5432/<db>
DB_URI=postgresql://<user>:<password>@localhost:5432/<db>
JWT_ACCESS_TOKEN_SECRET=change_me
JWT_ACCESS_TOKEN_EXPIRATION=1d
JWT_REFRESH_TOKEN_SECRET=change_me
JWT_REFRESH_TOKEN_EXPIRATION=7d
```

Additional integrations may be required for OAuth, AI, and payment:

- `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_CALLBACK_URL`
- `AI_SERVICE_URL`
- `SSL_STORE_ID`, `SSL_STORE_PASSWORD`, `SSL_IS_LIVE`, `SSL_SUCCESS_URL`, `SSL_FAIL_URL`, `SSL_CANCEL_URL`, `SSL_IPN_URL`

### AI Server (`neoRMS_AI_server/.env`)

```dotenv
HOST=0.0.0.0
PORT=8888
DEBUG=false
CORS_ORIGINS=["*"]
GEMINI_API_KEY=<your_key>
```

Tip: copy defaults first:

```bash
cp neoRMS_AI_server/.env.example neoRMS_AI_server/.env
```

### Management (`neoRMS_Management/.env`)

```dotenv
VITE_API_URL=http://localhost:5000/api
```

### Waiter (`neo-RMS-Waiter/.env`)

```dotenv
VITE_API_URL=http://localhost:5000/api
VITE_SOCKET_URL=http://localhost:5000/waiter
```

### Chef (`neoRMS-Chef/.env`)

```dotenv
VITE_API_URL=http://localhost:5000
VITE_SOCKET_URL=http://localhost:5000
```

### Customer (`neoRMS_frontend_customer/.env`)

```dotenv
VITE_API_BASE_URL=http://localhost:5000/api
```

## Service Commands

### Backend (`neoRMS-backend`)

- `npm run dev` — Run development server with hot reload.
- `npm run build` — Compile TypeScript to `dist/`.
- `npm run start` — Run compiled server.
- `npm run prisma:generate` — Generate Prisma client.
- `npm run db:seed` — Seed database.

### AI Server (`neoRMS_AI_server`)

- `python -m venv .venv` — Create virtual environment.
- `source .venv/bin/activate` — Activate virtual environment (Linux/macOS).
- `pip install -r requirements.txt` — Install Python dependencies.
- `python run.py` — Start FastAPI server on configured port (default `8888`).

### Frontends (all UI services)

- `npm run dev` — Start Vite development server.
- `npm run build` — Build production assets (available in waiter/chef/management).
- `npm run preview` — Preview production build (available in waiter/chef/management/customer).
- `npm run lint` — Run ESLint (available in waiter/chef/management/customer).

## Docker (Backend)

From `neoRMS-backend`:

```bash
# Development stack
docker compose -f compose.dev.yaml up --build

# Stop development stack
docker compose -f compose.dev.yaml down

# Production-style local stack
docker compose up --build
docker compose down
```

## Working with Submodules

### Check submodule status

```bash
git submodule status
```

### Update one submodule to latest tracked commit

```bash
git submodule update --remote neoRMS-backend
```

### Make changes inside a submodule

1. Enter submodule directory and commit/push there.
2. Return to root repository.
3. Commit the updated submodule pointer in the root repository.

## Common Issues

- Port conflicts on `5173`: run one frontend per default port or start with a custom port.
- Missing submodule content: run `git submodule update --init --recursive`.
- API connection issues: verify frontend `.env` values point to backend (`http://localhost:5000` / `/api`).
- Prisma/database errors: verify `DATABASE_URL`, database availability, and run migrations again.

## Additional Documentation

Detailed service-level docs are available in each submodule:

- `neoRMS-backend/README.md`
- `neoRMS_Management/README.md`
- `neoRMS_frontend_customer/README.md`
- `neo-RMS-Waiter/README.md`
- `neoRMS-Chef/README.md`
- `neoRMS_AI_server/README.md`

## Team Contributions

### Backend Team

| [naim1405](https://github.com/naim1405) | [mahfuz-saim](https://github.com/mahfuz-saim) | [Q3Alpha](https://github.com/Q3Alpha) |
|---|---|---|
|Project setup|Feature Flow Design|Order Managemetnt|
|Dockerize  |User management  | Table Reservation |
|Authentication  |Restaurant CRUD  | SQA & Testing |
|Schema Design  |Menu Items CRUD |  |
|Websocket  |Inventory Product Entry  |  |
|AI Integration|Analytics Endpoints||
|Code Review|Coupons CRUD||
||Payment Gateway Integration||

### Frontend Team

| [hasinsadaf](https://github.com/hasinsadaf) | [zanifazini0844](https://github.com/zanifazini0844) | [MiNi2136](https://github.com/MiNi2136) | [mahfuz-saim](https://github.com/mahfuz-saim)|[naim1405](https://github.com/naim1405)|
|---|---|---|---|---|
|Waiter -  DashBoard | Admin - Auth | Customer - Authentication | Payment Gateway Integration|Chef - WebSocket|
|Waiter - Order Placement and Status Contorl | Admin - Dashboard | Customer - Browse Restaurant  | Customer - Table Reservation|Waiter - WebSocket|
|Waiter - Profile Management   | Admin - Order Management | Customer - Browse Menu | Customer - Review integration|Waiter - Pagination|
|Chef -  Order Board For tracking orders | Admin - Menu & Inventory Management | Customer - Food Details |Customer - Order API Integration ||
|Chef - Order status control |Admin - Table Management|Customer - Cart|||
|Chef -Profile Management|Admin - Staff Management |Order UI|||


### AI Service

| [kefaet03](https://github.com/kefaet03) |
|---|
| Probabilistic Recommendation Engine |
|Review and Feedback Analyzer  |
|Review Sentiment Analysis |


