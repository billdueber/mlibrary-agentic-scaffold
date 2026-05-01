# Testing Standards

These instructions apply to all library projects.

---

## Philosophy

- Tests are documentation. A passing test suite is a contract with your users.
- Libraries are tested at the unit level (pure logic) and integration level (with real services).
- Never mock what you can test for real with Docker.
- Test behavior, not implementation.

---

## Test Layers

| Layer | What it tests | Uses real services? | Speed |
|-------|--------------|---------------------|-------|
| Unit | Pure logic, single class/function | No | Fast (<1s) |
| Integration | Interaction with DB, search, cache, etc. | Yes (Docker) | Slow (seconds) |
| Contract | Public API stability | No | Fast |

Integration tests live in `spec/integration/` (Ruby). Unit tests must pass without Docker.

---

## CI vs Local

- Unit tests must always pass in CI without any additional services.
- Integration tests run in CI against Docker services started in the workflow.
- Tag or separate integration tests so the test runner can skip them without Docker.

See `instructions/docker-integration.md` for how to start services locally.

---

## Coverage

Target ≥90% line coverage on production code. 100% is not always worth the cost; use judgment.

- **Ruby**: SimpleCov with `minimum_coverage 90` and branch coverage enabled.

---

## Mocks vs Live Services

**Agent rule: strongly prefer integration tests against live Dockerized services over mocks or fixtures.**

This is the default. Do not reach for mocks or stubs because they are easier or faster to write.

Acceptable reasons to use a mock or stub:

- The external service cannot be run locally in Docker (e.g., a paid third-party API with no OSS image).
- A specific failure mode (network timeout, 500 error) is genuinely impractical to trigger with a real service.
- Performance has been measured and is a demonstrated problem — not a theoretical concern.

Unacceptable reasons to use a mock or stub:

- "It's faster to write."
- "Docker takes time to start."
- "The unit test would be simpler."
- "I'm not sure how to dockerize this service."

When a mock is used, leave a comment naming the specific reason it was necessary. If the reason is performance, record the measured baseline that justified the decision.

---

## Test Data and Fixtures

- Prefer factories (FactoryBot for Ruby) over fixtures.
- Each test must be independent: no shared mutable state.
- Clean up after integration tests (truncate tables, flush caches).
- Never use production data in tests.

---

## When Agents Write Tests

- Write tests before or alongside production code — never after the fact.
- When fixing a bug: write a failing test that reproduces the bug, then fix it.
- When adding a feature: write tests that document expected behavior.
- Name tests to describe behavior: `"returns nil when no match found"`, not `"test_parse_returns_nil"`.
- Do not write tests that test the language or framework itself.
