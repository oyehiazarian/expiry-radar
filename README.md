# Expiry Radar

> 🚧 **Work in progress**: early development, not ready for use yet.

Self-hosted service that automatically detects what is about to expire (TLS certificates, domains, tokens, licenses) and keeps notifying the right people in Telegram **until the problem is actually fixed**.

## Why

Many outages are caused by something simply expiring: an SSL certificate, a domain, an API token. Usually every type is tracked in a different tool, in a spreadsheet, or not at all. Expiry Radar puts them all in one place and doesn't stop alerting just because someone clicked a button. It verifies the renewal with a new scan.

## How it works

1. Add a target (e.g. TLS host `shop.example.com`) with a policy: warn 30 / 7 / 1 days before, owner, escalation chain.
2. A scanner reads the expiry date.
3. When a threshold is reached, the owner gets a Telegram message with buttons: `Renewed` · `Remind tomorrow` · `Ignore`.
4. No reaction → escalation to the next person.
5. `Renewed` triggers a verification scan. Only a new expiry date closes the alert.

## Architecture

| Component | Responsibility |
|---|---|
| `services/api` | Business logic and data owner: targets, policies, statuses, escalation |
| `services/scanner` | Executes scans (TLS, later domains and tokens) |
| `services/notifier` | Delivers notifications to Telegram, handles buttons |
| PostgreSQL | State |
| Kafka | Async communication between services |

## Tech Stack

Java 25 · Spring Boot 4 · Apache Kafka (KRaft) · PostgreSQL · Flyway · Docker Compose · Telegram Bot API · React + TypeScript *(planned)*

## Run locally

```bash
cp .env.example .env
docker compose -f deploy/docker-compose.yml up -d
mvn verify
```

| Tool | URL |
|---|---|
| Kafka UI | http://localhost:8090 |
| pgAdmin | http://localhost:5050 |

## Roadmap (MVP)

- [x] Project skeleton & local infrastructure
- [ ] Kafka communication between services
- [ ] TLS scanner
- [ ] Targets, policies, expiry statuses
- [ ] Telegram notifications with buttons
- [ ] Escalation & renewal verification
- [ ] Web dashboard
- [ ] Authentication (JWT)
- [ ] Integration tests with Testcontainers

## License

[Apache License 2.0](LICENSE)