# Docker Integration Test Services

These instructions describe how to use Docker Compose for running integration tests
against real services (databases, search engines, caches, etc.).

---

## When to Use Docker

Use Docker Compose for integration tests whenever the library interacts with:
- PostgreSQL
- MySQL / MariaDB
- SQLite (no Docker needed — SQLite is a file)
- Redis
- OpenSearch / Elasticsearch
- Solr
- Any custom REST service your library wraps

Do **not** mock these services in integration tests. The point is to test real behavior.

---

## Docker Compose File Location

Place service definitions in `docker/docker-compose.yml`. This keeps them out of the
project root and makes it clear they're for development/test only.

Only include the services your project actually needs. Every service definition must include a healthcheck so the test runner can wait for readiness before running tests.

---

## Starting and Stopping Services

```bash
# Start all services (detached)
docker compose -f docker/docker-compose.yml up -d

# Wait for services to be healthy
docker compose -f docker/docker-compose.yml wait

# Stop and remove containers (keeps volumes)
docker compose -f docker/docker-compose.yml down

# Stop and remove containers AND volumes (clean slate)
docker compose -f docker/docker-compose.yml down -v
```

Use the `docker-services` skill for a guided workflow.

---

## Connection Configuration in Tests

Do not hardcode connection strings. Use environment variables with sensible defaults
that match your Docker Compose configuration.

### Ruby

```ruby
# spec/support/service_config.rb or similar
module TestConfig
  # Add constants for each service your project uses.
  # Defaults should match your docker/docker-compose.yml port mappings and credentials.
  POSTGRES_URL   = ENV.fetch("TEST_POSTGRES_URL",   "postgresql://test_user:test_password@localhost:5432/test_db")
  REDIS_URL      = ENV.fetch("TEST_REDIS_URL",       "redis://localhost:6379/0")
  OPENSEARCH_URL = ENV.fetch("TEST_OPENSEARCH_URL",  "http://localhost:9200")
  # Add / remove entries to match only the services your project actually uses.
end
```

---

## Integration Test Lifecycle

1. **Setup**: Start services (`docker compose up -d`)
2. **Verify**: Wait for healthchecks to pass
3. **Run**: Execute integration tests
4. **Teardown**: Stop services (`docker compose down`)

For local development, you can leave services running across test runs (faster).
For CI, always start fresh and tear down after.

---

## Isolating Integration Tests

Tag or separate integration tests so they can be skipped without Docker:

### Ruby (RSpec)

```ruby
# spec/spec_helper.rb or spec/integration_helper.rb
RSpec.configure do |config|
  config.define_derived_metadata(file_path: %r{spec/integration}) do |meta|
    meta[:integration] = true
  end
end
```

Run with: `bundle exec rspec --tag ~integration` (skip) or `bundle exec rspec` (all).

---

## CI Integration

See `instructions/ci-github-actions.md` for the full CI workflow template. Add a
`services:` block to the job for each service your tests need. Example for postgres:

```yaml
services:
  postgres:
    image: postgres:16-alpine
    env:
      POSTGRES_DB: test_db
      POSTGRES_USER: test_user
      POSTGRES_PASSWORD: test_password
    options: >-
      --health-cmd pg_isready
      --health-interval 5s
      --health-timeout 5s
      --health-retries 5
    ports:
      - 5432:5432
```

Add one `services:` entry per service. Use the same image, env vars, and port as
in your `docker/docker-compose.yml` so local and CI behavior match.

---

## Custom REST Endpoints

For integration testing against custom REST APIs (e.g., your library wraps a third-party API):

1. Use [webmock](https://github.com/bblimke/webmock) + [VCR](https://github.com/vcr/vcr) for recorded responses in unit tests.
2. For true integration tests against a real service, use a test-mode API key and a dedicated test environment.
3. Document which tests require external connectivity and tag/skip them in CI if the service is unavailable.
