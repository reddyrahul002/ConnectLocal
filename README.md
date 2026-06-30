# ConnectLocal

> Helping elderly Australians discover free social communities near them.

ConnectLocal is an age friendly social discovery web platform built for older Australians aged 65 to 85 living alone in metropolitan Melbourne. It brings together social events, journey planning, suburb liveability information, and an AI chatbot assistant into a single, accessible website.

Built as part of **FIT5120 Industry Experience Studio** at **Monash University** by **Team TE18 (Ctrl Alt Del Loneliness)**, Semester 1, 2026.

---

## Table of Contents

- [About the Project](#about-the-project)
- [Live Links and Credentials](#live-links-and-credentials)
- [Software Features](#software-features)
- [System Architecture](#system-architecture)
- [Tech Stack by Layer](#tech-stack-by-layer)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Database](#database)
- [External APIs](#external-apis)
- [CI/CD with GitHub Actions](#cicd-with-github-actions)
- [Testing](#testing)
- [Deployment](#deployment)
- [Documentation](#documentation)
- [Team](#team)

---

## About the Project

ConnectLocal is a location aware web platform that helps older Australians discover nearby free or low cost social activities and community events, and feel confident about travelling to them. It was built in response to a clear social problem: loneliness and social isolation are common among older adults, and the social opportunities near them are often scattered across many websites and hard to navigate.

The product is designed around a representative user, **Emily**, a 72 year old retiree who lives alone and wants low pressure ways to stay socially connected. The wider audience includes people with stricter mobility needs, such as those who use a walking frame or a wheelchair, and the support workers, family members, and community staff who help them.

Six features are delivered across three iterations:

| Iteration | Epics Delivered |
| :--- | :--- |
| Iteration 1 | Wellbeing Check, Discover Events |
| Iteration 2 | Journey Support, Best Time and Resonance Engine |
| Iteration 3 | AI Chatbot, Suburb Explorer Map |

---

## Live Links and Credentials

| Field | Value |
| :--- | :--- |
| Live Site | https://connectlocal-zeta.vercel.app/login |
| Login ID | `connectlocal` |
| Password | `community2024` |
| Iteration 1 | https://connect-local-v1.vercel.app |
| Iteration 2 | https://connect-local-v2.vercel.app |
| Iteration 3 | https://connect-local-v3.vercel.app |
| Backend API | https://connectlocal.duckdns.org |
| Backend API Docs (Swagger) | https://connectlocal.duckdns.org/docs |
| LeanKit Board | https://monashie.leankit.com/board/2426784368 |
| E-portfolio | https://bit.ly/4mDOfhz |
| Source Repository | https://github.com/rahulyer002/ConnectLocal |

---

## Software Features

| Feature | Description |
| :--- | :--- |
| **Wellbeing Check** | A short, private self assessment of how socially connected the user feels, based on a validated loneliness scale, with a clear plain language result. |
| **Discover Events** | Search for free or low cost events and community activities near a chosen suburb, with simple filters for category, date, and price. |
| **Journey Support** | Plan a comfortable, step by step trip from home to an event or community place, with a recommended departure time and accessibility aware route segments. |
| **Best Time and Resonance Engine** | See live comfort conditions (weather, crowd levels), the quietest times to go out, and welcoming nearby green spaces. |
| **AI Chatbot** | A text to functionality assistant available on every page. The user types natural language, and the chatbot converts it into in app actions such as navigation, suburb opening, journey planning, or event search. |
| **Suburb Explorer Map** | An interactive Leaflet map of Melbourne suburbs showing age friendliness scores based on demographics, accessibility, transport, green space, climate, and live pedestrian activity. |

---

## System Architecture

The diagram below shows the full system architecture as of Iteration 3.

<img width="1727" height="1160" alt="image" src="https://github.com/user-attachments/assets/e4f526f9-2201-4b47-96fc-ba5ecf1f7422" />


### Architecture Layers

| Layer | Components | Purpose |
| :--- | :--- | :--- |
| **Client Layer** | User browser running the Vue 3 single page application | Renders the user interface and handles interaction. |
| **API Gateway Layer** | Nginx reverse proxy on EC2 with SSL via Let's Encrypt | Terminates HTTPS, forwards requests to the FastAPI backend. |
| **Backend Layer** | FastAPI application with eight service modules | Provides the REST API. Handles event aggregation, recommendations, location enrichment, journey building, resonance scoring, chatbot orchestration, suburb aggregation, and weather fallback. |
| **Database Layer** | PostgreSQL 16 on AWS RDS | Stores open datasets (venues, events, user preferences, saved activities, pedestrian patterns, microclimate sensors, suburb boundaries, suburb sensor links). No personal data. |
| **External APIs Layer** | Nine third party services | Event sources (Eventfinda, Ticketmaster), maps and routing (OpenStreetMap, Google Maps), transit (PTV GTFS), weather (Open-Meteo, CoM Microclimate), live pedestrian counts (CoM Pedestrian), AI models (Google Gemini, Groq). |
| **Infrastructure Layer** | AWS EC2, AWS RDS, AWS CloudWatch, AWS Secrets Manager, Vercel | Hosting, monitoring, secret storage. |
| **DevOps Layer** | GitHub, GitHub Actions, environment variable management | Source control, automated testing, security scanning, and deployment. |

---

## Tech Stack by Layer

### Frontend

| Component | Technology | Role |
| :--- | :--- | :--- |
| Framework | Vue 3 (Composition API) | Reactive user interface and component model. |
| Build tool | Vite 8 | Build pipeline and local development server with hot module replacement. |
| Routing | Vue Router 5 | Client side routing for the single page application. |
| Maps | Leaflet 1.9 | Renders the Melbourne suburb choropleth map and welcoming spaces map. |
| State | Plain reactive stores | Lightweight stores for chatbot, resonance, UI, and wellbeing state. Pinia is not used. |
| Testing | Vitest 4, Vue Test Utils 2.4, jsdom 29 | Unit and component testing. |
| Hosting | Vercel | Auto deployed from the `development` branch. |

### Backend

| Component | Technology | Role |
| :--- | :--- | :--- |
| Framework | FastAPI 4.0 (Python 3.11) | REST API server with automatic Swagger documentation. |
| ORM | SQLAlchemy 2.0 | Database tables and parameterised queries. |
| Database driver | psycopg2 | Connects Python to PostgreSQL over SSL. |
| HTTP server | Uvicorn | Runs the FastAPI application. |
| HTTP client | httpx | Outbound calls to external services. |
| Config | pydantic-settings | Loads and validates environment variables. |
| JSON handling | Custom `SafeJSONResponse` | Converts NaN and Inf floats to null during serialisation, important for sensor data. |
| Containerisation | Docker, docker-compose | Packages the backend so it runs the same everywhere. |
| Testing | pytest, respx | Backend unit and integration tests with mocked external services. |

### Database

| Component | Technology | Role |
| :--- | :--- | :--- |
| Engine | PostgreSQL 16 | Relational database. |
| Hosting | AWS RDS (Melbourne region) | Managed database service with automated backups. |
| Extensions | PostGIS | Spatial geometry support for suburb boundary polygons. |
| Tables | venues, events, user preferences, saved activities, pedestrian patterns, micro sensors, suburb boundaries, suburb sensor links | See the Maintenance Document for the full schema. |

### Infrastructure and DevOps

| Component | Technology | Role |
| :--- | :--- | :--- |
| Backend hosting | AWS EC2 (Ubuntu 24.04) | Virtual server running the backend Docker container. |
| Database hosting | AWS RDS | PostgreSQL 16. |
| Secrets | AWS Secrets Manager and EC2 `.env` | Stores backend API keys and database credentials. |
| Monitoring | AWS CloudWatch | Backend logs and basic metrics. |
| Reverse proxy | Nginx | Terminates HTTPS and forwards to the backend. |
| SSL | Let's Encrypt via Certbot | HTTPS certificate, renewed automatically. |
| Frontend hosting | Vercel | Static frontend with CDN. |
| Version control | Git, GitHub | Repository at `github.com/rahulyer002/ConnectLocal`. |
| CI/CD | GitHub Actions | Six workflows covering tests, security scanning, dependency updates, build verification, and deployment. |

### External Services

See the [External APIs](#external-apis) section for the full list of nine integrated services.

---

## Project Structure

```
ConnectLocal/
├── BE/                          Backend FastAPI application
│   ├── app/
│   │   ├── routers/             Seventeen API routers (events, journey, chatbot, suburbs, etc.)
│   │   ├── services/            Service modules (companion, suburb_aggregation, weather_fallback, etc.)
│   │   ├── models/              SQLAlchemy database models
│   │   ├── schemas/             Pydantic request and response schemas
│   │   └── utils/               Shared utilities including SafeJSONResponse
│   ├── tests/                   pytest test suite
│   ├── prompts/                 System prompts for the AI chatbot
│   ├── Dockerfile               Backend container definition
│   ├── docker-compose.yml       Local and EC2 orchestration
│   ├── requirements.txt         Production Python dependencies
│   ├── requirements-test.txt    Test dependencies
│   ├── pytest.ini               Test configuration
│   └── conftest.py              Test fixtures with mocked env vars
├── FE/
│   └── vue-app/                 Vue 3 frontend application
│       ├── src/
│       │   ├── pages/           Top level pages
│       │   ├── components/      Reusable UI components
│       │   ├── router/          Vue Router configuration
│       │   ├── composables/     Reusable logic and API helpers
│       │   ├── stores/          Plain reactive state stores
│       │   ├── layouts/         Shared page layouts
│       │   └── assets/          Static assets
│       ├── public/              Public files including vic-suburbs.geojson
│       ├── tests/               Vitest specs
│       ├── package.json         Frontend dependencies and scripts
│       ├── vite.config.js       Vite build and dev server config
│       └── vercel.json          Vercel SPA rewrite configuration
├── Data/                        Data loading scripts and seed data
├── docs/                        Documentation and architecture diagrams
└── .github/
    ├── dependabot.yml           Dependency update configuration
    └── workflows/               GitHub Actions workflows
```

---

## Getting Started

### Prerequisites

| Tool | Version | Purpose |
| :--- | :--- | :--- |
| Node.js | 22 LTS (Vite 8 requires 20.19 or higher) | Frontend build and dev server. |
| Python | 3.11 | Backend runtime. |
| Docker | Latest | Optional. Recommended for the backend. |
| PostgreSQL | 16 (or use the hosted RDS instance) | Database. |
| Git | Latest | Version control. |

### Clone the Repository

```bash
git clone https://github.com/rahulyer002/ConnectLocal.git
cd ConnectLocal
```

### Frontend Setup

```bash
cd FE/vue-app
npm install
```

Create a `.env` file in `FE/vue-app/`. See the [Environment Variables](#environment-variables) section for the required keys.

Start the development server:

```bash
npm run dev
```

The frontend runs at `http://localhost:5173`. Requests to `/api` are proxied to the backend defined by `VITE_PROXY_TARGET`, so cross origin errors are avoided during development.

Run frontend tests:

```bash
npm test
```

Build for production:

```bash
npm run build
```

This runs the test suite and then creates a production build in the `dist/` folder.

Preview the production build:

```bash
npm run preview
```

### Backend Setup

```bash
cd BE
python3.11 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pip install -r requirements-test.txt
```

Create a `.env` file in `BE/`. See the [Environment Variables](#environment-variables) section for the required keys.

Run the development server:

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The backend runs at `http://localhost:8000`. Interactive Swagger documentation is available at `http://localhost:8000/docs`.

Run backend tests:

```bash
pytest -v
```

### Run the Backend with Docker

The recommended setup is Docker, which matches the production EC2 environment.

```bash
cd BE
docker-compose up -d --build
```

To stop:

```bash
docker-compose down
```

To view logs:

```bash
docker-compose logs -f api
```

---

## Environment Variables

Both `.env` files must be created locally. **Real secrets must never be committed to the repository.** Both `.env` files are listed in `.gitignore`.

### Frontend Environment Variables

Located at `FE/vue-app/.env`. All variables must use the `VITE_` prefix so Vite exposes them to the application.

```env
VITE_API_BASE_URL=https://connectlocal.duckdns.org
VITE_ACTIVITIES_API_URL=https://connectlocal.duckdns.org
VITE_RESONANCE_API_URL=https://connectlocal.duckdns.org
VITE_GOOGLE_MAPS_API_KEY=<your_google_maps_api_key>
VITE_GOOGLE_MAPS_MAP_ID=<your_google_maps_map_id>
VITE_PROXY_TARGET=https://connectlocal.duckdns.org
```

For local development against a locally running backend, set `VITE_API_BASE_URL` and `VITE_PROXY_TARGET` to `http://localhost:8000` instead.

### Backend Environment Variables

Located at `BE/.env`.

```env
# Database
DATABASE_URL=postgresql+psycopg2://<user>:<password>@<host>:5432/<db>?sslmode=require
DATABASE_URL_SYNC=postgresql+psycopg2://<user>:<password>@<host>:5432/<db>?sslmode=require

# Event sources
EVENTFINDA_USERNAME=<eventfinda_username>
EVENTFINDA_PASSWORD=<eventfinda_password>
TICKETMASTER_API_KEY=<ticketmaster_api_key>

# Maps and routing
GOOGLE_MAPS_API_KEY=<google_maps_api_key>

# Weather
OPENWEATHER_API_KEY=<openweather_api_key>

# AI Chatbot
GEMINI_API_KEY=<google_gemini_api_key>
GROQ_API_KEY=<groq_api_key>

# Environment flag
ENVIRONMENT=development
```

In production, all backend secrets are stored in AWS Secrets Manager and loaded onto EC2 at deploy time.

---

## Database

The database is PostgreSQL 16 hosted on AWS RDS in the Melbourne region. It holds open datasets only and stores no personal information.

Main tables:

| Table | Purpose |
| :--- | :--- |
| `venues` | Locations where events take place. |
| `events` | Event records from Eventfinda and Ticketmaster. |
| `user_prefs` | Anonymous user interface preferences (text size, theme). |
| `saved_activities` | Bookmarked events. |
| `pedestrian_patterns` | Aggregated CoM pedestrian count history. |
| `micro_sensors` | CoM microclimate sensor inventory. |
| `suburb_boundaries` | ABS SA2 polygon boundaries with PostGIS geometry. |
| `suburb_sensor_link` | Many to many link between suburbs and sensors. |

The full schema is documented in the Maintenance Document, Section 4.

---

## External APIs

ConnectLocal integrates with nine external services. All are wrapped behind service layer adapters so providers can be swapped without changing the routers.

| Service | Used For | Tier |
| :--- | :--- | :--- |
| **Eventfinda** | Primary event source | Free developer account |
| **Ticketmaster Discovery** | Secondary event source | Free developer account |
| **OpenStreetMap / Nominatim** | Geocoding and accessibility POIs | Free, public |
| **PTV GTFS** | Public transport timetable data | Free, open data |
| **OSRM** | Walking route geometry | Free, self hosted compatible |
| **Google Maps Directions** | Multi modal journey planning | Free tier |
| **Open-Meteo** | Weather data (general areas) | Free tier |
| **City of Melbourne Microclimate** | Live microclimate readings (CBD) | Free, open data |
| **City of Melbourne Pedestrian** | Live pedestrian counts | Free, open data |
| **Google Gemini** | AI Chatbot primary language model | Free tier |
| **Groq** | AI Chatbot fallback language model | Developer tier |

---

## CI/CD with GitHub Actions

The repository runs six automated workflows on every change. Together they form a complete CI/CD pipeline covering testing, security scanning, dependency management, build verification, and deployment.

| Workflow | File | Trigger | Purpose |
| :--- | :--- | :--- | :--- |
| **Tests** | `.github/workflows/test.yml` | Every push and PR to `development` | Runs the full pytest backend suite and Vitest frontend suite in parallel. Blocks merges if any test fails. |
| **CodeQL** | Managed by GitHub Security tab | Every push and PR, plus weekly | Static security analysis for Python and JavaScript. Detects SQL injection, XSS, ReDoS, hardcoded credentials, missing workflow permissions, and similar patterns. |
| **Secret Scan (Gitleaks)** | `.github/workflows/gitleaks.yml` | Every push and PR, plus weekly cron | Scans diffs and full git history for accidentally committed API keys, passwords, and credentials. |
| **Dependabot** | `.github/dependabot.yml` | Weekly cron, plus immediate runs for CVEs | Automated pull requests for outdated and vulnerable Python, npm, Docker, and GitHub Actions dependencies. |
| **Docker Build (Backend)** | `.github/workflows/docker-build.yml` | Every PR touching `BE/**` | Builds the backend Docker image in a sandbox and verifies the `/health` endpoint responds. Catches Dockerfile errors before they reach the live EC2 server. |
| **Deploy Backend to EC2** | `.github/workflows/deploy-backend.yml` | Every push to `development` touching `BE/**`, plus manual trigger | SSHes into EC2, pulls the latest code, rebuilds the Docker container, and verifies the live `/health` endpoint. |

### Required GitHub Secrets

These secrets must be configured under **Settings → Secrets and variables → Actions**:

| Secret | Purpose |
| :--- | :--- |
| `EC2_HOST` | Public IP or DNS of the EC2 backend host. |
| `EC2_USER` | SSH user on EC2 (`ubuntu` for Ubuntu AMIs). |
| `EC2_SSH_KEY` | Full contents of the `.pem` private key including BEGIN and END lines. |

The Tests, Gitleaks, CodeQL, and Docker Build workflows require no secrets. Tests run with mocked external services via `conftest.py`.

### Frontend Deployment

The frontend deploys automatically through Vercel's GitHub integration on every push to the `development` branch. No GitHub Actions workflow is needed for the frontend deploy.

---

## Testing

### Frontend Tests

The frontend uses **Vitest 4** with **Vue Test Utils 2.4** and **jsdom 29**. Run from `FE/vue-app/`:

```bash
npm test              # run once
npm run test:watch    # watch mode for development
```

Tests cover page routes, components, and the composable API helpers. There are roughly 51 frontend tests as of Iteration 3 close.

### Backend Tests

The backend uses **pytest** with **respx** for HTTP mocking. Run from `BE/`:

```bash
pytest -v             # verbose
pytest tests/test_chatbot_router.py    # single file
pytest -k "suburb"    # match keyword
```

Tests cover all 17 routers, service modules, parsers, and the `SafeJSONResponse` class. External services (Eventfinda, Ticketmaster, Gemini, Groq, OSRM) are stubbed via fixtures so the suite is deterministic and offline. There are roughly 117 backend tests as of Iteration 3 close.

---

## Deployment

### Frontend Deployment (Vercel)

The frontend is deployed automatically via Vercel's GitHub integration. Every push to the `development` branch triggers a rebuild and deploy. Environment variables are configured in the Vercel dashboard under **Settings → Environment Variables**.

To trigger a manual redeploy without a code change, use the **Redeploy** button in the Vercel dashboard.

### Backend Deployment (AWS EC2)

The backend is deployed automatically via the GitHub Actions `deploy-backend.yml` workflow on every push to `development` that touches `BE/**`. The workflow:

1. SSHes into the EC2 instance at `connectlocal.duckdns.org`.
2. Pulls the latest code from `development`.
3. Rebuilds the Docker container with `docker-compose up -d --build`.
4. Prunes dangling Docker images to reclaim disk space.
5. Verifies the live `/health` endpoint responds.

For manual deploys, SSH into the EC2 instance and run:

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

The following companion documents are available in the project handover. See the `docs/` folder or the project shared drive.

| Document | Audience | Purpose |
| :--- | :--- | :--- |
| **Product Document** | Future sponsors | Explains the value of ConnectLocal to a sponsor organisation. |
| **Support Document** | Support staff and sponsor technical staff | How to run, operate, and support the product after handover. |
| **Maintenance Document** | Future developers and technical staff | Technical setup, codebase structure, deployment, troubleshooting, and future development. |
| **Code Quality Document** | Technical reviewers | Practices and tooling that keep the codebase healthy across iterations. |
| **Build Documentation** | Technical reviewers | What was built, by which technology, in which iteration. |
| **Pair Programming Record** | Technical reviewers | Records of pair programming sessions across iterations. |
| **Security Plan** | Sponsor and security staff | Security approach, vulnerabilities, and mitigations. |
| **Data Management Plan** | Sponsor and data governance staff | What data is collected, where it lives, and how it is protected. |
| **Handover Package** | Sponsor | Full set of handover artefacts. |

API documentation is generated automatically by FastAPI and is available live at https://connectlocal.duckdns.org/docs.

---

## Team

**Team TE18: Ctrl Alt Del Loneliness**

| Member | Primary Role |
| :--- | :--- |
| Rahul Yerram | Backend developer, deployment, infrastructure |
| Yongkang Liu | Frontend developer |
| Junxing Peng | Frontend and chatbot integration |
| Dom | Frontend |
| Ajay | Frontend |
| Muhammad | Backend |
| Peng | Documentation and frontend |

**Unit:** FIT5120, Industry Experience Studio Project
**Semester:** Semester 1, 2026
**Institution:** Monash University

---

## License and Acknowledgements

This project was built for educational purposes as part of FIT5120 at Monash University. All third party datasets, libraries, and APIs are used under their respective terms of service.

Data sources gratefully acknowledged:

- Australian Bureau of Statistics (ABS) for SA2 boundary and demographic data
- City of Melbourne Open Data for pedestrian, microclimate, urban forest, and public toilet datasets
- Public Transport Victoria for the GTFS feed
- OpenStreetMap contributors for geocoding and POI data
- Eventfinda and Ticketmaster for event data
