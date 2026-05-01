---
name: integration-test
description: Stand up required Docker services, run integration tests, and report results. Handles the full lifecycle: start, test, teardown.
license: MIT
compatibility: opencode
metadata:
  audience: library-maintainers
  workflow: testing
---

## What I Do

Run the full integration test lifecycle: start Docker services, run tests, report
results, tear down services.

## Workflow

### Step 1: Check prerequisites

- Docker is running: `docker info`
- `docker/docker-compose.yml` exists
- Test command is known (ask if not obvious)

### Step 2: Start services

```bash
docker compose -f docker/docker-compose.yml up -d
```

Wait for healthy status (poll every 3s, up to 60s):
```bash
docker compose -f docker/docker-compose.yml ps
```

### Step 3: Run integration tests

**Ruby:**
```bash
bundle exec rspec spec/integration
```

If the test command differs (e.g., a custom tag or path), ask the user.

### Step 4: Report results

Summarize:
- Total tests run
- Failures (with relevant output)
- Which services were involved

### Step 5: Tear down

```bash
docker compose -f docker/docker-compose.yml down
```

(Keep volumes by default; use `down -v` only if you want a clean state next time.)

## If Tests Fail

1. Show the failing test output.
2. Check if services were actually healthy before the test ran.
3. Check service logs: `docker compose -f docker/docker-compose.yml logs`
4. Suggest whether the failure is a test bug, app bug, or service config issue.

## Skipping Teardown

If you want to re-run tests quickly without restarting services, say "skip teardown"
and I'll leave services running. Remember to `docker compose down` when you're done for the day.
