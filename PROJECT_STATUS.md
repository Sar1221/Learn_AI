# LearnAdapt AI — Project Status

## Implementation plan and prerequisites

1. **Foundation:** create a React/TypeScript/Vite client, FastAPI service, environment template, database migration, and health check.
2. **Core learning loop:** seed eight DSA topics and a validated 80-question bank; add onboarding, diagnostic, attempts, learner statistics, and deterministic recommendations.
3. **Study support:** connect revisions, confidence analytics, error notebook, experiments, transfer, teach-back, focus, exam plans, and reflections to persisted learner evidence.
4. **Finalize:** test available behavior, document setup/deployment, and list service-dependent limitations.

### External prerequisites

- Supabase project URL and anon key for hosted authentication/database setup. The included local demo backend works without these credentials; hosted Supabase persistence/auth still requires configuration and adapter completion.
- OpenAI API key is optional. The application uses deterministic fallback teaching feedback; no AI call is needed for the demo loop.
- Deployment accounts (Vercel/Render or equivalent) are required only to deploy.

## Current increment

- [x] Workspace inspected; it was empty and was not a Git repository.
- [x] Phase 1: application structure, API health check, environment template, Supabase schema/RLS migration, client/backend communication.
- [x] Phase 2 local demo: curated 80-item bank, onboarding, ten-question diagnostic, attempts and learner stats in SQLite.
- [x] Phase 3: explainable mastery, normalized weights, priority factors, recommendations and revision schedule.
- [~] Phase 4–7: dashboard, topics, practice/hints, confidence chart, error notebook, revision list, teach-back fallback, transfer practice, focus, exam plans, reflections. Several workflows are still partial; see checklist/README.
- [~] Phase 9: frontend build and backend tests pass. Hosted security/data isolation verification, accessibility review, and deployment remain.

## Checks

- Backend: `pytest -q --basetemp=.pytest-tmp` — 10 passed; Python `compileall` passed.
- Frontend: TypeScript project build and Vite production build — passed. Chart dependencies are split into their own chunks; largest chunk is about 463 kB uncompressed.
- Supabase migration was not run against a live project.

## Known limitations

- No Supabase or OpenAI credentials were present. The schema, RLS rules, and exportable question-bank seed exist, but the backend currently uses local SQLite and does not claim production database persistence.
- Registration/login UI is wired to optional Supabase Auth, but authenticated API verification and records have not been validated with a real project. Supabase JWT signing configuration is required; asymmetric JWTs use the project's JWKS.
- OpenAI generation is not implemented; teach-back uses a clear rule-based fallback.
- Remaining feature gaps: daily coach completion/reordering, full method experiments, focus pause/resume/distraction events, automated weekly summaries, account deletion lifecycle, persistent recommendation rows, and full integration/accessibility tests.
