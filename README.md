# IBM Telemetry DCO

Signed Developer Certificate of Origins for the IBM Telemetry projects.

## Overview

Per the
[Open Source @ IBM](https://w3.ibm.com/developer/opensource/frequently-asked-questions/faq-contribute/#contribute-faq-approved-cla-dco-list)
guidance, the IBM Telemetry projects use a Developer Certificate of Origin (DCO)
to ensure that intellectual property received through contributions is
acceptable. DCOs are a lightweight alternative to Contributor License Agreements
(CLAs) that serve the same purpose.

The IBM Telemetry projects use the standard Linux-style DCO and as such, IBM
employees do not need IBM Legal review prior to signing.

Instead of including a signoff in every commit, this
[GitHub Action](https://github.com/cla-assistant/github-action) ensures that all
committers have signed the
[DCO](https://github.com/ibm-telemetry/telemetry-dco/blob/main/dco.md) through a
special GitHub pull request comment, as a merge-blocking mechanism.

## Files

This repository primarily houses two files.

**[dco.md](https://github.com/ibm-telemetry/telemetry-dco/blob/main/dco.md)** -
the DCO

**[dco-signatures.json](https://github.com/ibm-telemetry/telemetry-dco/blob/main/dco-signatures.json)** -
the saved consents on a per-repository basis

## Installation

### Step 1) Add a personal access token

Contact a member of the IBM Telemetry team to add a `PERSONAL_ACCESS_TOKEN` for
as a secret in your repository settings.

### Step 2) Add workflow file `.github/workflows/dco.yml`

Use the following in that file and configure:

- `allowlist` - GitHub usernames to bypass the DCO signature requirement.
  Comma-separate the names and wildcard symbols are available, e.g. `bot*` to
  allow all bot users.

The
[cla-assistant/github-action](https://github.com/cla-assistant/github-action)
documentation specifies all inputs, but they should not be modified from below
as we're using the
[ibm-telemetry/telemetry-dco](https://github.com/ibm-telemetry/telemetry-dco)
repository to save signatures across many IBM Telemetry repositories.

<!-- prettier-ignore -->
```yml
name: "DCO Assistant"
on:
  issue_comment:
    types: [created]
  pull_request_target:
    types: [opened, closed, synchronize]

jobs:
  DCO:
    runs-on: ubuntu-latest
    steps:
      - name: "DCO Assistant"
        if: (github.event.comment.body == 'recheck' || github.event.comment.body == 'I have read the DCO document and I hereby sign the DCO.') || github.event_name == 'pull_request_target'
        uses: cla-assistant/github-action@v2.1.2-beta
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          PERSONAL_ACCESS_TOKEN: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
        with:
          path-to-signatures: "dco-signatures.json"
          path-to-document: "https://github.com/ibm-telemetry/telemetry-dco/blob/main/dco.md"
          branch: "main"
          allowlist: dependabot,kodiakhq
          remote-organization-name: ibm-telemetry
          remote-repository-name: telemetry-dco
          create-file-commit-message: "chore: create file to store dco signatures"
          signed-commit-message: "chore: $contributorName has signed the dco in #$pullRequestNo"
          custom-notsigned-prcomment: "Thanks for your submission! We ask that $you sign our [Developer Certificate of Origin](https://github.com/ibm-telemetry/telemetry-dco/blob/main/dco.md) before we can accept your contribution. You can sign the DCO by adding a comment below using this text:"
          custom-pr-sign-comment: "I have read the DCO document and I hereby sign the DCO."
          custom-allsigned-prcomment: "All contributors have signed the DCO."
          lock-pullrequest-aftermerge: false
          use-dco-flag: true
```

## Usage

Once installed, the GitHub Action will prompt contributors on how to sign the
DCO if it's not yet been signed.
