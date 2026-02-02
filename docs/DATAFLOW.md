# Data Flow

This document describes how data moves through the system and where
state is owned.

## 1. Request Flow (Read / Write)

```
User
|
v
Frontend Service
|
v
API Service
|
v
Database (Single Source of Truth)
```

- The frontend never accesses the database directly.
- The API layer is the only component allowed to perform reads and writes.
- The database is authoritative for all persistent state.

## 2. State Ownership Model

```
Stateless
├─ frontend
└─ api

Stateful
└─ database
```

- Frontend and API containers do not persist data locally.
- All durable state lives in the database.
- Container restarts do not result in data loss.

## 3. Scaling Behavior

```
API Service (replicas)
├─ api.1
├─ api.2
└─ api.3
```

- Requests may be served by any replica.
- Replicas are interchangeable.
- No replica assumes ownership of user state.

## 4. Consistency Guarantees

- All writes go through the database.
- Database transactions ensure atomicity.
- Application services treat the database as the system of record.

The database is deployed as a single-replica service and acts as the system’s single source of truth. Stateless services are scaled horizontally. This mirrors real-world architectures where databases are centralized or externally managed, while application services scale independently.

## 5. Failure Handling

- If a frontend or API container fails, another replica continues serving traffic.
- If a container restarts, no state recovery is required.
- Database availability determines overall system correctness.

## 6. Design Principles

- No service shares local filesystem state.
- No in-memory state is relied upon for correctness.
- Horizontal scaling is achieved at the service layer.