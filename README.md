# HangMap

**Stellar network activity dashboard.** Block explorers list transactions one by one. HangMap shows what the network is doing as a whole: operation activity, top dApps, and how activity splits across payments, DEX, Soroban, and more.

> Open source · [github.com/hangmap](https://github.com/hangmap) · Version: **0.1**

## What you see

| Question | Answer in HangMap |
| --- | --- |
| How busy is the network? | Total operations for the day, week, or month |
| What is happening on chain? | Share of payments, DEX, Soroban, trustlines, and account ops |
| Which accounts drive activity? | Top source accounts by operation count, with known names where available |
| Which dApps are used most? | Top Soroban contracts and protocols, ranked and drillable |
| What does this address mean? | Labels from the entity registry, Stellar Expert, and Hubble metadata |

The treemap is the center of the product. Tile size is share of operation activity. Color is category. Click to go from broad categories down to specific wallets and contracts.

## Why not just use an explorer?

Explorers are built to look up a single address or transaction. HangMap is built to read the network at a glance and then zoom in. It groups raw operations into categories, ranks the actors behind them, and surfaces the few numbers that matter before you ever open a block detail page.

---

## Architecture & Codebase Split

HangMap is split into two independent codebases:

1. **Frontend (`hangmap`)**: Next.js 16 application containing the dashboard UI, D3 treemaps, charts, corridors, and client state.
2. **Backend (`hangmap-backend`)**: Standalone Node.js + Express TypeScript service in `../lumenmap-backend` handling BigQuery Hubble queries, OpenTelemetry telemetry, caching, rate limiting, and REST API endpoints.

```text
Browser / Client
      │
      ▼ (Port 3000)
Next.js Frontend (HangMap)
      │
      │  Rewrites: /api/:path* → NEXT_PUBLIC_API_URL/api/:path*
      ▼ (Port 3001)
Express API Backend (HangMap Backend)
      │
      ├─► BigQuery (Hubble Stellar DBT Dataset)
      └─► Fallback / Local Fixture Mode (Offline / Testing)
```

---

## Features

- **Hierarchical Treemap**: D3 squarified layout, drill-down breadcrumbs, and node detail view.
- **Views**: Operation Types, Accounts & Contracts, and Payment Flow Corridors.
- **Metrics**: Operation count, transaction count, XLM volume, verified Circle USDC volume, and Protocol TVL.
- **Time Periods**: 1 day, 7 days, 30 days, calendar month, with side-by-side period comparisons.
- **Time-Series Analytics**: Hourly/daily UTC buckets, operations vs. transactions, and stacked category-share area charts.
- **Interactive Search**: Typo-tolerant search across accounts, contracts, protocols, assets, and categories.
- **Watchlist & Research Notes**: Local storage persistence, CSV import/export, and delta tracking.
- **Shareable Snapshots**: Deep-link URL state and sharable research view snapshots.
- **Deterministic Fixtures**: Works completely offline without requiring Google Cloud / BigQuery credentials.

---

## Getting Started

### Prerequisites

- Node.js >= 20.x
- npm >= 10.x

### 1. Running the Backend Service

In `../lumenmap-backend`:

```bash
cd ../lumenmap-backend
npm install
npm run dev
```

The backend starts at `http://localhost:3001`.

### 2. Running the Frontend Dashboard

In this directory:

```bash
npm install
npm run dev
```

The dashboard will be live at `http://localhost:3000`.

---

## Environment Variables

### Frontend Configuration (`.env.local`)

| Variable | Description | Default |
| --- | --- | --- |
| `NEXT_PUBLIC_API_URL` | Base URL of the HangMap backend service | `http://localhost:3001` |
| `NEXT_PUBLIC_SITE_URL` | Canonical public URL of the frontend | `http://localhost:3000` |

### Backend Configuration (`../lumenmap-backend/.env`)

| Variable | Description | Default |
| --- | --- | --- |
| `PORT` | Port for the Express REST server | `3001` |
| `LUMENMAP_DATA_SOURCE` | Set to `fixture` for deterministic local data without GCP | `fixture` (if credentials absent) |
| `GCP_SERVICE_ACCOUNT_KEY` | Base64-encoded Google Cloud service account JSON | None |
| `GOOGLE_APPLICATION_CREDENTIALS` | Path to Google Cloud credentials file | None |
| `CACHE_BACKEND` | `memory` or `redis` | `memory` |

---

## API Endpoints (Backend)

The backend provides full REST endpoints matching OpenAPI 3.1 specification at `/api/openapi.json`:

- `GET /api/health` — Service readiness and dependency health checks
- `GET /api/activity?period={1d|7d|30d|month}` — Dashboard activity and treemap dataset
- `GET /api/v1/flow?period={1d|7d|30d|month}` — Payment flow edges and nodes
- `GET /api/v1/timeseries?period={...}&granularity={hour|day}` — Time-series operation metrics
- `GET /api/v1/protocols/health` — Protocol scorecard health rankings
- `GET /api/v1/snapshots/:id` — Retrieve saved research snapshot
- `POST /api/v1/snapshots` — Save a new research snapshot
- `GET /api/openapi.json` — Machine-readable OpenAPI 3.1 schema

---

## Testing & Quality

### Frontend Tests
```bash
npm test          # Run unit and integration tests
npm run typecheck # TypeScript validation
```

### Backend Tests
```bash
cd ../lumenmap-backend
npm test          # Run API route and query tests
```

---

## Embeds

HangMap panels can be embedded in external apps via iframes:

```html
<iframe
  title="HangMap KPI"
  src="http://localhost:3000/embed/kpi?period=7d&metric=ops"
  width="100%"
  height="160"
  frameborder="0"
></iframe>
```

---

## License

MIT
