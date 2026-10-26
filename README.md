# CoCoCheck Action

A GitHub Action that checks commit messages against the
[Conventional Commits](https://www.conventionalcommits.org/) specification with
[CoCoCheck](https://github.com/coco-tools/cococheck).

> [!NOTE]
> CoCoCheck does not publish releases yet, so this action builds CoCoCheck from source, by default
> from its `main` branch. The binary is cached per CoCoCheck commit, so it is only rebuilt when that
> commit changes.

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
| `cococheck-ref` | The branch, tag, or commit SHA of CoCoCheck to build.                       | `main`                                      |

## License

Licensed under the [Apache License, Version 2.0](LICENSE).
