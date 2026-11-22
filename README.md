# CoCoCheck Action

A GitHub Action that checks commit messages against the
[Conventional Commits](https://www.conventionalcommits.org/) specification with
[CoCoCheck](https://github.com/coco-tools/cococheck).

## Usage

```yaml
on:
  push:
    branches:
      - main
  pull_request:

permissions:
  contents: read

jobs:
  cococheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7.0.1
        with:
          # Fetch the full history, so that the commits to check can be found.
          fetch-depth: 0
      - uses: coco-tools/cococheck-action@main
```

For a pull request, the action checks the commits between the base and the head commit of the pull
request. It uses the head commit instead of the merge commit that GitHub checks out. For a push, it
checks the pushed commits. For the first push of a new branch, there is no previous commit, so all
commits up to the pushed one are checked.

CoCoCheck reads its configuration from the first of `cococheck.yml`, `cococheck.yaml`,
`.cococheck.yml`, or `.cococheck.yaml` that exists in the root of the repository. See the
[configuration reference](https://github.com/coco-tools/cococheck/blob/main/docs/configuration.md).

The action fails if the repository is a shallow clone, because then the commits to check may be
missing.

## Inputs

| Input           | Description                                                                 | Default                                     |
|-----------------|-----------------------------------------------------------------------------|---------------------------------------------|
| `from`          | Check only the commits after this one. It is not checked itself.            | Base commit of the pull request, or the previous head of the pushed branch |
| `to`            | The last commit to check. It is checked itself.                             | Head commit of the pull request, or the pushed commit |
| `config`        | Path to the config file.                                                    | `cococheck.yml`, `cococheck.yaml`, `.cococheck.yml`, or `.cococheck.yaml` |
| `deny-warnings` | Fail the checks for commits that only violate rules with severity `warning`. | `false`                                     |
| `args`          | Additional command-line arguments for CoCoCheck, separated by whitespace.   |                                             |
| `version`       | The version of CoCoCheck, or `latest` for the latest release.               | A fixed version for each release of the action |
| `cococheck-ref` | The branch, tag, or commit SHA of CoCoCheck to build from source. If it is set, `version` is ignored. |                                 |

## CoCoCheck version

Each release of the action uses a fixed version of CoCoCheck by default, so that a new release of
CoCoCheck does not suddenly fail your checks. A new version of CoCoCheck is adopted with a new
release of the action. Choose another version with the `version` input:

```yaml
      - uses: coco-tools/cococheck-action@main
        with:
          version: 0.1.0 # Or `latest` for the latest release of CoCoCheck.
```

The action downloads the prebuilt binary of the release and verifies its checksum. Binaries are
available for Linux, macOS, and Windows on x64 and ARM64. On other platforms, the action builds
CoCoCheck from source, which installs Rust and takes a few minutes.

To build an unreleased version of CoCoCheck, set `cococheck-ref` to a branch, tag, or commit SHA.
If `cococheck-ref` is set, it takes precedence and `version` is ignored: CoCoCheck is always built
from source, even if the ref is the tag of a release. Binaries built from source are cached per
commit.

## License

Licensed under the [Apache License, Version 2.0](LICENSE).
