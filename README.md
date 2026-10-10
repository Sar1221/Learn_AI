# LearnAdapt AI

Evidence-based DSA practice that helps learners see how their understanding changes over time.

> **Current state:** this is a working local demo vertical slice, not the production-ready application described in the master prompt. It includes the React/Vite client, FastAPI API, deterministic adaptive engine, a curated 80-question bank, a ten-question diagnostic, and a local SQLite demo store. Optional Supabase registration/login is wired in the client and token verification in the API, but no real Supabase account was configured or validated. The Supabase schema and seed define the hosted tables/RLS; the backend does not yet connect to that database. The interface labels demo data accordingly.

## Run locally

### Backend

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item ..\.env.example ..\.env
uvicorn app.main:app --reload --port 8000
```

The API stores the shared demo learner's activity in `data/learnadapt-demo.sqlite3`. Set `DATABASE_PATH` to a different writable path if needed. This local database is for demonstration only.

### Frontend

Use Node.js 22+ and pnpm/npm:

```powershell
pnpm install
pnpm dev
```

Open `http://localhost:5173`. The client calls `http://localhost:8000` by default. Set `VITE_API_URL` at build time to change it.

## Current implemented features

- Eight DSA topics and 80 curated question/answer/explanation entries; answer keys are withheld until the attempt is submitted.
- Topic-based practice, four progressive hint levels, confidence ratings, idempotent attempt submissions, correctness feedback, and separate transfer flags.
- Explainable mastery calculation (version 1.0), missing-evidence handling, normalized available weights, priority factors, and recommendation reasons.
- Local demo onboarding, topic snapshot, confidence/correctness chart, review dates, error notebook, focus sessions, exam-plan/reflection forms, rule-based teach-back feedback, and a transparent insufficient-data experiment view.
- Data export for the current learner and private records keyed by the authenticated user ID when the API is configured for authenticated mode.
- SQL migration with owner-only RLS policies and public curriculum read policies.

## Configuration and hosted services

Copy `.env.example` and set `APP_MODE=demo` for the local walkthrough. Demo mode needs no API keys. For authenticated mode, set `APP_MODE=authenticated`, `SUPABASE_URL`, and `SUPABASE_ANON_KEY`; set `SUPABASE_JWT_SECRET` for legacy HS256 signing or use the project's JWKS for asymmetric ES256/RS256 tokens. The backend still uses SQLite in authenticated mode. **Do not deploy authenticated mode as a hosted production system until the Supabase persistence adapter is implemented.** The included database migration is not automatically applied by the API.

`OPENAI_API_KEY` and `OPENAI_MODEL` are optional placeholders. Teach-back currently uses labeled rule-based fallback feedback; no successful OpenAI call is claimed or required. The API key must remain server-side if an AI adapter is added.

## Supabase setup (schema only at this stage)

Create a Supabase project, then apply `supabase/migrations/202610100001_initial_schema.sql` followed by `supabase/seed.sql` using the Supabase SQL editor or CLI. The migration enables RLS for user-owned tables, keeps correct answers in a separately protected table, and limits curriculum tables to public reads. Supabase is not yet wired to the current SQLite repository.

## Verification

Backend tests live in `backend/tests`. Install backend requirements, then run `python -m pytest -q` from `backend`. Frontend production build: `pnpm install` then `pnpm build`; the first pnpm install may require approving the esbuild build script. Backend core/handler tests passed (10 tests); they do not replace authenticated HTTP integration or cross-user RLS tests. TypeScript and Vite production build passed in this workspace. Chart dependencies are split into their own chunk.

## Deployment (after hosted persistence/auth is completed)

- **Frontend:** deploy the Vite static build to Vercel or an equivalent host; set `VITE_API_URL` to the backend URL. Set only public Supabase URL/anon values in the browser if the Supabase Auth client is enabled.
- **Backend:** deploy `backend` to Render or an equivalent ASGI host with `uvicorn app.main:app --host 0.0.0.0 --port $PORT`; configure secrets in the provider environment, set explicit `FRONTEND_URL`, and use a persistent database. The present SQLite adapter is not suitable for multi-instance deployment.
- **Database:** apply the migration and seed data with the Supabase CLI; verify RLS and cross-user access before launch.

## Security and limitations

- Demo mode deliberately has one shared demo identity and disables demo data deletion; never use it for private user data.
- Authenticated mode verifies a bearer token and scopes SQL queries by its subject, but current SQLite storage is not a substitute for Supabase RLS-backed production persistence.
- No credentials, deployment, certification, AI response, or external database operation is represented as completed.
- Remaining required work: Supabase persistence adapter, persistent recommendation records, full coach task lifecycle, method-specific experiment observations, pause/resume focus sessions and distraction event log, generated evidence-backed weekly summary, authenticated account deletion confirmation, API/frontend integration tests and accessibility review.
