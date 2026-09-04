# Releasing float-mux

Releases are built by GitHub Actions (`.github/workflows/rust.yml`) when a
version tag is pushed. There is nothing to build or upload by hand.

## Cut a release

1. Bump `version` in `Cargo.toml` (and let `cargo build` update `Cargo.lock`).
2. Commit that: `git commit -am "chore: v1.3.0"`.
3. Tag and push:

   ```bash
   git push origin main
   git tag -a v1.3.0 -m "v1.3.0: short summary"
   git push origin v1.3.0
   ```

The tag must match `v*.*.*`. Keep the tag version and the `Cargo.toml`
version in step.

## What the workflow does

On the tag push it runs three jobs:

- **build** — compiles a release binary for each target:
  `x86_64-unknown-linux-gnu`, `x86_64-unknown-linux-musl` (static),
  `aarch64-unknown-linux-gnu` (the last two via `cross`). Each binary is
  renamed `float-mux-<target>` and uploaded as a workflow artifact.
- **release** — downloads the artifacts and creates a GitHub Release for the
  tag with `generate_release_notes: true`, attaching all three binaries.

Progress: <https://github.com/ErikAllanKincaid/float-mux/actions>. The result:
<https://github.com/ErikAllanKincaid/float-mux/releases>.

## Requirements (one-time)

- Repo Settings -> Actions -> General: "Allow all actions and reusable
  workflows".
- The workflow already declares `permissions: contents: write`, which is what
  `softprops/action-gh-release` needs to publish the Release.

## Not done here

- No crates.io publish. The `float-mux` crate name on crates.io belongs to the
  upstream project (`henktorius/float`); this fork ships only GitHub releases
  and source.
- The separate `push.yml` workflow (fmt / clippy / test on Linux, macOS,
  Windows) runs on every push and pull request to `main`, not on tags.
