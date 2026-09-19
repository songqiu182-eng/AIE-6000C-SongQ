# Week 3 Submission - Individual Readiness Lab

## Student information

- Name: QIU, Song
- Student ID: 21313591
- Repository: https://github.com/songqiu182-eng/AIE-6000C-SongQ
- Checkpoint tag: `w03-readiness`
- Commit SHA: resolved from `w03-readiness^{commit}` and submitted in Canvas

## 1. What I changed

I made case creation reject titles and descriptions that contain only whitespace.
Previously, values such as `"   "` passed the length checks and were stripped to empty
strings immediately before being stored. `CaseCreate` now strips surrounding whitespace
before applying its length constraints, so blank values return HTTP 422 while valid values
are stored without unintended leading or trailing whitespace.

I also updated the Docker image so the repository's development dependencies and tests are
available for reproducible container-based verification.

## 2. Files touched

- `services/common/schemas.py` - normalize input before validating its length.
- `tests/integration/test_api_case_flow.py` - verify blank-field rejection and whitespace normalization.
- `Dockerfile` - install development dependencies and copy the test suite into the image.
- `submissions/week03/README.md` - document the readiness change and evidence.

## 3. How I verified it

- `docker compose config --quiet` completed successfully.
- `docker compose build --no-cache api` completed successfully with Python 3.11 and the dev dependencies.
- `docker compose run --rm --no-deps api pytest -q tests/unit tests/integration` returned `6 passed`.
- `docker compose run --rm --no-deps api ruff check .` returned `All checks passed!`.
- `docker compose run --rm --no-deps -e SMOKE_BASE_URL=http://api:8000 api pytest -q tests/smoke` returned `1 passed`.
- `GET /`, `GET /health/live`, `GET /health/ready`, `GET /metrics`, and the AI live endpoint returned HTTP 200.
- Blank titles and descriptions returned HTTP 422 through the running API.
- An end-to-end case was normalized, persisted, triaged as `access`, and completed with one worker attempt.
- PostgreSQL queries and the worker/AI logs confirmed the completed case and job.
- With PostgreSQL stopped, liveness remained HTTP 200 while readiness returned HTTP 500; readiness returned HTTP 200 after PostgreSQL restarted.

## 4. Known limitations or notes

- The change only affects case-creation input and does not change existing stored rows.
- The test run reports two third-party deprecation warnings from Starlette and python-json-logger; they do not affect the passing result.
- The starter worker records failed jobs but does not retry them automatically.

## 5. AI Use Statement

OpenAI Codex was used to interpret the Week 3 lab instructions, inspect the starter repository,
identify the whitespace-validation issue, implement the bounded change and tests, and run the
documented verification commands. The resulting behavior was checked through automated tests,
live HTTP requests, database queries, and service logs. No AI-generated result was accepted
without repository-based or runtime verification.

## 6. Baseline evidence note

- Repository remote: `https://github.com/songqiu182-eng/AIE-6000C-SongQ.git`
- Working branch: `readiness/reject-blank-case-input`
- Starting commit: `377cdac` (`Initial commit`)
- Configured ports: API `8000`, AI `8100`, PostgreSQL `5432`
- Running services: `db`, `ai`, `api`, `worker`
- Health results: live HTTP 200; ready HTTP 200 with PostgreSQL available
- Traced case: `2b8b3a19-06e1-4ff6-a585-add9ab9f7eb2`, final status `triaged`, AI label `access`
- Verification result: unit/integration `6 passed`; smoke `1 passed`; Ruff passed
- Blockers: none
