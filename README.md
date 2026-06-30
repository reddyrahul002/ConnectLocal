# ConnectLocal

> Helping elderly Australians discover free social communities near them.

ConnectLocal is an age friendly social discovery web platform for older Australians aged 65 to 85 living alone in metropolitan Melbourne. It brings together social events, journey planning, suburb liveability information, and an AI chatbot assistant into a single accessible website.

Built as part of FIT5120 Industry Experience Studio at Monash University, Semester 1, 2026.

---

## Table of Contents

- [Live Links](#live-links)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)
- [System Architecture](#system-architecture)
- [Database](#database)
- [External APIs](#external-apis)
- [CI/CD with GitHub Actions](#cicd-with-github-actions)
- [Testing](#testing)
- [Deployment](#deployment)
- [Documentation](#documentation)

---

## Live Links

| Field | Value |
| :--- | :--- |
| Live Site | https://connectlocal-zeta.vercel.app/login |
| Login ID | `connectlocal` |
| Password | `community2024` |
| Backend API | https://connectlocal.duckdns.org |
| API Docs (Swagger) | https://connectlocal.duckdns.org/docs |
| Source Repository | https://github.com/rahulyer002/ConnectLocal |
| Iteration 1 | https://connect-local-v1.vercel.app |
| Iteration 2 | https://connect-local-v2.vercel.app |
| Iteration 3 | https://connect-local-v3.vercel.app |
| LeanKit Board | https://monashie.leankit.com/board/2426784368 |
| E-portfolio | https://bit.ly/4mDOfhz |

---

## Features

| Feature | Description |
| :--- | :--- |
| **Wellbeing Check** | Private self assessment of social connectedness based on a validated loneliness scale. |
| **Discover Events** | Search free or low cost events and community activities near a chosen suburb. |
| **Journey Support** | Plan a step by step trip from home to an event with a recommended departure time. |
| **Best Time** | Live comfort conditions, quietest times to go out, and welcoming nearby green spaces. |
| **AI Chatbot** | Text to functionality assistant that converts natural language into in app actions. |
| **Suburb Explorer Map** | Interactive Leaflet map of Melbourne suburbs scored on age friendliness. |

---

## Tech Stack

| Layer | Stack |
| :--- | :--- |
| **Frontend** | Vue 3, Vite 8, Vue Router 5, Leaflet 1.9, Vitest 4. Hosted on Vercel. |
| **Backend** | FastAPI 4.0, Python 3.11, SQLAlchemy 2.0, Uvicorn, httpx, pydantic-settings. Containerised with Docker. |
| **Database** | PostgreSQL 16 on AWS RDS with PostGIS for spatial queries. |
| **Infrastructure** | AWS EC2 (Ubuntu 24.04), AWS RDS, AWS CloudWatch, AWS Secrets Manager, Vercel, Nginx, Let's Encrypt via Certbot. |
| **DevOps** | GitHub, GitHub Actions, Dependabot. |
| **External Services** | Eventfinda, Ticketmaster, OpenStreetMap, PTV GTFS, Google Maps, Open-Meteo, City of Melbourne Pedestrian and Microclimate, Google Gemini, Groq. |

---

## Getting Started

### Prerequisites

| Tool | Version |
| :--- | :--- |
| Node.js | 22 LTS (or Vite 8 compatible) |
| Python | 3.11 |
| Docker | Latest (recommended for backend) |
| Git | Latest |

### Clone the Repository

```bash
git clone https://github.com/rahulyer002/ConnectLocal.git
cd ConnectLocal
```

### Frontend

```bash
cd FE/vue-app
npm install
# Create .env file with the variables in the Environment Variables section
npm run dev
```

The frontend runs at `http://localhost:5173`.

### Backend

```bash
cd BE
python3.11 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pip install -r requirements-test.txt
# Create .env file with the variables in the Environment Variables section
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The backend runs at `http://localhost:8000`. Interactive Swagger documentation is at `http://localhost:8000/docs`.

### Backend with Docker (Recommended)

```bash
cd BE
docker-compose up -d --build
```

To stop: `docker-compose down`. To view logs: `docker-compose logs -f api`.

---

## Environment Variables

Both `.env` files must be created locally. Real secrets must never be committed to the repository.

### Frontend `.env`

Located at `FE/vue-app/.env`. All variables must use the `VITE_` prefix.

```env
VITE_API_BASE_URL=https://connectlocal.duckdns.org
VITE_ACTIVITIES_API_URL=https://connectlocal.duckdns.org
VITE_RESONANCE_API_URL=https://connectlocal.duckdns.org
VITE_GOOGLE_MAPS_API_KEY=<your_google_maps_api_key>
VITE_GOOGLE_MAPS_MAP_ID=<your_google_maps_map_id>
VITE_PROXY_TARGET=https://connectlocal.duckdns.org
```

### Backend `.env`

Located at `BE/.env`.

```env
DATABASE_URL=postgresql+psycopg2://<user>:<password>@<host>:5432/<db>?sslmode=require
DATABASE_URL_SYNC=postgresql+psycopg2://<user>:<password>@<host>:5432/<db>?sslmode=require
EVENTFINDA_USERNAME=<eventfinda_username>
EVENTFINDA_PASSWORD=<eventfinda_password>
TICKETMASTER_API_KEY=<ticketmaster_api_key>
GOOGLE_MAPS_API_KEY=<google_maps_api_key>
OPENWEATHER_API_KEY=<openweather_api_key>
GEMINI_API_KEY=<google_gemini_api_key>
GROQ_API_KEY=<groq_api_key>
ENVIRONMENT=development
```

In production, backend secrets are stored in AWS Secrets Manager and loaded onto EC2 at deploy time.

---

## Project Structure

```
ConnectLocal/
├── BE/                          Backend FastAPI application
│   ├── app/
│   │   ├── routers/             API routers
│   │   ├── services/            Service modules
│   │   ├── models/              SQLAlchemy models
│   │   ├── schemas/             Pydantic schemas
│   │   └── utils/               Shared utilities
│   ├── tests/                   pytest test suite
│   ├── prompts/                 AI chatbot system prompts
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── requirements.txt
│   └── requirements-test.txt
├── FE/
│   └── vue-app/                 Vue 3 frontend
│       ├── src/
│       │   ├── pages/           Top level pages
│       │   ├── components/      Reusable UI components
│       │   ├── router/          Vue Router config
│       │   ├── composables/     API helpers
│       │   ├── stores/          Reactive state stores
│       │   ├── layouts/         Shared layouts
│       │   └── assets/          Static assets
│       ├── public/              Includes vic-suburbs.geojson
│       ├── tests/               Vitest specs
│       ├── package.json
│       ├── vite.config.js
│       └── vercel.json
├── Data/                        Data loading scripts
├── docs/                        Documentation and diagrams
└── .github/
    ├── dependabot.yml
    └── workflows/
```

---

## System Architecture

<img width="1667" height="922" alt="image" src="https://github.com/user-attachments/assets/690662e5-6f65-4bfa-846f-07ec1dfce356" />

| Layer | Purpose |
| :--- | :--- |
| **Client Layer** | User browser running the Vue 3 single page application. |
| **API Gateway Layer** | Nginx reverse proxy on EC2 with SSL via Let's Encrypt. |
| **Backend Layer** | FastAPI application with eight service modules: event aggregation, recommendation, location enrichment, journey builder, resonance engine, companion service, suburb aggregation, weather fallback. |
| **Database Layer** | PostgreSQL 16 on AWS RDS holding open datasets. No personal data. |
| **External APIs Layer** | Nine third party services. See the [External APIs](#external-apis) section. |
| **Infrastructure Layer** | AWS EC2, RDS, CloudWatch, Secrets Manager, Vercel. |
| **DevOps Layer** | GitHub, GitHub Actions, environment variable management. |

---

## Database

PostgreSQL 16 on AWS RDS in the Melbourne region. Open datasets only. No personal data.

| Table | Purpose |
| :--- | :--- |
| `venues` | Locations where events take place. |
| `events` | Event records from Eventfinda and Ticketmaster. |
| `user_prefs` | Anonymous UI preferences (text size, theme). |
| `saved_activities` | Bookmarked events. |
| `pedestrian_patterns` | Aggregated CoM pedestrian count history. |
| `micro_sensors` | CoM microclimate sensor inventory. |
| `suburb_boundaries` | ABS SA2 polygon boundaries with PostGIS geometry. |
| `suburb_sensor_link` | Many to many link between suburbs and sensors. |

---

## External APIs

| Service | Used For |
| :--- | :--- |
| Eventfinda | Primary event source. |
| Ticketmaster Discovery | Secondary event source. |
| OpenStreetMap and Nominatim | Geocoding and accessibility POIs. |
| PTV GTFS | Public transport timetable. |
| OSRM | Walking route geometry. |
| Google Maps Directions | Multi modal journey planning. |
| Open-Meteo | Weather data for general areas. |
| City of Melbourne Microclimate | Live CBD microclimate readings. |
| City of Melbourne Pedestrian | Live pedestrian counts. |
| Google Gemini | AI Chatbot primary language model. |
| Groq | AI Chatbot fallback language model. |

---

## CI/CD with GitHub Actions

Six automated workflows run on every change.

| Workflow | Trigger | Purpose |
| :--- | :--- | :--- |
| **Tests** | Push and PR to `development` | Runs pytest backend and Vitest frontend suites in parallel. Blocks merges on failure. |
| **CodeQL** | Push and PR, plus weekly | Static security analysis for Python and JavaScript. |
| **Secret Scan (Gitleaks)** | Push and PR, plus weekly cron | Scans diffs and full git history for committed API keys and credentials. |
| **Dependabot** | Weekly cron, plus immediate CVE runs | Automated PRs for outdated and vulnerable dependencies. |
| **Docker Build (Backend)** | PR touching `BE/**` | Builds backend image in a sandbox and verifies the `/health` endpoint. |
| **Deploy Backend to EC2** | Push to `development` touching `BE/**`, plus manual | SSHes into EC2, pulls, rebuilds, and verifies the live `/health` endpoint. |

### Required GitHub Secrets

Configure under **Settings → Secrets and variables → Actions**:

| Secret | Purpose |
| :--- | :--- |
| `EC2_HOST` | Public IP or DNS of the EC2 backend host. |
| `EC2_USER` | SSH user on EC2 (`ubuntu` for Ubuntu AMIs). |
| `EC2_SSH_KEY` | Full contents of the `.pem` private key including BEGIN and END lines. |

The frontend deploys automatically through Vercel's GitHub integration on every push to `development`. No GitHub Actions workflow is required for the frontend.

---

## Testing

### Frontend

Run from `FE/vue-app/`:

```bash
npm test              # run once
npm run test:watch    # watch mode
```

Vitest 4 with Vue Test Utils 2.4 and jsdom 29. Covers page routes, components, and composable API helpers.

### Backend

Run from `BE/`:

```bash
pytest -v
```

pytest with respx for HTTP mocking. Covers all routers, service modules, parsers, and the `SafeJSONResponse` class. External services are stubbed via fixtures so the suite is deterministic and offline.

---

## Deployment

### Frontend (Vercel)

Deployed automatically via Vercel's GitHub integration. Every push to the `development` branch triggers a rebuild and deploy. Environment variables are configured in the Vercel dashboard under **Settings → Environment Variables**.

### Backend (AWS EC2)

Deployed automatically via the `deploy-backend.yml` GitHub Actions workflow on every push to `development` touching `BE/**`. The workflow SSHes into the EC2 instance, pulls the latest code, rebuilds the Docker container, and verifies the live `/health` endpoint.

For a manual deploy, SSH into the EC2 instance and run:

```bash
cd ~/ConnectLocal
git pull origin development
cd BE
docker-compose down
docker-compose up -d --build
curl https://connectlocal.duckdns.org/health
```

---

## Documentation

Companion documents available in the project handover:

| Document | Purpose |
| :--- | :--- |
| Product Document | Value of ConnectLocal to a sponsor. |
| Support Document | How to run, operate, and support the product after handover. |
| Maintenance Document | Technical setup, codebase structure, deployment, troubleshooting. |
| Code Quality Document | Practices and tooling that keep the codebase healthy. |
| Build Documentation | What was built, by which technology, in which iteration. |
| Pair Programming Record | Records of pair programming sessions across iterations. |
| Security Plan | Security approach, vulnerabilities, and mitigations. |
| Data Management Plan | What data is collected, where it lives, and how it is protected. |

Live API documentation is generated automatically by FastAPI at https://connectlocal.duckdns.org/docs.
