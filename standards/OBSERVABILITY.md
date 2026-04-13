# Observability Standards

> Starter example. Replace or expand for your project.

## Scope

These standards apply to all services in production: logs, metrics, traces, and the on-call workflow that consumes them. Local development is exempt — observability is for shared environments.

## The Three Pillars

| Pillar  | Use it for                                    | Don't use it for                          |
|---------|-----------------------------------------------|-------------------------------------------|
| Logs    | What happened, in detail, for one request     | Aggregated counts, alerting               |
| Metrics | Aggregated counts, rates, distributions       | Per-request detail, debugging individuals |
| Traces  | Cross-service request flow, latency breakdown | Replacing logs or metrics                 |

If you reach for logs to build a dashboard, you wanted a metric. If you reach for metrics to debug one user's bad request, you wanted a trace.

## Logging

- **Structured JSON only.** Plain text logs cannot be queried at scale.
- Every log line includes: `timestamp` (ISO 8601 UTC), `level`, `service`, `trace_id`, `request_id`, `message`, plus event-specific fields.
- Log levels mean what they say:
  - `ERROR` — something is broken; on-call may need to act
  - `WARN` — something is unusual; might need investigation in the morning
  - `INFO` — normal operations; useful for forensics
  - `DEBUG` — verbose; off in production
- **Never log secrets.** No tokens, passwords, full credit card numbers, or PII. Add a redaction layer at the logger if your runtime allows.
- **Never log at `INFO` inside hot loops.** A request handler logging once per request is fine; a parser logging once per token is not.
- Errors include the exception, stack trace, and the operation context (what was being attempted, with which inputs — sanitized).

## Metrics

- Use the **RED method** for services: **Rate** (requests/sec), **Errors** (errors/sec), **Duration** (latency distribution).
- Use the **USE method** for resources: **Utilization**, **Saturation**, **Errors**.
- Latency is measured as a distribution (histogram), not an average. Track p50, p95, p99 — averages hide the bad tail.
- Metric names: `<service>_<resource>_<unit>` — e.g., `api_http_requests_total`, `api_http_request_duration_seconds`.
- Cardinality discipline: do not put unbounded values (user IDs, URLs with IDs in them, full URLs) into label dimensions. High cardinality kills the metrics backend.

## Tracing

- Every request that crosses a service boundary gets a `trace_id`. Propagated via standard headers (W3C Trace Context: `traceparent`, `tracestate`).
- Spans cover meaningful operations: HTTP handler, database query, external API call. Not every function.
- Span names are stable identifiers, not interpolated strings: `db.query.user_by_id`, not `SELECT * FROM users WHERE id = 42`.
- Sample at the head if volume is high; record errors at 100% regardless of sampling rate.

## Alerting

- **Alert on symptoms, not causes.** "Error rate above 1%" is a symptom; "CPU above 80%" is a cause that may or may not matter.
- Every alert has a runbook link. An alert without a runbook is on-call torture.
- Every alert has an owner. An alert with no owner is everyone's problem, which means no one's.
- **Page only for things that need a human in the next 15 minutes.** Everything else is a ticket or an email.
- Test alerts via game days — fire them deliberately and confirm the runbook actually leads to a fix.

## Dashboards

- One overview dashboard per service: RED metrics, dependencies, recent deploys.
- Dashboards are versioned in the repo, not just clicked together in the UI. Loss of the UI = loss of the dashboard otherwise.
- Pin the time range to "last 1 hour" by default — incident view, not historical view.

## Test and Validate (mandatory)

> A monitoring system that has never alerted is a monitoring system that doesn't work — you just haven't proven it yet.

1. **Synthetic alert test.** For every new alert, fire it deliberately at least once (in staging) and confirm the page lands and the runbook resolves it.
2. **Log query smoke test.** Pick the top 3 queries on-call would run during an incident; verify they return results in under 5 seconds.
3. **Trace propagation test.** Fire a request across every service boundary and confirm the trace shows up end-to-end with no broken parents.
4. **Cardinality check.** Run a query against the metrics backend for each new metric: `count by (label) (metric)`. If any label has more than ~1000 distinct values and isn't intentional, fix it before deploy.
5. **Dashboard load test.** Open the overview dashboard for the time range your on-call rotation actually uses (typically 6 hours). It must load in under 5 seconds — a slow dashboard during an incident is a non-functional dashboard.
6. **Redaction test.** Send a request containing a known fake secret (e.g., `Bearer test-secret-MUST-NOT-LOG`). Grep the log store. Zero matches required.
