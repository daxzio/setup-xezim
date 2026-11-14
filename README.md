# setup-xezim

This action downloads a prebuilt [xezim](https://github.com/aionhw/xezim) binary from [daxzio/xezim-package](https://github.com/daxzio/xezim-package) and adds it to `PATH`. The job that uses it does not compile xezim.

Supported runner: `ubuntu-24.04` / `ubuntu-latest`.

```yaml
name: Check
on: [push, pull_request]
jobs:
  check:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@v4
      - uses: daxzio/setup-xezim@v1
      - run: xezim -V
```

Pin a release with `version`. The default is the latest package release.

```yaml
- uses: daxzio/setup-xezim@v1
  with:
    version: "0.11.6"
```
