# Fluent-bit

This is the aws-fluent-bit Helm Chart.

## Dependencies

- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)

## Installation

Add the Chemistry Group Helm repository:

```bash
helm repo add chemistry https://chemistrygroup.github.io/helm-charts
```

Install the service using the chart:

```bash
helm upgrade -i fluent-bit chemistry/fluent-bit
```

## Repo Structure

The following dir tree represents the repo directory structure:

```bash
- templates # folder were the k8s resources templates are stored
-- _helpers.tpl # helper's/utils file for the generation of shared helm values
-- configmap.yaml # configmap
-- daemonset.yaml
-- rbac.yaml # creating the ClusterRole/Role and the respective ClusterRoleBinding/RoleBinding
-- sa.yaml # creating a service account
- Chart.yaml # chart description file
- values.yaml # sample values yaml file
- values.schema.yaml # yaml json schema used to validate the format of values.yaml
```


## Configuration

The following tables lists the configurable parameters of the Service
chart and their default values.

| Parameter                                         | Default                                                                       | Description
| ------------------------------------------------- | ----------------------------------------------------------------------------- | ---
| `version`                                         | `v1`                                                                          | Service Version label
| `nameOverride`                                    | `fluent-bit`                                                                        | Override default chart name ( daemonset name, service name .. )
| `fullnameOverride`                                | `None`                                                                        | Override default chart full name
| `prometheus.enabled`                              | `true`                                                                        | Flags pod for prometheus metric scrapping
| `prometheus.path`                                 | `/api/v1/metrics/prometheus`                                                                    | Url Path for metrics scrapping
| `prometheus.port`                                 | `2020`                                                                        | Metrics webserver running port
| `prometheus.scheme`                               | `TCP`                                                                        | Metrics webserver protocol/scheme
| `serviceAccount.create`                           | `true`                                                                        | If `true`, create a new service account
| `serviceAccount.name`                             | `{nameOverride}`                                                              | Service account to be used
| `serviceAccount.annotations`                      | `{}`                                                                          | Service account annotations
| `rbac.enabled`                                    | `true`                                                                        | If `true`, create and use RBAC resources
| `rbac.type`                                       | `cluster`                                                                     | `cluster` creates cluster level role's , `namespace` create's namespace level roles
| `affinity`                                        | `{}`                                                                          | Affinity properties for the daemonset
| `nodeSelector`                                    | `{}`                                                                          | Node Selector properties for the daemonset
| `tolerations`                                     | `[]`                                                                          | Tolerations properties for the daemonset
| `image.registry`                                  | `docker.io`                                                                      | Image repository host url
| `image.repository`                                | `amazon/aws-for-fluent-bit`                                      | Image repository
| `image.tag`                                       | `2.3.0`                                                                      | Image tag
| `image.pullPolicy`                                | `Always`                                                                | Image pull policy
`resources.requests.cpu`                            | `100Mi`                                                                        | CPU resource requests for the daemonset
| `resources.requests.memory`                       | `500m`                                                                       | Memory resource requests for the daemonset
| `resources.limits.cpu`                            | ``                                                                        | CPU resource limits for the daemonset
| `resources.limits.memory`                         | `500m`                                                                      | Memory resource limits for the daemonset
| `podSecurityContext`                              | `{}`                                                                          | Security context policies to add to the pod
| `securityContext`                                 | `{}`                                                                          | Security context policies to add to the container
| `configMaps`                                      | `default configuration with cloudwatch output`                                                                          | A list of configmap resources to create and their data

# Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

# License

[License](LICENSE.md)