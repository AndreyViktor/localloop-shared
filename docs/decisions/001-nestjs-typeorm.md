# ADR-001: NestJS + TypeORM for the API

## Status
Accepted

## Date
2024-03-18

## Context
We needed a backend framework for a Node.js API with Clean Architecture. The primary requirements were:
- Built-in dependency injection to support the inward-dependency rule
- First-class TypeScript support
- An ORM that plays well with PostgreSQL and allows raw SQL for migrations
- A large ecosystem (WebSockets, Passport, JWT) to avoid reinventing the wheel

## Decision
We use **NestJS 10** as the API framework and **TypeORM 0.3** as the ORM.

NestJS's module/provider system maps naturally onto Clean Architecture layers: domain interfaces are injected via tokens, and infra repositories are the implementations. Controllers live in presentation and call use cases only.

TypeORM was chosen over Prisma because it allows raw SQL in migrations (needed for PostGIS-specific SQL) and supports repository patterns that align with the domain repository interface pattern.

## Consequences

### Positive
- NestJS DI enforces the dependency inversion principle at compile time
- TypeORM migrations in plain SQL give full control over PostGIS DDL
- Nest ecosystem covers WebSockets, Passport, config, testing — no extra plumbing

### Negative
- TypeORM 0.3 has known rough edges (e.g. relation loading, migration CLI flags) — requires care
- More boilerplate than a simpler Express + Prisma setup

### Neutral
- Each module follows the same 4-layer folder structure (domain/application/infra/presentation)

## Alternatives considered

| Option | Why rejected |
|--------|-------------|
| Express + Prisma | No built-in DI; Prisma migrations don't support raw PostGIS DDL easily |
| Fastify + Drizzle | Less ecosystem maturity for WebSockets + Passport at the time of decision |
