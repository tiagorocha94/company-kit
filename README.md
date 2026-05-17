# company-kit

A Go infrastructure library providing production-ready packages with sane defaults.
Built for reliability and availability, with everything configurable and nothing mandatory.

## Philosophy

**Stdlib-first.** External dependencies are only introduced when the benefit clearly
outweighs the maintenance cost. Where external deps are used, the reasoning is
documented in an ADR.

**Opinionated defaults.** Each package comes pre-configured for production. You should
not need to read the source to get a working, reliable client.

**Everything overridable.** Defaults exist to help, not to restrict. Every option can
be changed, and most behaviours can be disabled entirely. Not all projects are the
same — if this library is too restrictive, no one will use it.

**One package, one concern.** An application only pulls in what it uses.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get started and what the review bar
looks like. See [AGENTS.md](AGENTS.md) for commit conventions, branch naming, and the
PR workflow.

## Licence

[MIT](LICENSE)