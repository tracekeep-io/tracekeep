# TraceKeep

**Immutable, cryptographically-verified audit trail for every action in your system.**

TraceKeep records audit events and protects them with HMAC-SHA-256 integrity hashes so you can prove records have not been tampered with — even by insiders.

## What it does

| Capability | Details |
|---|---|
| **Tamper-evident logging** | Every audit record is sealed with an HMAC-SHA-256 integrity hash |
| **Cryptographic verification** | Independently verify any record (or a full export) from the CLI |
| **Immutable storage** | Records are append-only; past events cannot be modified |
| **Batch verification** | Verify an entire JSON export file in one command |
| **REST API** | Ingest events and fetch records via a standard HTTP API |

## How verification works

Each audit record contains an `integrity_hash` field — an HMAC-SHA-256 over the fields:

```
id | action | resource_type | resource_id | created_at
```

The shared secret never leaves your infrastructure. The CLI recomputes the hash locally and compares it against the stored value.

```
Exit 0  →  record is valid (hashes match)
Exit 1  →  record has been tampered with (hashes differ)
```

## Repositories

| Repo | Description |
|---|---|
| [`entrepreneur-platform`](https://github.com/girisenji/entrepreneur-platform) | Mono-repo containing all backend services |
| [`tracekeep-cli`](https://github.com/girisenji/tracekeep-cli) | Command-line interface for verifying and inspecting records |

## Architecture overview

```
Your app  ──POST /v1/events──▶  ingest-api
                                    │
                                    ▼
                               notary-service  (signs & stores records)
                                    │
                                    ▼
                              PostgreSQL 15
                                    │
                              replay-engine   (for audit exports / replay)
```

Event bus: Redpanda (Kafka-compatible)  
Database: PostgreSQL 15  
Analytics: ClickHouse  
Auth: Clerk

## License

[Apache 2.0](LICENSE)