# Reliability and trade-offs

The public SDK is designed to fail in predictable ways instead of hiding problems behind unlimited queues or retries.

## Current safeguards

- bounded in-memory queue;
- finite retry attempts with backoff;
- request-size limits;
- graceful shutdown with a finite wait;
- automated tests for batching, retries, queue pressure and shutdown;
- CI checks for linting, formatting, tests, coverage and package build.

## Failure behavior

| Scenario | Behavior |
| --- | --- |
| Temporary network failure | Retry |
| HTTP 429 | Retry after the requested delay when available |
| Temporary server failure | Retry |
| Permanent client error | Do not retry forever |
| Queue saturation | Drop the new analytics event rather than block the host bot indefinitely |
| Graceful shutdown | Attempt to flush queued events for a bounded time |
| Hard process termination | Unsent in-memory events may be lost |

## Delivery semantics

The SDK is intentionally best-effort. It does not claim durable or exactly-once delivery.

That choice keeps integration simple and protects the host Telegram bot from analytics-side failures.

## Possible future evolution

The following are design directions, not features claimed to be running today:

- durable queueing when loss tolerance becomes stricter;
- independent horizontal scaling of ingestion;
- end-to-end delivery metrics and alerting;
- idempotency or deduplication where business semantics require stronger guarantees;
- distributed tracing for ingestion diagnostics.

The key principle is to add this complexity only when real load or reliability requirements justify it.
