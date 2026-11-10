# dockerfiles

Where Dockerfiles for things will end up being stored, built, and pulled from. Yay!

## Repo layout

Pretty simple.

- [image name]/
		- Dockerfile

Don't think too hard about it.

## Releasing

Versions are managed by [release-please](https://github.com/googleapis/release-please).

1. Merge changes to `main` with [conventional commits](https://www.conventionalcommits.org/): `fix:` bumps the patch, `feat:` the minor, `!` the major. Anything else (`chore:`, `ci:`, non-conventional messages) releases nothing.
2. release-please opens (or updates) a release PR bumping `.release-please-manifest.json` and each image's `CHANGELOG.md`.
3. Merging that PR tags `<image>/vX.Y.Z` and pushes `ghcr.io/tredy-ai/<image>:X.Y.Z`.

A new image dir needs an entry in both `release-please-config.json` and `.release-please-manifest.json`.
