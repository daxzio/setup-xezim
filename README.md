# setup-xezim

This action downloads a prebuilt [xezim](https://github.com/aionhw/xezim) binary from [daxzio/xezim-package](https://github.com/daxzio/xezim-package) and adds it to `PATH`. The job that uses it does not compile xezim.

Supported runners: **Ubuntu 24.04** and **Ubuntu 26.04** (including **`ubuntu-latest`** while that label points at 26.04). By default the action downloads the tarball for the runner’s `VERSION_ID`.

```yaml
name: Check
on: [push, pull_request]
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: daxzio/setup-xezim@v1
      - run: xezim -V
```

Pin a release with `version`. The default is the latest package release.

```yaml
- uses: daxzio/setup-xezim@v1
  with:
    version: "0.11.7"
```

## New package releases

Downstream repos can re-run CI when **xezim-package** marks a release **Latest** without pinning `version` here. The package repo notifies **this** repository; we fan out to registered subscribers. See **[SUBSCRIBING.md](./SUBSCRIBING.md)**.