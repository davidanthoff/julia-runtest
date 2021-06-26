# julia-runtest Action

This action runs the tests in a Julia package.

## Usage

Julia needs to be installed before this action can run. This can easily be achieved with the [setup-julia](https://github.com/marketplace/actions/setup-julia-environment) action.

And example workflow that uses this action might look like this:

```yaml
name: Run tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        julia-version: ['1.0', '1', 'nightly']
        julia-arch: [x64, x86]
        os: [ubuntu-latest, windows-latest, macOS-latest]
        exclude:
          - os: macOS-latest
            julia-arch: x86

    steps:
      - uses: actions/checkout@v2
      - uses: julia-actions/setup-julia@v1
        with:
          version: ${{ matrix.julia-version }}
          arch: ${{ matrix.julia-arch }}
      - uses: julia-actions/julia-buildpkg@v1
      - uses: julia-actions/julia-runtest@v1
```

You can add this workflow to your repository by placing it in a file called `test.yml` in the folder `.github/workflows/`. [More info here](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions).

### Prefixing the Julia command

In some packages, you may want to prefix the `julia` command with another command, e.g. for running tests of certain graphical libraries with `xvfb-run`.
In that case, you can add an input called `prefix` containing the command that will be inserted to your workflow:

```yaml
      - uses: julia-actions/julia-runtest@v1
        with:
          prefix: xvfb-run
```

If you only want to add this prefix on certain builds, you can [include additional values into a combination](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#example-including-additional-values-into-combinations) of your build matrix, e.g.:

```yaml
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macOS-latest]
        version: ['1.0', '1', 'nightly']
        arch: [x64]
        include:
          - os: ubuntu-latest
            prefix: xvfb-run
    steps:
    # ...
      - uses: julia-actions/julia-runtest@v1
        with:
          prefix: ${{ matrix.prefix }}
    # ...
```

This will add the prefix `xvfb-run` to all builds where the `os` is `ubuntu-latest`.
