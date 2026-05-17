# ADR 0001 - Stdlib-first dependency policy

## Status

Accepted

## Context

This library will be imported by every service in the organisation. Every external dependency it introduces becomes a transitive dependency of all those services, compounding across the entire codebase.

That cost has several dimensions:

- **Maintenance.** Each external package must be monitored for security advisories, breaking changes, and licence changes. A vulnerability in a transitive dependency requires coordinated updates across every consuming service, even if they never directly use the affected code.

- **Vendoring.** Teams that vendor dependencies must vendor everything, including transitive dependencies of dependencies. A deep graph makes auditing and reproducing builds harder.

- **Upgrades.** When a dependency introduces a breaking change, every service importing this library is affected. The broader the dependency graph, the more frequently this happens and the harder it is to coordinate.

- **Trust.** Every external package is code written and maintained by a third party. Fewer packages means a smaller attack surface.

At the same time, the standard library does not cover everything. Some problems are not viable to solve without external code - the cost of reimplementing them from scratch would exceed the cost of the dependency itself.

## Decision

The default position for any new addition to this library is: **use the standard library**.

An external dependency may be introduced only when **all three** of the following are true:

1. The standard library does not provide a viable implementation - not merely a convenient one, but a viable one. A solution that is more verbose in stdlib is not sufficient justification.

2. The package is mature and stable. Concretely: it has a stable public API (`v1` or later), is widely adopted, and has a track record that makes future breaking changes unlikely.

3. The decision is recorded in an ADR that explains which alternatives were considered and why the stdlib alternative was insufficient. That ADR is the record of the dependency being approved - not this one.

## Consequences

Every new external dependency requires its own ADR before the code that uses it is written. Opening a PR that adds a `require` line to `go.mod` without a corresponding accepted ADR is grounds for rejection, regardless of how reasonable the dependency appears.

An application importing only one sub-package does not pull in any dependency it does not need. The module graph stays as narrow as what the application actually uses.

Implementing some features in stdlib takes more code than reaching for a library. That code lives in one place and is maintained once. The alternative - a broad dependency graph - distributes maintenance cost invisibly across every consumer.

This decision does not restrict what applications built on this library may depend on. It applies only to what this library itself imports.

## Alternatives considered

**Accept any well-maintained dependency.** Reduces implementation effort. Rejected because the transitive cost across all consuming services outweighs the one-time implementation effort, and dependency sprawl is difficult to reverse once established.

**Use a single framework** (e.g. Go kit, Uber Fx). Provides structure out of the box. Rejected because frameworks impose opinions on application architecture, not just on infrastructure. A framework in a shared library forces those opinions on every team, regardless of their needs.