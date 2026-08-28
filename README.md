# ReviveAI — Autonomous AI Revenue Recovery Agent

> **ReviveAI** is an intelligent, closed-loop revenue recovery platform that detects transaction leaks (failed payments, checkout abandonments), evaluates recovery propensity with ML, autonomously selects safe, high-value interventions under strict policy guardrails, and executes recovery actions via Razorpay.

---

## 🏗️ Monorepo Architecture

```text
RazorPay---RecoveryAI/
├── apps/
│   ├── web/                     # Next.js frontend dashboard & merchant portal
│   └── api/                     # FastAPI backend, recovery engine & webhook ingestion
│
├── packages/
│   └── shared/                  # Shared TypeScript/Python types, schemas, and utils
│
├── infrastructure/
│   ├── docker/                  # Container definitions (Dockerfile.api, Dockerfile.web)
│   └── docs/                    # Architecture diagrams, specifications, API references
│
├── data/
│   ├── raw/                     # Raw transaction & checkout event datasets (git-ignored)
│   ├── processed/               # Cleaned, feature-engineered training data (git-ignored)
│   └── models/                  # Trained ML model weights and artifacts (git-ignored)
│
├── scripts/                     # Utility scripts for data synthesis, seeding & evaluation
│
├── .env.example                 # Template for environment configuration
├── .gitignore                   # Comprehensive ignore rules for Next.js, Python, ML & OS
├── README.md                    # Project overview & documentation
└── docker-compose.yml           # Multi-container local orchestration (Postgres, Redis, API, Web)
```

---

## ⚡ Key Capabilities

- **Automated Revenue Leak Detection**: Ingests payment failure and checkout abandonment webhooks in real time.
- **ML Propensity & Expected Value Engine**: Predicts customer recovery likelihood and calculates net recovery value before taking action.
- **Autonomous Policy Guardrails**: Enforces discount limits, contact frequency limits, quiet hours, and cooling-off intervals.
- **Razorpay Test Mode Integration**: Triggers automated recovery payment links, payment retries, and customer notifications.
- **Complete Audit Trail & Learning Loop**: Tracks every autonomous decision with explainability logs and updates models based on conversion outcomes.

---

## 🛠️ Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | Next.js 14+, TypeScript, Tailwind CSS, shadcn/ui, TanStack Query, Recharts |
| **Backend** | Python 3.11+, FastAPI, Pydantic v2, SQLAlchemy, Alembic |
| **Async / Queues** | Celery, Redis |
| **Database** | PostgreSQL 16+ |
| **Machine Learning** | CatBoost, scikit-learn, Pandas, NumPy |
| **Infra & DevOps** | Docker, Docker Compose |

---

## 🚀 Quickstart

### Prerequisites

- [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/)
- [Node.js 20+](https://nodejs.org/) (for frontend local development)
- [Python 3.11+](https://www.python.org/) (for backend local development)

### 1. Clone & Configure Environment

```bash
cp .env.example .env
```

Edit `.env` with your development parameters (Razorpay test keys, database credentials, API keys).

### 2. Run with Docker Compose

Start the full stack (PostgreSQL, Redis, API, and Frontend):

```bash
docker-compose up -d
```

- **Frontend Dashboard:** [http://localhost:3000](http://localhost:3000)
- **API & Swagger Docs:** [http://localhost:8000/docs](http://localhost:8000/docs)
- **PostgreSQL:** `localhost:5432`
- **Redis:** `localhost:6379`

---

## 🔒 Security & Best Practices

- **Never commit `.env` or any secret keys** to version control.
- All model weights, large datasets (`data/raw/`, `data/processed/`, `data/models/`), and virtual environments are explicitly excluded via `.gitignore`.
