# About

This repo includes common CI workflows for the Instant-Messaging team. It might
still include some of the old gitlab templates, but those aren't used anymore.

# How to use

Include the workflows you want to use in your projects main workflow, for
example:

```yaml
name: "All the app specific jobs"

on:
  push:
    branches:
      - main
  pull_request:
  merge_group:

concurrency:
  group: ${{ github.ref }}
  cancel-in-progress: true

jobs:
  build_web:
    runs-on: ubuntu-latest
    container:
      image: ghcr.io/famedly/container-image-flutter/flutter:latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      # You may need to pull this repo instead using a checkout action
      - name: Enable pulling private dependencies
        uses: famedly/frontend-ci-templates/.github/actions/dart-prepare
        with:
          ssh_key: "${{ secrets.CI_SSH_PRIVATE_KEY }}"
      - name: Do dart stuff
        run: |
          ./scripts/prepare-web.sh
          apk add yq
  review_app:
    needs: build_web
    secrets: inherit
    uses: famedly/frontend-ci-templates/.github/workflows/review-app.yml
    with:
      projectname: "app"
      pr: ${{ github.events.pull_request.number }}

  dart:
    permissions:
      contents: read
    uses: famedly/frontend-ci-templates/.github/workflows/dart.yml
    with:
      env_file: ".github/workflows/versions.env"
    secrets:
      ssh_key: "${{ secrets.CI_SSH_PRIVATE_KEY }}"

  general:
    permissions:
      contents: read
    uses: famedly/frontend-ci-templates/.github/workflows/general.yml
```

You can also include specific subsets of files to reduce the amount of reused
jobs.

# Workflow Descriptions

This section describes the different workflows and jobs in the template.

## general/

These jobs should be useful for all Frontend projects.

### conventional_commits

This job checks new commits on a merge request, if they follow the conventional
commit specification.

This means commit with the following prefixes in the summary line are allowed:

- builds
- chore
- ci
- docs
- feat
- fix
- refactor
- style
- test
- revert

## dart/

These are dart project specific checks.

Make sure you define the flutter_version and dart_version in an env file and pass it, so that CI
knows what container to use.

### dart_analyzer

Runs the dart analyzer, formatter and pub get.

## review_app

Allows deploying a review app to our testservers. This is currently just done on
PRs and they are not cleaned up.
