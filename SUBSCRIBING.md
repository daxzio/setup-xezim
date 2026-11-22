# Subscribing to new xezim package releases

When [daxzio/xezim-package](https://github.com/daxzio/xezim-package) publishes a GitHub release marked **Latest**, downstream CI can re-run against the new tarball without pinning `version` in `setup-xezim`.

## Flow

1. **xezim-package** finishes **Build** with `latest: true` and sends `repository_dispatch` to this repo (`event_type: xezim-package-published`, payload `{ "tool": "xezim", "tag": "<release tag>" }`).
2. **setup-xezim** (this repo) runs `.github/workflows/announce-package-release.yml` and dispatches to every repo in [`.github/xezim-release-subscribers.json`](.github/xezim-release-subscribers.json) with that repo’s `event_type` (default contract: **`xezim-release`**) and payload:

   ```json
   { "tool": "xezim", "tag": "0.11.7", "source": "daxzio/xezim-package" }
   ```

3. Your repo runs whatever workflows you wire to `repository_dispatch`.

## Add your repo

1. Open a PR on **setup-xezim** that adds an entry to `.github/xezim-release-subscribers.json`:

   ```json
   { "repo": "your-org/your-repo", "event_type": "xezim-release" }
   ```

2. In **your repo**, add a workflow:

   ```yaml
   on:
     repository_dispatch:
       types: [xezim-release]

   jobs:
     retest:
       runs-on: ubuntu-26.04
       steps:
         - run: echo "New package tag: ${{ github.event.client_payload.tag }}"
         # e.g. gh workflow run your-xezim-suite.yml ...
   ```

3. Secrets (org or repo):

   | Repo | Secret | Purpose |
   |------|--------|---------|
   | **xezim-package** | `SETUP_XEZIM_DISPATCH` | PAT allowed to trigger `daxzio/setup-xezim` |
   | **setup-xezim** | `SUBSCRIBER_DISPATCH` | PAT allowed to `repository_dispatch` on every listed subscriber repo |

   If either secret is missing, the corresponding step is skipped (`continue-on-error` on the package side; fan-out exits 0 with a log on the hub).

## Current subscriber

[daxzio/cocotbext-xif](https://github.com/daxzio/cocotbext-xif) is the first entry in [`.github/xezim-release-subscribers.json`](.github/xezim-release-subscribers.json). `test_xif_xezim.yml` runs on `repository_dispatch` type `xezim-release` (and still on push, pull request, and the weekly schedule). PeakRDL-etana will be added the same way once this path is proven.