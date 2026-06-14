# Versioning — opsta reusable GitHub Actions workflows

These reusable workflows follow **Semantic Versioning** with a **moving major tag**
(the `actions/checkout@v4` model).

## How to consume

```yaml
# Recommended for app repos: track the major — auto-receives non-breaking fixes/features.
uses: opsta/.github/.github/workflows/build-container-image-kaniko.yaml@v1

# Production-critical / audited: pin an exact release (or a commit SHA).
uses: opsta/.github/.github/workflows/build-container-image-kaniko.yaml@v1.3.0
uses: opsta/.github/.github/workflows/build-container-image-kaniko.yaml@<full-sha>
```

- `@v1` — moving tag, always the latest `v1.x.y`. **Default for app repos.**
- `@v1.3.0` — immutable, exact.
- `@<sha>` — immutable + supply-chain hardened.
- `@main` — **development only**, never in app repos.

## What bumps which number

The "interface" of a reusable workflow is its `inputs`, `outputs`, `secrets`, and
required runner/permissions.

| Bump | When |
|------|------|
| **MAJOR** (`v1`→`v2`) | Remove/rename an input/output/secret; make an optional input required; remove/flip a default so behavior changes; change required runner or permissions. |
| **MINOR** (`v1.2`→`v1.3`) | Add a new input/output/workflow; new optional capability — all backward-compatible. |
| **PATCH** (`v1.2.3`→`v1.2.4`) | Bug fix; bump a pinned tool image in `image-versions`; docs — no interface change. |

A MAJOR bump is an explicit opt-in for consumers (they move `@v1` → `@v2`).

## Release process

1. Land changes on `main` (CI green).
2. Create an annotated semver tag and push it:
   ```bash
   git tag -a v1.3.0 -m "v1.3.0" && git push origin v1.3.0
   ```
3. The **Release** workflow (`.github/workflows/release-major-tag.yaml`) then:
   - force-updates the moving major tag (`v1`) to that commit, and
   - publishes a GitHub Release with auto-generated notes.

## Support policy

- The latest major is fully supported.
- Security fixes are backported to the previous major (`vN-1`) for a deprecation window.
- Breaking changes are announced in the release notes for the new major.
