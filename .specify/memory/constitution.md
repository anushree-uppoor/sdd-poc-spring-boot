<!--
Sync Impact Report (machine-readable)
- Version change: unversioned-template → 1.0.0
- Principles: template placeholders → I. Layered Architecture; II. DTO Enforcement;
  III. Database Management; IV. TDD Mandatory (consolidated from six-slot template)
- Added sections: Technology & Stack Constraints; Development Workflow
- Removed sections: none (merged unused principle slots into four explicit principles)
- Templates: ✅ .specify/templates/plan-template.md | ✅ .specify/templates/spec-template.md |
  ✅ .specify/templates/tasks-template.md | ⚠️ .specify/templates/commands/*.md (directory absent)
- Follow-up TODOs: none
-->

# SDD POC Spring Boot Constitution

## Core Principles

### I. Layered Architecture (NON-NEGOTIABLE)

- HTTP adapters MUST live in controllers only; persistence MUST live in repositories
  only; business rules and DTO mapping MUST live in the service layer.
- Dependency flow MUST be **Controller → Service → Repository**; controllers MUST NOT
  call repositories directly or embed business logic.
- **Rationale**: Keeps concerns testable, avoids leaking persistence and domain rules
  into the web layer.

### II. DTO Enforcement (NON-NEGOTIABLE)

- REST APIs MUST NOT expose JPA entities; use **Request** DTOs for input and **Response**
  DTOs for output, with validation on request DTOs as required by the API contract.
- Mapping between entities and DTOs MUST occur in the service layer (or dedicated
  mappers invoked from services), not in controllers.
- **Rationale**: Stable API contracts, controlled serialization, and clear validation
  boundaries.

### III. Database Management (NON-NEGOTIABLE)

- Relational schema changes MUST be delivered **only** through **Flyway** migrations
  under `src/main/resources/db/migration` (versioned scripts); ad-hoc DDL or
  Hibernate-only schema drift for shared environments is not permitted.
- **Rationale**: Reproducible environments, reviewable schema history, and safe rollout.

### IV. TDD Mandatory (NON-NEGOTIABLE)

- Development MUST follow **Test → Fail → Implement → Refactor**; production code MUST
  not be added without a failing test that defines the behavior first.
- **Rationale**: Executable specifications, regression safety, and design feedback before
  implementation hardens.

## Technology & Stack Constraints

- **Runtime**: Java 17+, Spring Boot, Spring Web MVC, Spring Data JPA, Jakarta Validation.
- **Build**: Maven; configuration via `application.yml` (and profiles as needed).
- **Persistence**: H2 unless a feature plan explicitly targets another store; JPA entities
  remain internal to the backend.
- **APIs**: Contract-first; implement endpoints per OpenAPI in
  `design-inputs/api-contracts/` and keep responses aligned with DTO rules above.

## Development Workflow

- Use Spec-Driven Development: specification and plan before implementation; one active
  **spec folder per branch** under `specs/<feature>/`.
- Feature artifacts: `spec.md`, `plan.md`, `tasks.md` maintained per spec-kit workflow;
  implementation MUST satisfy this constitution and the feature spec.
- Code review MUST check layered boundaries, DTO usage, Flyway-only schema changes, and
  evidence of test-first delivery for new behavior.

## Governance

- This constitution **supersedes** conflicting informal practices for this repository.
- **Amendments**: Propose via pull request; update this file, bump **Version** per
  semantic rules (MAJOR: incompatible removals or redefinitions of principles; MINOR:
  new or materially expanded governance; PATCH: clarifications and non-semantic edits),
  and set **Last Amended** to the merge date.
- **Compliance**: Authors and reviewers MUST verify plans, tasks, and code against the
  Core Principles before merge; violations require an explicit constitution amendment or
  an approved, time-boxed exception documented in the feature plan’s complexity
  tracking.
- **Runtime guidance**: Cursor rules under `.cursor/rules/active-rules/` and project
  README elaborate stack and style; they MUST NOT contradict this document.

**Version**: 1.0.0 | **Ratified**: 2026-03-22 | **Last Amended**: 2026-03-22
