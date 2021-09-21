About
=====

This repo includes common CI configurations inside the Frontend group. It is
intended to be included into a project to reduce duplication.

How to use
==========

```yaml
include:
  project: 'famedly/company/frontend/ci-templates'
  file: 'gitlab-ci.yml'
```

You can also include specific subsets of files to reduce the amount of reused
jobs.

