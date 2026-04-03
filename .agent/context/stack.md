# Technology Stack

> Runtime dependencies, toolchain, and infrastructure components.
> Updated when major dependencies change. Referenced by all agents for compatibility decisions.

---

## Runtime

| Component        | Technology       | Version    | Notes                          |
|------------------|------------------|------------|--------------------------------|
| <!-- Language -->| <!-- e.g. Node.js --> | <!-- 22.x --> | <!-- LTS -->              |
| <!-- Framework -->| <!-- e.g. Hono --> | <!-- 4.x --> | <!-- why chosen -->         |
| <!-- Database --> | <!-- e.g. SQLite --> | <!-- 3.x --> | <!-- embedded vs hosted --> |
| <!-- Cache -->   | <!-- e.g. Redis --> | <!-- 7.x --> | <!-- if applicable -->      |

## Build Toolchain

| Tool             | Version          | Purpose                        |
|------------------|------------------|--------------------------------|
| <!-- e.g. tsc -->| <!-- 5.x -->     | <!-- TypeScript compilation --> |
| <!-- e.g. cargo --> | <!-- stable --> | <!-- Rust build -->            |
| <!-- e.g. vite -->| <!-- 6.x -->    | <!-- Frontend bundling -->     |

## Infrastructure

| Component          | Technology          | Notes                        |
|--------------------|---------------------|------------------------------|
| IaC                | Terraform           | See `infrastructure.md`      |
| Config Management  | Ansible             | See `infrastructure.md`      |
| CI/CD              | <!-- GitHub Actions / Spacelift --> | <!-- -->          |
| Container Runtime  | <!-- Docker / Podman --> | <!-- -->                  |
| Orchestration      | <!-- AKS / Proxmox / none --> | <!-- -->              |
| DNS                | <!-- Cloudflare / Azure DNS --> | <!-- -->              |
| Monitoring         | <!-- Prometheus / Azure Monitor --> | <!-- -->           |

## External Services / APIs

| Service          | Purpose              | Auth Method                   |
|------------------|----------------------|-------------------------------|
| <!-- e.g. Ollama --> | <!-- LLM inference --> | <!-- API key / none -->  |
| <!-- e.g. NATS --> | <!-- Message bus -->   | <!-- token / mTLS -->      |

## Development Environment

| Tool               | Version / Config     |
|--------------------|----------------------|
| Editor             | VS Code + Copilot    |
| Node.js            | <!-- version -->     |
| Rust               | <!-- stable/nightly -->|
| Python             | <!-- 3.12+ -->       |
| Terraform          | <!-- version -->     |
| Ansible            | <!-- version -->     |
| Docker             | <!-- version -->     |
