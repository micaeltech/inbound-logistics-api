# Inbound Logistics API

Centralized RESTful API (TMS) for managing supply chain operations and road freight transport (Suppliers -> Carrier -> Distribution Centers).

> **Project Status:** In development — Phase 1: Business Rules Specification & Relational Database Modeling.

## Technical Documentation (`/docs`)

1. **[Business Rules & Domain](docs/01-business-rules.md)** — Domain actors, operational constraints, and freight state machine.
2. **[Database Modeling](docs/02-database-modeling.md)** — Data dictionary, keys (PK/FK), normalization (3NF), and ER diagrams.
3. **[Architecture Decisions (ADRs)](docs/03-architecture-decisions.md)** — Technical rationale behind stack, database engine, and concurrency choices.
4. **[Pseudocode & Transactional Logic](docs/04-pseudocode.md)** — Step-by-step algorithmic logic for freight allocation, maintenance locks, and state transitions.

## Project Architecture

`src/main/java/com/micael/logistics/`

- `config/` — Global configurations (OpenAPI/Swagger, Beans)
- `controller/` — REST entry layer (Endpoints and contract validation)
- `domain/` — JPA entities and state machine enums
- `dto/` — Data Transfer Objects (Request / Response)
- `exception/` — Custom domain exceptions and GlobalExceptionHandler (RFC 7807)
- `repository/` — Data persistence and queries via Spring Data JPA
- `security/` — Stateless authentication and authorization (JWT)
- `service/` — Business logic, validations, and transactional boundaries

## Tech Stack

- **Language & Framework:** Java 17, Spring Boot 3, Spring Data JPA, Spring Validation, Spring Security
- **Database & Versioning:** PostgreSQL, Flyway Migrations, Redis, MongoDB
- **Infrastructure & Testing:** Docker, Docker Compose, JUnit 5, Mockito
