# LANDVERSE 3D — Complete Project Report
*Written for someone seeing this project for the first time. No prior knowledge assumed.*

---

## 1. What is this project, in one paragraph?

**LANDVERSE 3D is a website that shows buildings and land as an interactive 3D city.** You can rotate around the city, click any tower to see its floors and flats, upload a drone/satellite photo and let AI detect the building in it, generate official-style property ID numbers (called ULPINs), check whether a building's data looks correct, and even see the water pipes and cables buried underground. It is built for India, where the government wants every land parcel to have a unique ID.

---

## 2. Where does everything live?

| Place | Address / Path | What it is |
|---|---|---|
| This computer (project folder) | `C:\Users\AKSHITH\OneDrive\Desktop\TRIAL PROTOTYPE` | The main working copy — all code lives here |
| GitHub repo 1 (SIH submission) | `github.com/Akshithsan11/SIH_working_project_26011` | The repo judges will open |
| GitHub repo 2 (mirror) | `github.com/Akshithsan11/landverse-3D` | Backup copy, identical content |
| Live cloud website (works even if this laptop is off) | `https://web-production-0de0c.up.railway.app` | Hosted on Railway |
| Live cloud API (data service) | `https://api-production-4fb6.up.railway.app` | Same hosting; `/docs` shows all data endpoints |
| Backup public link (needs this laptop on) | `https://boil-earshot-wilt.ngrok-free.dev` | Tunnel to the home Docker setup |

---

## 3. Folder tour (what each folder is for)

Think of the project as two halves — a **backend** (the kitchen that prepares data) and a **frontend** (the dining room where visitors see it).

```
TRIAL PROTOTYPE/
├── backend/            → The kitchen: Python server (FastAPI) + AI + database logic
│   └── app/
│       ├── routes/     → 8 files, each one handles one topic of web requests
│       │                 (parcels, buildings, floors/units, ULPIN, AI analysis,
│       │                  validation, underground infra, dashboard stats)
│       └── services/   → 3 helper files: the AI detector, the ULPIN maker,
│                           and the validation rule-checker
├── frontend/           → The dining room: the React website visitors see
│   └── src/
│       ├── pages/      → 8 files, one per website page (Home, Dashboard, 3D Map…)
│       ├── components/ → Reusable pieces (menu bar, stat cards…)
│       │   └── 3d/     → The 3D city renderer (Three.js scene)
│       └── api/        → The file that talks to the backend over the internet
├── database/           → Ready-made table designs + starter data (for Postgres)
├── uploads/            → Photos users upload + AI-drawn result images (temporary)
```

**Helper files sitting in the main folder:**

| File | Plain-English meaning |
|---|---|
| `README.md` | The project's front page: what it is, how to run it, where it's deployed |
| `REPORT.md` | This file — the full beginner's report |
| `PRESENTATION.html` | A 13-slide presentation that opens in any browser (press F11, use arrow keys) |
| `DEMO_SCRIPT.txt` | Word-for-word 10-minute demo walkthrough: which link to open, what to click, what to say |
| `PITCH_ONE_PAGER.txt` | One printable page summarizing the project for judges |
| `docker-compose.yml` | Recipe that starts the whole app (database + server + website) with one command |
| `Dockerfile` (main folder) | Recipe for the single-container cloud version (website + server in one box) |
| `.dockerignore` | "Don't pack these into the cloud box" list (passwords file, local database…) |
| `.gitignore` | "Don't upload these to GitHub" list (passwords file, local database, temp files…) |
| `render.yaml` | Leftover recipe for an earlier hosting idea (Render) — not used anymore |
| `start-backend.bat` | Double-click to run just the server on this PC |
| `start-frontend.bat` | Double-click to run just the website on this PC |
| `start-public.bat` | Double-click to launch everything + a public tunnel link |

---

## 4. Every important file, explained simply

### Backend — the kitchen (`backend/`)

| File path | What it does (simply) |
|---|---|
| `backend/requirements.txt` | Shopping list of Python add-ons the server needs (FastAPI, OpenCV…) |
| `backend/Dockerfile` | Recipe to pack the server into a portable box (container) for any computer/cloud |
| `backend/.dockerignore` | Things excluded from that box |
| `backend/.env` | Private settings file (database location, secret key). Deliberately shared here for the hackathon; normally kept secret |
| `backend/.env.example` | Blank template of the settings file, safe to share |
| `backend/railway.toml` | Tells Railway cloud "this folder is a Docker project, build it" |
| `backend/app/main.py` | The server's front door: opens the API, allows the website to call it, creates tables + starter data on first run, serves uploaded photos |
| `backend/app/database.py` | Chooses the database: local file (SQLite) by default, or Postgres in the cloud; includes a fix so Railway-style addresses work |
| `backend/app/models.py` | Describes the 8 data tables (parcels, buildings, floors, units, ULPINs, underground assets, AI jobs, validation reports) |
| `backend/app/seed.py` | Plants the starter demo city (5 plots, 8 towers…) the first time the server runs |
| `backend/app/routes/parcels.py` | Handles land-plot requests: list, view, add, edit, delete |
| `backend/app/routes/buildings.py` | Handles tower requests, including the full plot→tower→floors→flats family tree the 3D view uses |
| `backend/app/routes/units.py` | Handles floor and flat (unit) requests |
| `backend/app/routes/ulpin.py` | Makes new ULPIN ID numbers and checks whether a given one is valid |
| `backend/app/routes/analysis.py` | Accepts photo uploads and runs the AI detector on them |
| `backend/app/routes/validation.py` | Runs the 6-rule quality check on a building and stores the score |
| `backend/app/routes/infrastructure.py` | Lists underground assets and warns about digging conflicts |
| `backend/app/routes/stats.py` | Computes the dashboard numbers (counts, averages, charts data) |
| `backend/app/services/ai_detector.py` | The actual AI: finds building outlines in photos using classic computer vision (grey → blur → edges → shapes → measurements) |
| `backend/app/services/ulpin.py` | Builds ULPIN codes like `IND-TG-HYD-01928-B01-F04-U01` and validates their format |
| `backend/app/services/validation.py` | The 6 rules (e.g. "each floor should be 2.8–4.2 m tall") that produce a 0–100 score |

### Frontend — the dining room (`frontend/`)

| File path | What it does (simply) |
|---|---|
| `frontend/package.json` | Shopping list of website add-ons + the `dev`/`build` commands |
| `frontend/package-lock.json` | Exact pinned versions of those add-ons (keeps builds identical everywhere; required by Docker) |
| `frontend/vite.config.ts` | Settings for the build tool (Vite): port 5173, image routing during development |
| `frontend/vercel.json` | Leftover settings for an earlier hosting idea (Vercel) — not used anymore |
| `frontend/nginx.conf` | Settings for the web-server program (nginx) inside the website's container: serves pages, forwards data requests to the API, injects the API address at startup |
| `frontend/Dockerfile` | Recipe: builds the website, then serves it with nginx |
| `frontend/.dockerignore` | Things excluded from the website's box |
| `frontend/railway.toml` | Tells Railway cloud "build this folder with Docker" |
| `frontend/index.html` | The single HTML shell every page loads into |
| `frontend/src/main.tsx` | The website's ignition key — starts React |
| `frontend/src/App.tsx` | The road map: which web address shows which page |
| `frontend/src/config.ts` | Decides where data comes from: cloud address if provided, same website if proxied, local server otherwise |
| `frontend/src/types.ts` | Dictionary of data shapes (what a "Building" or "Parcel" looks like in code) |
| `frontend/src/index.css` | All visual styling (dark cyber theme, glowing buttons…) |
| `frontend/src/api/client.ts` | The telephone line to the server: 20+ named functions like "get buildings" or "generate ULPIN" |
| `frontend/src/components/Layout.tsx` | The top menu bar + page frame seen on every page |
| `frontend/src/components/StatCard.tsx` | The little number cards on the dashboard ("8 Buildings"…) |
| `frontend/src/components/3d/CityScene.tsx` | **The heart of the visuals**: draws every tower from real height/floor/outline data, colors them by type, handles clicks, exploding floor view, and underground pipes |
| `frontend/src/pages/Landing.tsx` | Home page: title, feature cards, quick-start |
| `frontend/src/pages/Dashboard.tsx` | Numbers page: stat cards, bar chart (types), pie chart (validation), recent AI jobs |
| `frontend/src/pages/Map3D.tsx` | The 3D city page: the 3D scene + building list + click-to-inspect family tree + explode slider + underground toggle |
| `frontend/src/pages/Parcels.tsx` | Land-plot table + "add new plot" form + plot inspector |
| `frontend/src/pages/Analysis.tsx` | Upload a photo → run AI → see detected type/height/floors + annotated image |
| `frontend/src/pages/ULPIN.tsx` | Fill a form → get a ULPIN code; paste any code → check if valid; see the registry |
| `frontend/src/pages/Validation.tsx` | Pick a building → run the 6-rule check → see score, failed rules, history |
| `frontend/src/pages/Infrastructure.tsx` | Underground assets list + "will my foundation hit a pipe?" conflict checker |

### Database designs (`database/`)

| File path | What it does (simply) |
|---|---|
| `database/schema.sql` | Blueprint for creating all 8 tables in Postgres (columns, links between tables) |
| `database/seed_data.sql` | Starter rows (the Hyderabad demo plots and towers) for a manual Postgres setup |

---

## 5. The datasets (what data lives inside)

There are no downloaded datasets — the demo city is **hand-built starter data** that loads itself automatically the first time the server runs (`backend/app/seed.py`). It represents a fictional neighborhood in **Hyderabad, Telangana**:

| Dataset (table) | Contents | Example row |
|---|---|---|
| Parcels (land plots) | 5 plots with ID, location, GPS coordinates, area | `PRC-01928, Hyderabad, 17.3850°N 78.4867°E, 2500.5 sq.m` |
| Buildings (towers) | 8 towers with code, height, floors, type, AI score | `BLD-2041, 42.6 m, 12 floors, Residential – High Rise, 94.2%` |
| Floors | 12 floors for the main tower (+ sample floors for others) | `Building 1, Floor 4, 3.5 m tall, 4 units` |
| Property units (flats) | 4 flats on floor 4 | `Flat 403, 1350.75 sq.ft, Residential, Rented` |
| ULPIN records | 1 registered property ID | `IND-TG-HYD-01928-B01-F04-U01` (country-state-city-plot-tower-floor-flat) |
| Infrastructure (underground) | 5 buried assets with depth + owner | `INF-0293, Water Pipeline, 8.4 m deep, Municipal Authority` |
| AI jobs + validation reports | Created live whenever anyone uses those pages | e.g. "photo X → Mixed Use, 98.5%, 18 floors" |

Everything else (uploads, analysis results) is created by users during use and stored temporarily.

---

## 6. Developer tools & technologies (what each one is, simply)

**Languages**
- **Python** — the language the server/AI is written in.
- **TypeScript** — a safer version of JavaScript; the whole website is written in it.
- **SQL** — the language databases understand (used in the two `.sql` blueprint files).

**Backend tools**
- **FastAPI** — a Python toolkit for building the data service (the API); also auto-creates the interactive docs page at `/docs`.
- **Uvicorn** — the engine that actually runs the FastAPI server and talks to the internet.
- **SQLAlchemy** — a translator so Python code can work with both SQLite and Postgres without changes.
- **Pydantic** — checks that incoming data has the right shape (part of FastAPI).
- **OpenCV + NumPy + Pillow** — the AI toolkit: reads photos as number grids, finds edges and shapes (building outlines), measures them.
- **psycopg2** — the driver that lets Python talk to Postgres databases.
- **python-dotenv / python-multipart** — read the settings file / handle photo uploads.

**Frontend tools**
- **React** — toolkit for building interactive website pages out of reusable pieces.
- **Vite** — builds the website files for production and gives a fast preview while developing.
- **Three.js + React Three Fiber + Drei** — draws real 3D graphics in the browser (the city, towers, pipes, orbit controls).
- **Tailwind CSS** — ready-made styling pieces for the dark cyber look.
- **Recharts** — draws the dashboard bar/pie charts.
- **Framer Motion + Lucide** — smooth animations + icons.
- **Axios** — the website's telephone line to the server.
- **React Router** — shows different pages for different web addresses (`/map`, `/ulpin`…).
- **nginx** — a professional web-server program that delivers the website files inside the container and forwards data requests to the API.

**Databases**
- **SQLite** — a database that is just a single file on disk; zero setup, used on this PC and in single-container cloud mode.
- **PostgreSQL 15** — a full professional database; used inside Docker at home and on Railway cloud.

**Containers & hosting (where the app runs)**
- **Docker + Docker Desktop** — packs each part of the app into portable sealed boxes (containers) that run identically anywhere; Desktop runs them on this PC with WSL2/Ubuntu underneath.
- **Docker Compose** (`docker-compose.yml`) — starts all three boxes (database + server + website) with one command.
- **Railway** — the cloud company hosting the always-on public site + database (free trial). Project `landverse-3d` holds 3 services: `db`, `api`, `web`.
- **ngrok** — a tunnel that temporarily exposes this PC to the internet (the backup public link; needs this PC on).
- **Cloudflare tunnel** — an earlier temporary-link method, replaced by ngrok.
- **Vercel / Render / Hugging Face** — hosting options that were tried and set aside (paid or paywalled); their leftover config files were removed, except `frontend/vercel.json` history in git.

**Code & project tools**
- **Git + GitHub** — version control (every change saved as a commit) and online backup of the code (two repos).
- **Node.js + npm** — runs JavaScript tools and installs website add-ons.
- **Railway CLI** — command-line remote control used to create the cloud project, services, variables, and deploys without clicking.
- **cloudflared** — the Cloudflare tunnel program (in temp files, no longer used).
- **Swagger UI** — the auto-generated `/docs` page where you can try every data endpoint in a browser.
- **PowerShell** — the Windows terminal where all commands were run.

---

## 7. How data flows (the 10-second version)

```
Visitor's browser  →  Website (React)  →  "I need buildings" (Axios)
       →  Server (FastAPI)  →  Database (Postgres/SQLite)  →  rows come back
       →  Website draws towers in 3D (Three.js)

Drone photo  →  Upload page  →  AI service (OpenCV finds outline, measures it)
       →  saved as a job  →  optionally becomes a new 3D tower + validated + ULPIN'd
```

---

## 8. How to run it (three ways)

**On this PC with Docker (recommended):** `docker compose up --build` → site at `http://localhost:5173`, data at `http://localhost:8000/docs`.

**On this PC without Docker:** double-click `start-backend.bat`, then `start-frontend.bat`.

**Make it public from this PC:** double-click `start-public.bat` (needs ngrok logged in) → share the printed link.

**In the cloud (already live):** nothing to do — Railway runs it at the links in section 2. Redeploy from this PC with `railway up ./backend --service api` / `railway up ./frontend --service web`.

---

## 9. Glossary (words judges may ask about)

- **API** — a menu of data services the website orders from (e.g. "give me all buildings").
- **ULPIN** — Unique Land Parcel Identification Number: India's planned ID for every property, extended here down to flat level.
- **3D twin** — a living digital copy of real-world property you can explore on screen.
- **Container (Docker)** — a sealed box holding a program plus everything it needs, runnable on any machine.
- **Seed data** — starter demo content loaded automatically so the app never opens empty.
- **CORS** — a browser safety rule; the server explicitly permits our website addresses.
- **SPA fallback** — trick so refreshing any page (like `/map`) still loads the app instead of an error.
- **Healthcheck** — the system asking each container "are you alive?" and restarting/reordering as needed.
