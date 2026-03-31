@title: Git for Pipelines: Branches, Merges, and Hooks
@icon: 🌿
@description: Workflows, reviews, signing, and team practices.
@order: 2

# Git as the foundation of CI/CD

**Git** is the version control system that makes diffs reviewable, revertible, and pipeline-triggerable. This lesson does not repeat `init/add/commit` basics; it connects **operations** to **team practices**: protected branches, PR/MR, hooks, and signing.

@section: Essential concepts

* **Commit:** immutable snapshot with parent(s) and metadata (author, date, message).
* **Branch:** a movable pointer to a commit; `main` is usually the integration line.
* **Merge:** combine histories; **rebase** rewrites commits for a linear history (careful on shared branches).
* **Tag:** fixed reference to a commit (releases).

**SHA hashes** identify exact content; pipelines should build from known commits, not movable tags without control.

@section: Pull requests and review

The standard remote workflow:

1. Feature branch from `main`.
2. Atomic commits with clear messages.
3. **Pull request** with description, test checklist, ticket links.
4. Peer review; CI must pass before merge.
5. Merge (squash, merge commit, or rebase per policy).

**CODEOWNERS** (GitHub/GitLab) routes reviews to teams by folder.

@section: Protected branches

Configure the main branch to:

* Require green CI before merge.
* Require approvals (1+).
* Block force-push and accidental deletion.

This reduces incidents from unreviewed changes.

@section: Local and server hooks

* **pre-commit:** local hooks before `git commit` (format, lint, secret scanning).
* **Server-side:** hooks on the Git server or branch rules to validate messages or signatures.

Hooks **do not replace** CI: local environments can skip hooks; the pipeline is the source of truth.

@section: Signing and supply chain

**GPG/SSH signing** of commits and tags verifies identity. For releases, **SLSA** and artifact signing (cosign, sigstore) are increasingly common.

@section: Monorepo vs multirepo

* **Monorepo:** one repo with many services; tools like Bazel, Nx, Turborepo manage selective builds.
* **Multirepo:** one repo per service; simpler CI per repo but more overhead for shared dependencies.

@section: Conflicts and resolution

* Merge conflicts: understand what changed on each side; do not blindly accept “theirs” in production.
* **Blame** and `git bisect` to locate the commit that introduced a failure.

@section: CI integration

CI systems listen for `push` and `pull_request`. Configure:

* Version matrices (Python 3.10, 3.11).
* Dependency caching carefully (invalidate via lockfile).
* Secrets in **repo settings**, not public YAML.

@section: Common mistakes

* Huge commits mixing five features (impossible to revert).
* Committing `.env` or keys (use **git-secrets**, CI scanning).
* Rewriting shared `main` history.

@section: Suggested lab

1. Create a test repo and branch; open a PR with a small change.
2. Configure a branch rule requiring CI (minimal GitHub Actions workflow).
3. Practice `git bisect` with a simulated bug between commits.

@quiz: Why are local pre-commit hooks not enough as the only quality guarantee?
@option: Git does not support hooks
@correct: They can be skipped or disabled; CI must validate in a controlled environment
@option: They only work on Windows
