# AGENTS.md

Working agreement for humans and AI agents contributing to this repository.
Read this before writing any code or opening any PR.

---

## Core principle: atomic commits

Every commit in this repository represents one complete, self-contained unit of work.
A commit should be correct and meaningful on its own — not a checkpoint, not a
work-in-progress snapshot, not a "add files" blob.

**A commit is atomic when:**
- It does exactly one thing.
- Its title describes that one thing completely.
- `go build ./...` and `go test ./...` pass on that commit alone.
- Reverting it leaves the repository in a valid prior state.

**A commit is not atomic when:**
- Its message contains "and" describing two separate concerns.
- It mixes documentation changes with code changes unless the doc change exists solely
  to describe that code change.
- It contains files unrelated to the stated change.

---

## Commit message format

```
<type>(<scope>): <imperative short description>

<optional body — explain why, not what>

<optional footer — refs, breaking changes>
```

**Types:**

| Type | Use for |
|---|---|
| `feat` | A new package, type, function, or behaviour |
| `fix` | A bug fix |
| `adr` | Adding or amending an Architecture Decision Record |
| `docs` | Documentation only (README, comments, examples) |
| `chore` | Tooling, CI, go.mod, .gitignore — no behaviour change |
| `test` | Adding or fixing tests with no production code change |
| `refactor` | Code restructuring with no behaviour change |
| `breaking` | A change that breaks an existing public API (use sparingly) |

**Scope** is the package or area affected: `httpclient`, `internal/retry`,
`database/postgres`, `health`, `docs/adr`, etc.

**Examples:**

```
chore: initialise repository with README and AGENTS.md

adr(docs/adr): add ADR 0001 — stdlib-first dependency policy

docs(README): add ADR 0001 to decision table

chore: add go.mod and .gitignore

feat(internal/retry): implement exponential backoff with full jitter

docs(internal/retry): add doc.go and package README

test(internal/retry): add unit tests and race condition coverage
```

---

## Pull request workflow

### One PR = one feature or decision

A PR maps to a single feature, package, or design decision. It groups all the atomic
commits required to deliver that unit of work. Its title is the feature name, written
in the imperative: "Add internal retry package", "Add ADR 0003 — retry jitter strategy".

PRs are created from a feature branch off `main` and merged back into `main` via
the GitHub UI. Direct pushes to `main` are not allowed.

### Branch naming

```
adr/<number>-<slug>          adr/0003-retry-jitter
feat/<scope>-<slug>          feat/internal-retry
fix/<scope>-<slug>           fix/httpclient-timeout-leak
docs/<slug>                  docs/contributing-guide
chore/<slug>                 chore/go-mod-init
```

### PR description template

Every PR description must include:

```markdown
## What
<one paragraph: what this PR adds or changes>

## Why
<link to the ADR(s) that justify this change, or a brief rationale if no ADR applies>

## Commits
<paste the git log --oneline for this branch — lets reviewers verify atomicity>

## Checklist
- [ ] Each commit is atomic and passes `go build ./...` and `go test -race ./...`
- [ ] CHANGELOG.md is updated under [Unreleased]
- [ ] README.md is updated if a new package or ADR was added
- [ ] No unrelated files are touched
```

### CHANGELOG discipline

Every PR that adds behaviour, fixes a bug, or makes a decision **must** update
`CHANGELOG.md` under the `[Unreleased]` section before the PR is merged.

The entry belongs in the commit that introduces the relevant change — not in a
separate "update changelog" commit at the end of the PR. The CHANGELOG update is
part of the atomic commit it documents.

```
feat(internal/retry): implement exponential backoff with full jitter

Adds internal/retry with configurable attempts, base delay, cap, and
multiplier. Full jitter is applied as per ADR 0003.

Updates CHANGELOG.md [Unreleased] > Added.
```

---

## Versioning

Versions follow [Semantic Versioning](https://semver.org): `MAJOR.MINOR.PATCH`.

| Commit type | Version segment affected |
|---|---|
| `feat` | MINOR (`0.x.0`) |
| `fix` | PATCH (`0.0.x`) |
| `breaking` | MAJOR — disabled until v1.0.0; do not use before then |
| `adr`, `docs`, `chore`, `test`, `refactor` | none |

**Until v1.0.0**, MAJOR stays at `0` regardless of the nature of changes. Breaking
changes before v1.0.0 increment MINOR, not MAJOR. This is standard semver convention
for pre-release libraries.

Versions are **not bumped on every PR.** All merged PRs accumulate under `[Unreleased]`
in the CHANGELOG. A version is cut only when explicitly requested, at which point:

1. `[Unreleased]` in `CHANGELOG.md` is renamed to the new version with its date.
2. A new empty `[Unreleased]` section is added above it.
3. The `go.mod` version tag (if applicable) is updated.
4. A git tag `vX.Y.Z` is created and pushed.

The commit for a version cut looks like:

```
chore(release): cut v0.2.0
```

---

## Workflow: ADR before code

No package is implemented without a corresponding ADR being merged first.
The sequence for any new package or significant decision is:

```
1. adr(docs/adr): add ADR <N> — <decision title>          [updates CHANGELOG]
2. docs(README): add ADR <N> to decision table
3. chore(<scope>): add go.mod / directory scaffold if needed
4. feat(<scope>): implement <package>                      [updates CHANGELOG]
5. test(<scope>): add tests
6. docs(<scope>): add doc.go, package README, example_test.go
```

Steps 1 and 2 must be separate commits. The ADR is the record; the README update is
documentation of the record. They are different concerns.

Steps 4, 5, and 6 may be merged into fewer commits if the package is small, but never
combined with step 1 or 2.

---

## README.md is a live document

`README.md` is updated incrementally. Each ADR commit is followed immediately by a
`docs(README)` commit that adds the ADR to the decision table. Each implemented package
is followed by a `docs(README)` commit that adds it to the package layout section.

The README always reflects what exists in the repository at that commit, nothing more.

---

## Rules for AI agents

> The human-facing contribution guide is in [CONTRIBUTING.md](CONTRIBUTING.md).
> That file covers what to work on, how to communicate, and the review bar.
> This section covers the mechanical rules that apply equally to humans and agents.

If you are an AI agent working in this repository:

1. **Read this file first.** Before generating any file or command.

2. **One commit, one thing.** Do not bundle. If asked to "add the httpclient package",
   the correct output is a sequence of commits, not one large commit.

3. **Propose before implementing.** If a task requires a new external dependency or a
   new package that lacks an ADR, stop and surface the gap. Do not add the dependency
   and write an ADR retroactively.

4. **Check the ADR table in README.md.** Before implementing any package, verify the
   corresponding ADR exists and is accepted. If it does not exist, the first output
   must be the ADR commit, not the implementation.

5. **Never touch unrelated files.** A commit for `internal/retry` must not modify
   `httpclient/` or `README.md` unless the README change is solely to document the
   retry package. Exception: CHANGELOG.md is always updated in the same commit as the
   change it documents.

6. **Always verify build and tests pass** on each commit before moving to the next.
   `go build ./...` and `go test -race ./...` are the baseline.

7. **Commit messages are not optional.** Every commit needs a message in the format
   above. "update files" or "changes" are not acceptable.

8. **Update CHANGELOG.md in the same commit as the change.** Do not add a separate
   "update changelog" commit. The changelog entry is part of the atomic unit.

9. **Branch from main, never from another feature branch.**

10. **PR description must follow the template.** Fill every section. Paste the
    `git log --oneline` of the branch commits under "Commits".

---

## What good looks like

### Git log for a PR adding the `internal/retry` package

```
adr(docs/adr): add ADR 0003 — full jitter for retry backoff
docs(README): add ADR 0003 to decision table
feat(internal/retry): implement exponential backoff with full jitter
test(internal/retry): add unit and race condition tests
docs(internal/retry): add doc.go with ADR reference
```

Five commits. Each correct on its own. Each revertable without breaking anything else.
CHANGELOG.md is updated in the `feat` commit, not as a sixth commit.

### What the CHANGELOG entry looks like after that PR merges

```markdown
## [Unreleased]

### Added
- `internal/retry`: exponential backoff with full jitter (ADR 0003)
```

---

## GitHub configuration

To enforce this workflow, apply the following settings in the repository.

### Branch protection for `main`

`Settings → Branches → Add branch ruleset`

| Setting | Value |
|---|---|
| Target branches | `main` |
| Restrict deletions | ✅ |
| Require linear history | ✅ |
| Require a pull request before merging | ✅ |
| Required approvals | 1 (adjust to team size) |
| Dismiss stale reviews on new commits | ✅ |
| Require review from code owners | ✅ (once CODEOWNERS is added) |
| Require status checks to pass | ✅ |
| Required status checks | `build`, `test` (add once CI is configured) |
| Block force pushes | ✅ |

### Merge strategy

`Settings → General → Pull Requests`

| Setting | Value |
|---|---|
| Allow merge commits | ❌ |
| Allow squash merging | ❌ |
| Allow rebase merging | ✅ |
| Default to PR title for merge commit | — (n/a, only rebase) |
| Automatically delete head branches | ✅ |

Rebase-only merge preserves the atomic commit history on `main` exactly as it
appeared on the feature branch. Squash destroys atomicity; merge commits pollute the
log with noise.

### PR title enforcement (optional but recommended)

Install the [Semantic Pull Requests](https://github.com/apps/semantic-pull-requests)
GitHub App. Configure it to require that the PR title matches the conventional commit
format. This ensures the PR title (which becomes the branch's first line in the log)
is consistent with the commit convention.