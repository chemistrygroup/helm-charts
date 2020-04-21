# Helm Charts

This repository hosts all the custom Helm Charts required for the Insight project.

# Dependencies

- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)
- [yamllint](https://yamllint.readthedocs.io/en/stable/)

# Introduction

To be able to use custom chart's as dependencies to other charts and also to have a centralized template schema for deploying services in the project cluster we should have a centralized helm chart repository. For that purpose this repo was built. The repo will be hosted in GitHub pages as described in the [helm repo documentation](https://helm.sh/docs/topics/chart_repository/).

For this purpose this repo will only contain 2 branches:
- master
- gh-pages

Everytime you create a new chart as long as validation ci tasks are addressed a new chart package will be generated. Please point your pull requests allways to `master` branch as the branch `gh-pages` will be updated automatically.

# Development

Description of the most common development taks.

## Creating a new chart

Inside the root of this repo type the following command:

```bash
helm create mychart
```

W.I.P