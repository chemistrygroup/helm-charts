# Service

This is a template Helm Chart for deploying services into our cluster. This template contains all the required resources to successfully deploy a service into our kuberentes cluster.

## Dependencies

- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)

## Installation

Add the Chemistry Group Helm repository:

```bash
helm repo add chemistry https://chemistrygroup.github.io/helm-charts
```

Install the service using the chart:


```bash
helm upgrade -i service chemistry/service
```

## Repo Structure

The following dir tree represents the repo directory structure:

```bash
- templates # folder were the k8s resources templates are stored
-- _helpers.tpl # helper's/utils file for the generation of shared helm values
-- configmap.yaml # template for configmap generation
-- deployment.yaml # template for deploying the service as stateless service of type deployment
-- hpa.yaml # template for setting up the Horizontal Pod Scalling policies
-- rbac.yaml # template for creating the ClusterRole/Role and the respective ClusterRoleBinding/RoleBinding
-- sa.yaml # template for creating a service account
-- secret.yaml # template for creating secrets
-- service.yaml # template for creating services
- Chart.yaml # chart description file
- values.yaml # sample values yaml file 
- values.schema.yaml # yaml json schema used to validate the format of values.yaml
```


## Configuration

The following tables lists the configurable parameters of the Service
chart and their default values.

| Parameter                                         | Default                                              | Description
| -----------------------------------------------   | ---------------------------------------------------- | ---
| `image.repository`                                | `docker.io/fluxcd/helm-operator`                     | Image repository
| `image.tag`                                       | `{{ version }}`                                      | Image tag
| `replicaCount`                                    | `1`                                                  | Number of Helm Operator pods to deploy, more than one is not desirable
| `image.pullPolicy`                                | `IfNotPresent`                                       | Image pull policy
| `image.pullSecret`                                | `None`                                               | Image pull secret
| `resources.requests.cpu`                          | `50m`                                                | CPU resource requests for the deployment
| `resources.requests.memory`                       | `64Mi`                                               | Memory resource requests for the deployment
| `resources.requests.cpu`                          | `None`                                               | CPU resource limits for the deployment
| `resources.limits.memory`                         | `None`                                               | Memory resource limits for the deployment
| `nodeSelector`                                    | `{}`                                                 | Node Selector properties for the deployment
| `tolerations`                                     | `[]`                                                 | Tolerations properties for the deployment
| `affinity`                                        | `{}`                                                 | Affinity properties for the deployment
| `extraEnvs`                                       | `[]`                                                 | Extra environment variables for the Helm Operator pod(s)
| `podAnnotations`                                  | `{}`                                                 | Additional pod annotations
| `podLabels`                                       | `{}`                                                 | Additional pod labels
| `rbac.create`                                     | `true`                                               | If `true`, create and use RBAC resources
| `rbac.pspEnabled`                                 | `false`                                              | If `true`, create and use a restricted pod security policy for Helm Operator pod(s)
| `serviceAccount.create`                           | `true`                                               | If `true`, create a new service account
| `serviceAccount.annotations`                      | `{}`                                                 | Additional Service Account annotations
| `serviceAccount.name`                             | `flux`                                               | Service account to be used
| `clusterRole.create`                              | `true`                                               | If `false`, Helm Operator will be restricted to the namespace where is deployed
| `createCRD`                                       | `false`                                              | Install the `HelmRelease` CRD. Setting this value only has effect for Helm 2, as Helm 3 uses `--skip-crds` and will skip installation if the CRD is already present. Managing CRDs outside of Helm is recommended, also see the [Helm best practices](https://helm.sh/docs/chart_best_practices/custom_resource_definitions/)
| `service.type`                                    | `ClusterIP`                                          | Service type to be used (exposing the Helm Operator API outside of the cluster is not advised)
| `service.port`                                    | `3030`                                               | Service port to be used
| `updateChartDeps`                                 | `true`                                               | Update dependencies for charts
| `git.pollInterval`                                | `git.pollInterval`                                   | Period on which to poll git chart sources for changes
| `git.timeout`                                     | `git.timeout`                                        | Duration after which git operations time out
| `git.defaultRef`                                  | `master`                                             | Ref to clone chart from if ref is unspecified in a HelmRelease
| `git.ssh.secretName`                              | `None`                                               | The name of the kubernetes secret with the SSH private key, supercedes `git.secretName`
| `git.ssh.known_hosts`                             | `None`                                               | The contents of an SSH `known_hosts` file, if you need to supply host key(s)
| `git.ssh.configMapName`                           | `None`                                               | The name of a kubernetes config map containing the ssh config
| `git.ssh.configMapKey`                            | `config`                                             | The name of the key in the kubernetes config map specified above
| `chartsSyncInterval`                              | `3m`                                                 | Period on which to reconcile the Helm releases with `HelmRelease` resources
| `statusUpdateInterval`                            | `30s`                                                | Period on which to update the Helm release status in `HelmRelease` resources
| `workers`                                         | `None`                                               | Number of workers processing releases
| `logFormat`                                       | `fmt`                                                | Log format (fmt or json)
| `logReleaseDiffs`                                 | `false`                                              | Helm Operator should log the diff when a chart release diverges (possibly insecure)
| `allowNamespace`                                  | `None`                                               | If set, this limits the scope to a single namespace. If not specified, all namespaces will be watched
| `helm.versions`                                   | `v2,v3`                                              | Helm versions supported by this operator instance, if v2 is specified then Tiller is required
| `tillerNamespace`                                 | `kube-system`                                        | Namespace in which the Tiller server can be found
| `tillerSidecar.enabled`                           | `false`                                              | Whether to deploy Tiller as a sidecar (and listening on `localhost` only).
| `tillerSidecar.image.repository`                  | `gcr.io/kubernetes-helm/tiller`                      | Image repository to use for the Tiller sidecar.
| `tillerSidecar.image.tag`                         | `v2.16.1`                                            | Image tag to use for the Tiller sidecar.
| `tillerSidecar.storage`                           | `secret`                                             | Storage engine to use for the Tiller sidecar.
| `tls.enable`                                      | `false`                                              | Enable TLS for communicating with Tiller
| `tls.verify`                                      | `false`                                              | Verify the Tiller certificate, also enables TLS when set to true
| `tls.secretName`                                  | `helm-client-certs`                                  | Name of the secret containing the TLS client certificates for communicating with Tiller
| `tls.keyFile`                                     | `tls.key`                                            | Name of the key file within the k8s secret
| `tls.certFile`                                    | `tls.crt`                                            | Name of the certificate file within the k8s secret
| `tls.caContent`                                   | `None`                                               | Certificate Authority content used to validate the Tiller server certificate
| `tls.hostname`                                    | `None`                                               | The server name used to verify the hostname on the returned certificates from the Tiller server
| `configureRepositories.enable`                    | `false`                                              | Enable volume mount for a `repositories.yaml` configuration file and repository cache
| `configureRepositories.volumeName`                | `repositories-yaml`                                  | Name of the volume for the `repositories.yaml` file
| `configureRepositories.secretName`                | `flux-helm-repositories`                             | Name of the secret containing the contents of the `repositories.yaml` file
| `configureRepositories.cacheName`                 | `repositories-cache`                                 | Name for the repository cache volume
| `configureRepositories.repositories`              | `None`                                               | List of custom Helm repositories to add. If non empty, the corresponding secret with a `repositories.yaml` will be created
| `initPlugins.enable`                              | `false`                                              | Enable the initialization of Helm plugins using init containers
| `initPlugins.cacheVolumeName`                     | `plugins-cache`                                      | Name for the plugins cache volume
| `initPlugins.plugins`                             | `None`                                               | List of Helm plugins to initialize before starting the operator. If non empty, an init container will be added for every entry
| `kube.config`                                     | `None`                                               | Override for kubectl default config in the Helm Operator pod(s)
| `prometheus.enabled`                              | `false`                                              | If enabled, adds prometheus annotations to Helm Operator pod(s)
| `prometheus.serviceMonitor.create`                | `false`                                              | Set to true if using the Prometheus Operator
| `prometheus.serviceMonitor.interval`              | `None`                                               | Interval at which metrics should be scraped
| `prometheus.serviceMonitor.namespace`             | `None`                                               | The namespace where the ServiceMonitor is deployed
| `prometheus.serviceMonitor.additionalLabels`      | `{}`                                                 | Additional labels to add to the ServiceMonitor
| `initContainers`                                  | `[]`                                                 | Init containers and their specs
| `hostAliases`                                     | `{}`                                                 | Host aliases allow the modification of the hosts file (`/etc/hosts`) inside Helm Operator container. See 


# Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

# License

[License](LICENSE.md)