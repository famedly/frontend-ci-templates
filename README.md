# About

This repo includes common CI configurations inside the Frontend group. It is
intended to be included into a project to reduce duplication.

This repo sets the workflow to run jobs as merge request pipelines by default,
but also tries to run jobs on branches, if no MR exists.

# How to use

```yaml
include:
  project: "famedly/company/frontend/ci-templates"
  file: "all.yml"
```

You can also include specific subsets of files to reduce the amount of reused
jobs.

# Job Descriptions

This section describes the different jobs in the template.

## general/

These jobs should be useful for all Frontend projects.

### conventional_commit_check

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

## dart/

These are dart project specific checks. They are automatically enabled, if a
pubspec.yml is found in the root of the project.

Make sure you define the FLUTTER_IMAGE_TAG variable, so that it doesn't use the
latest image by default.

### dart_analyzer

Runs the dart analyzer, formatter and pub get.

### dart_review_app

Deploys a review app. This requires a build_web job in an earlier stage and the
`FAMEDLY_REVIEW_APP` variable set. Because of GitLab limitations, a default
build_web job is added. Please redefine it as needed.

This also adds support for a review and QA environment:

- The QA environment is created on tags starting with `qa`. There is only one of
  those always accessible under the same url.
- The staging environment is automatically deployed to when a version is tagged
  and also always available under the same url (but distinct from the qa env).
