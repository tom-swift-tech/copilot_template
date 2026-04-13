# Security Standards

> Starter example. Replace or expand for your project.

## Scope

These standards apply to all code, infrastructure, and operational practices in this project. Security is a property of the whole system — there is no carved-out "security work" that's separate from regular work.

## Principles

1. **Defense in depth.** No single control is the last line. Auth + authz + input validation + output encoding + monitoring — they all run together.
2. **Default deny.** Permissions, network access, and feature exposure all default to off. Opening up is an explicit decision.
3. **Least privilege.** Every identity (human, service, role) gets the minimum permissions needed for its actual job, not the maximum permissions someone might someday want.
4. **Secrets are radioactive.** They contaminate everything they touch — logs, screenshots, error messages, support tickets. Treat them accordingly.
5. **Assume breach.** Design as if an attacker is already inside the network. The blast radius of a compromise is what matters.

## Authentication

- **Never roll your own auth.** Use a vetted provider (Auth0, Okta, Entra ID, Cognito) or a vetted library. The cost of writing your own is paid in incidents, not lines of code.
- Passwords (if any): hashed with `argon2id` (or `bcrypt` minimum). Never `MD5`, `SHA-1`, or unsalted hashes.
- MFA on every privileged account. No exceptions for "convenience" — convenience is how compromises happen.
- Session tokens: short-lived, rotated on privilege change, revocable on logout.
- Service-to-service auth uses short-lived credentials (mTLS, OIDC tokens, managed identity), not long-lived API keys.

## Authorization

- Auth*z* is decided at the **resource boundary**, not at the UI. The UI hiding a button is not a security control.
- Use a single, named policy layer — RBAC, ABAC, or a policy engine like OPA. Scattered `if (user.role === ...)` checks rot.
- Every endpoint requires authorization by default. Public endpoints are the exception and must be explicitly annotated as such in code review.
- Audit log every privileged action with: who, what, when, from where, with what result. Audit logs are append-only and stored separately from application logs.

## Secrets Management

- Secrets live in Key Vault / Parameter Store / Secrets Manager / Vault. Never in:
  - Source code
  - Environment files committed to the repo (`.env` is in `.gitignore`; `.env.example` has placeholders only)
  - Container images
  - CI configuration files (use the CI secrets store)
  - Application logs (see redaction in `OBSERVABILITY.md`)
  - Error messages returned to users
- Rotate secrets on a schedule and on every suspected exposure. Have a rotation runbook before you need one.
- Service identities use managed identity / workload identity wherever the platform supports it — no static credentials.

## Input Validation

- **Validate at the trust boundary.** External input is hostile until proven otherwise; internal calls between trusted services can trust types and contracts.
- Allowlist over blocklist. "Reject anything that isn't on this approved list" beats "reject anything that looks bad" — the latter has infinite holes.
- Parse, don't validate. Convert raw input into a strong type as early as possible; the rest of the code works with the type, not the raw string.

## Output Encoding

- HTML output: contextual escaping (HTML body, attribute, URL, JS, CSS — each context has different escaping rules). Frameworks usually provide this; use them.
- SQL: parameterized queries only. String concatenation is forbidden. See `DATABASE-DESIGN.md`.
- Shell: avoid shelling out at all where possible. When unavoidable, use the language's `argv`-style API (no shell interpolation), never string concatenation.
- JSON / YAML / XML: use a real serializer. Don't hand-build serialized output.

## Dependencies

- Every dependency is a trust decision. Evaluate maintenance status, license, security advisory history before adding.
- Lockfiles committed. Reproducible builds are non-negotiable.
- Automated vulnerability scanning (Dependabot, Renovate, Snyk) on every PR. CRITICAL and HIGH advisories block merge.
- Pin to specific versions; no floating ranges in production lockfiles.

## Network

- Default-deny network policies. Services can only reach the dependencies they actually need.
- TLS everywhere, including service-to-service inside the cluster. Not "we'll add it later."
- Public endpoints minimized. Anything that doesn't need to be on the internet should be on a private network.

## Incident Response

- Have a runbook for the obvious incidents (credential leak, dependency CVE, suspected breach) before you need them. Improvising during an incident loses time.
- Postmortem every incident, blameless. The artifact is *what to change in the system*, not *who screwed up*.

## Test and Validate (mandatory)

> Security controls that have never been exercised are not controls — they're hopes.

1. **Auth bypass test.** For every new endpoint, write three tests: (a) no credentials → asserts `401`, (b) invalid credentials → asserts `401`, (c) authenticated user with insufficient privileges → asserts `403`. The presence of all three is the evidence the endpoint is actually protected.
2. **Secret-leak test.** Grep the test logs and CI artifacts for known secret prefixes (`Bearer `, `aws_secret_`, project-specific patterns). Zero matches required.
3. **Dependency scan.** CI runs the SCA tool on every PR. Open CRITICAL or HIGH advisories block merge.
4. **Static analysis.** SAST tool runs on every PR. New findings block merge; existing findings get a documented remediation date.
5. **Input validation tests.** For every parsing/validation function, include negative tests: oversized input, malformed input, injection-shaped input, unicode edge cases. The test names should describe the attack: `test_login_rejects_sql_injection_in_username`.
6. **Authorization regression test.** When a permission rule changes, write a test that asserts the new rule with both an allowed and a denied case. Permissions silently broadening is a top-3 cause of real incidents.
7. **Capture the evidence** in the PR: CI scan results, test names, manual review notes. Reviewer rejects security-sensitive changes without this evidence.
