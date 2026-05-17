# Contributing to company-kit

Thank you for taking the time to contribute.

This file covers the human side of contributing: what to work on, how to
communicate, and what the review bar looks like. The mechanical conventions - 
commit format, branch naming, PR structure, versioning - are in [AGENTS.md](AGENTS.md).
Read that first if you have not already.

---

## What to work on

- **Bugs** - open an issue describing the behaviour, the expected behaviour, and a
  minimal reproduction. If you have a fix ready, link the issue in the PR.
- **New packages** - open an issue or discussion before writing code. New packages
  require an ADR to be proposed and accepted first (see [AGENTS.md](AGENTS.md) §
  "Workflow: ADR before code"). This is not bureaucracy - it is how we avoid
  implementing things twice or in conflicting ways.
- **ADR amendments** - if you disagree with a decision or have new evidence, open a
  PR that updates the ADR with the new context. Changing behaviour without updating
  the ADR is not acceptable.
- **Documentation** - always welcome, no ADR required.

## Ground rules

- **No new external dependency without an ADR.** See
  [ADR 0001](docs/adr/0001-stdlib-first.md). If you need a dependency, write the ADR
  first and get it merged before writing any code that uses it.
- **Tests are not optional.** Every package must have tests. Every behaviour change
  must have a test that would have caught the bug or validated the feature.
- **Public API changes need a deprecation path.** Before v1.0.0 this is relaxed, but
  try not to break consumers without a clear migration.

## Running the tests

```bash
# All packages
go test -race ./...

# Single package
go test -race ./httpclient/...

# With coverage
go test -race -coverprofile=coverage.txt ./...
go tool cover -html=coverage.txt -o coverage.html
```

Integration tests require a live database or Redis and are gated behind a build tag:

```bash
go test -race -tags integration ./...
```

## Review expectations

- PRs are reviewed within a reasonable time. If a PR has had no activity for a week,
  ping the thread.
- A review is a conversation, not a verdict. Explain your reasoning; expect the same
  in return.
- The bar for merging is: correct, tested, documented, and consistent with the
  existing ADRs. Style preferences that are not enforced by `go vet` or `gofmt` are
  not blocking.

## Licence

By contributing you agree that your contributions will be licensed under the
[MIT Licence](LICENSE) that covers this project.