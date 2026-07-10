# Solo Mode — the Spec for Single-Person Projects

> 中文版本：[`solo-mode.zh-CN.md`](./solo-mode.zh-CN.md)

The main spec ([`STANDARDS.md`](../STANDARDS.md)) assumes a team: code
review, branch protection, CI/CD, preview deployments, a cloud
database. A large share of AI-native development happens somewhere
else entirely — **one product owner pairing with an AI agent**, on a
local-first app, with no pipeline and no reviewers.

Solo Mode is the same discipline re-derived for that environment. It
keeps every rule that is about *AI behavior* and replaces every rule
that is about *coordinating humans* with its single-player equivalent.

Assumed environment:

- One human (product decisions, acceptance testing) + AI agents (all
  implementation).
- Local-first or client-only product (mobile app, desktop tool, CLI);
  data lives on the user's device.
- No CI/CD, no staging, no branch protection. Possibly, at the start,
  no remote at all.

## The ten rules

### 1. Edit freely, commit deliberately

The agent may rewrite files as often as it likes, but a commit is the
smallest **independently verifiable** slice of a feature — something
that runs, renders, or passes a test on its own. No `wip`, `update`,
or `save point` commits; no bundling unrelated changes.

In a team this rule protects reviewers. Solo, it protects the only
history you will ever have: when something breaks two weeks later,
`git bisect` over verifiable slices is your code review.

### 2. Work on `main` by default

Feature branches exist to serialize review and parallelize people.
With one person and no review gate they are pure overhead. Commit
small, verified slices directly to `main`.

Open a branch in exactly two cases, and name it `exp/<topic>`:

- a large refactor that will leave the tree broken for a while;
- an experiment you genuinely might throw away.

Merge it back (or delete it) as soon as the answer is known. An
`exp/` branch that survives the week is a decision you are avoiding.

### 3. One feature = one commit trail + one progress update

Teams use PRs to mark a feature's boundary. Solo, the boundary lives
in a progress file (e.g. `PROGRESS.md`): when the feature's commits
land, check the task off and note anything left over. Release points
are annotated tags on `main` (e.g. `phase-1`) — the tag is your
release branch.

### 4. No CI means the pre-commit gate *is* the CI

Everything a CI pipeline would catch must be caught before each
commit: type check, linter, existing tests, and — for UI work — the
target screen actually rendering. **If the gate fails, the commit does
not happen. No exceptions.** It is the only quality gate in the whole
system; break it once and it no longer exists.

### 5. Ship only from a tagged commit on `main`

Anything that leaves your machine — an app-store build, a TestFlight
upload, a binary sent to a friend — is built from a tagged commit on
`main`, never from whatever happens to be in the working tree.
"Production deploys only from `main`" survives solo; it just loses
the deploy bot.

### 6. A local database deserves migration discipline

No cloud database does not mean no migrations. Schema changes go
through versioned migration scripts; never mutate tables in place.
Every migration must answer "what happens to existing data?" — the
user's device is the only copy, and there is no ops team and no
snapshot to restore when a migration eats it.

### 7. Aggregate schema changes

Design the feature's data model first, then write **one** migration
for it. Ten one-column migrations are ten chances to lose user data
and zero added value. (Unchanged from the main spec — if anything it
matters more when every migration runs unattended on end-user
devices.)

### 8. Search before generating

Before writing anything new, search the project for an existing
implementation: components, utilities, types, i18n keys, style
tokens. **Prefer editing an existing file to creating a new one;
prefer reuse to rewrite.** Parallel implementations are the most
expensive habit an AI pair has, and there is no reviewer to catch the
second copy. (Unchanged from the main spec.)

### 9. Bounded retries; escalate honestly

Three failed attempts at the same problem means stop: write down the
symptom, what was tried, and your current hypothesis in the progress
file's Blockers section, then ask the human. No infinite fix loops
burning tokens; no "solving" a problem by deleting the failing test
or mocking the error away. (Unchanged from the main spec — solo, the
human you escalate to is also the product owner, so this is cheap.)

### 10. No remote = no backup

The biggest risk in a solo project is not a merge conflict — it is a
disk. As soon as the repo exists, add a private remote (a free
private GitHub repo used purely as a backup target is enough), or at
minimum archive the repo at every milestone. And **never delete a
file that has not been committed somewhere** — for uncommitted work,
`rm` is irreversible.

## What changed from the main spec, and why

| Main spec | Solo Mode | Why |
| --- | --- | --- |
| `ai/<feature>` branches, PRs, squash merge | `main` by default; `exp/<topic>` only for refactors/experiments | No reviewers to serialize; branches are pure overhead |
| No `on: push` full deploys; PR-triggered CI | Rule 4: the pre-commit gate replaces CI | There is no pipeline; the gate moves to commit time |
| Direct connections for migrations, pools for runtime, Neon branching | Rules 6–7: versioned, aggregated migrations for the local DB | No cloud DB, but migration discipline transfers intact |
| Event-driven, not polling | Folded into rule 9 | Solo, its essence is "don't burn tokens idling" |
| Search before generating; bounded retries | Kept verbatim (rules 8–9) | AI-behavior rules are team-size independent |
| — | New rule 10 (backup) | The main spec gets backups for free from GitHub; solo projects often start with no remote at all |

## When to graduate back

Solo Mode is a starting point, not an identity. The moment a second
contributor appears — or a CI pipeline, or a staging environment —
switch the affected rules back to the main spec. The transitions are
mechanical: rule 2 becomes branching, rule 3 becomes PRs, rule 4
becomes CI, rule 5 becomes deploy-from-`main`.
