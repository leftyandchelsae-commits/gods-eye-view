# Automated releases

The `Release Please` workflow watches pushes to `main`. It uses Conventional Commit messages to maintain a release pull request with the next version and generated changelog. Merging that pull request creates a version tag and GitHub Release; it does not publish to npm. The package is marked `private` in `package.json`.

Use `feat:` for a feature (minor version), `fix:` for a bug fix (patch version), and `!` for a breaking change (major version), for example `feat(api)!: change the response format`. Commits without a release-please-recognized type may not trigger a release. `docs:` and `chore:` changes alone normally do not produce a release.

## Initial baseline

The repository has no previous release tags. The manifest therefore starts at `0.1.1`, matching `package.json` and `package-lock.json`. The bootstrap SHA is the current `main` commit when automation was configured, so older development history is excluded from the first generated release notes. Once the first release pull request has been merged, Release Please ignores the bootstrap SHA for subsequent releases.

## GitHub Actions setup

The workflow grants only the `contents`, `issues`, and `pull-requests` permissions needed to update release branches, labels, tags, and releases. In repository **Settings → Actions → General**, allow GitHub Actions to create pull requests if that option is disabled.

The workflow uses `RELEASE_PLEASE_TOKEN` when that Actions secret exists, otherwise it falls back to the built-in `GITHUB_TOKEN`. A token-based release PR is needed if you want the normal `pull_request` CI workflow to run on generated release PRs: GitHub does not start new workflow runs for events caused by `GITHUB_TOKEN`. To enable those checks, add `RELEASE_PLEASE_TOKEN` as a repository Actions secret using a fine-grained token or GitHub App token with repository contents, issues, and pull requests write access. Without it, Release Please can still create/update release PRs (subject to the Actions setting), but those PRs will not automatically trigger CI.
