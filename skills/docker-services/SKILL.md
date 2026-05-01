---
name: docker-services
description: Start, stop, and verify Docker Compose services for local development and integration testing. Detects which services are needed from the project's docker/docker-compose.yml.
license: MIT
compatibility: opencode
metadata:
  audience: library-maintainers
  workflow: development
---

## What I Do

Manage Docker Compose services for local integration testing. I help you start services,
wait for them to be healthy, and tear them down cleanly.

## Commands

### Start all services

```bash
docker compose -f docker/docker-compose.yml up -d
```

### Wait for healthchecks to pass

```bash
docker compose -f docker/docker-compose.yml wait
```

Or poll manually:
```bash
docker compose -f docker/docker-compose.yml ps
```

### Check service logs (if something is unhealthy)

```bash
docker compose -f docker/docker-compose.yml logs {service_name}
```

### Stop services (keep data volumes)

```bash
docker compose -f docker/docker-compose.yml down
```

### Stop services and destroy data (clean slate)

```bash
docker compose -f docker/docker-compose.yml down -v
```

## Typical Workflow

1. `docker compose -f docker/docker-compose.yml up -d` — start services
2. Wait ~10s, then check `docker compose -f docker/docker-compose.yml ps` — all should be `healthy`
3. Run integration tests
4. `docker compose -f docker/docker-compose.yml down` — stop services

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Container exits immediately | Bad env var or port conflict | Check logs: `docker compose logs {svc}` |
| Port already in use | Another process on that port | `lsof -i :PORT` to find it |
| Healthcheck failing | Service slow to start | Increase `retries` in docker-compose.yml |
| Tests can't connect | Wrong env var / wrong port | Verify `TEST_*_URL` env vars match docker-compose.yml ports |

## If docker/docker-compose.yml Doesn't Exist

I will check what services the project uses and offer to create an appropriate
`docker/docker-compose.yml`. Refer to `instructions/docker-integration.md` for
the conventions: service definitions go in `docker/docker-compose.yml`, every
service must have a healthcheck, and only services the project actually uses
should be included.
