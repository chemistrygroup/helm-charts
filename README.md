# Helm Charts

This repository hosts all the custom Helm Charts required for the Insight project.

![Lint Test And Release Charts](https://github.com/chemistrygroup/helm-charts/workflows/Lint%20Test%20And%20Release%20Charts/badge.svg?branch=master)

## Dependencies

- [Docker](https://www.docker.com/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)
- [KIND](https://kind.sigs.k8s.io/)
- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)
- [Helm Test](https://github.com/helm/chart-testing)

## Uses

- [Helm Test](https://github.com/helm/chart-testing)
- [Helm Test Action](https://github.com/helm/chart-testing-action)
- [Helm Releaser Action](https://github.com/helm/chart-releaser-action)

## Introduction

To be able to use custom chart's as dependencies to other charts and also to have a centralized template schema for deploying services in the project cluster we should have a centralized helm chart repository. For that purpose this repo was built. The repo will be hosted in GitHub pages as described in the [helm repo documentation](https://helm.sh/docs/topics/chart_repository/).

For this purpose this repo will only contain 2 branches:
- master
- gh-pages

Everytime you create a new chart as long as validation ci tasks are addressed a new chart package will be generated. Please point your pull requests allways to `master` branch as the branch `gh-pages` will be updated automatically.

## Development

Description of the most common development taks.

### Creating a new chart

Inside the root of this repo type the following command:

```bash
helm create charts/<chart-name>
```

### Chart Dependencies

If your chart contains dependencies from additional repositories you must add those repos to the file `ct.yaml`, example:

```yaml
...
chart-repos:
 - bitnami=https://charts.bitnami.com
...
```

### Linting the chart

By using helm's Chart Testing tool running inside a docker container we perform a set of recomend tests for our chart. 
Type the following in your command line in the root of the repo:

```bash
docker run -it -v $PWD:/code quay.io/helmpack/chart-testing:v3.0.0-rc.1 sh
```

Then when inside the container `cd` into `/code` and type the following command:

```sh
cd /code
ct lint --config=ct.yaml --validate-maintainers=false
```

### Testing the chart

To test this chart whithout needing to install it on a real kubernetes cluster, we can setup a new cluster with `KIND` and use it with the `ct`tool to test the release of the chart. To accomplish this first we need to create a new `KIND` cluster, type the following in your terminal in the root of this repo:

#### Start KIND Cluster
```bash
kind create cluster --config cluster-config.yaml
```

When the command ends your cluster should be up and running and `kubectl` should already have been setup to access it, to confirm this type the following:

```bash
kubectl get nodes
```

You should see the following output:

```bash
NAME                 STATUS   ROLES    AGE     VERSION
kind-control-plane   Ready    master   8m3s    v1.15.11
kind-worker          Ready    <none>   7m30s   v1.15.11
kind-worker2         Ready    <none>   7m30s   v1.15.11
kind-worker3         Ready    <none>   7m30s   v1.15.11
```

#### Start CT Dockerized tool

To be able to access our cluster inside the docker container that contains the `ct` tool we need to expose our host network to the container and share the kubectl config folder, open your terminal in the root of this repo and type the following command.

```bash
docker run -it -v $PWD:/code -v ~/.kube/:/root/.kube/ --net=host quay.io/helmpack/chart-testing:v3.0.0-rc.1 sh
```

The previous command should boot a docker container that is running in the host network and opens a terminal console inside it, the expected output should be the following:

```bash
/ #_ 
```

To verify if we have access to our KIND cluster type the following command:

```bash
kubectl get nodes
```

You should see the following output:

```bash
NAME                 STATUS   ROLES    AGE     VERSION
kind-control-plane   Ready    master   8m3s    v1.15.11
kind-worker          Ready    <none>   7m30s   v1.15.11
kind-worker2         Ready    <none>   7m30s   v1.15.11
kind-worker3         Ready    <none>   7m30s   v1.15.11
```

#### Test the chart

Now that we are in a shell running inside our docker container we only have to do the following to test our chart and see if it is working as expected, type the following set of commands inside the docker shell:

```sh
cd /code
ct install --config=ct.yaml --validate-maintainers=off --debug=true
```

If the command exits with other than 0 means that our chart is not working as expected and we have to debug the issues. Look at the log's for tips.


### Destroy testing environemnt

If a previous installation failed and you would like to retry it, the safes-te way to do it is to destroy the kind cluster and create it again. To do that you should type the following command:

```bash
kind delete cluster
```

## Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

## License

[License](LICENSE.md)