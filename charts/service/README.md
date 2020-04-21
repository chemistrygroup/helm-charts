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

| Parameter                                         | Default                                                                       | Description
| ------------------------------------------------- | ----------------------------------------------------------------------------- | ---
| `version`                                         | `v1`                                                                          | Service Version label
| `nameOverride`                                    | `None`                                                                        | Override default chart name ( deployment name, service name .. )
| `fullnameOverride`                                | `None`                                                                        | Override default chart full name
| `prometheus.enabled`                              | `true`                                                                        | Flags pod for prometheus metric scrapping
| `prometheus.path`                                 | `/metrics`                                                                    | Url Path for metrics scrapping
| `prometheus.port`                                 | `8082`                                                                        | Metrics webserver running port
| `prometheus.scheme`                               | `http`                                                                        | Metrics webserver protocol/scheme
| `serviceAccount.create`                           | `true`                                                                        | If `true`, create a new service account
| `serviceAccount.name`                             | `{nameOverride}`                                                              | Service account to be used
| `rbac.enabled`                                    | `true`                                                                        | If `true`, create and use RBAC resources
| `rbac.type`                                       | `cluster`                                                                     | `cluster` creates cluster level role's , `namespace` create's namespace level roles
| `affinity`                                        | `{}`                                                                          | Affinity properties for the deployment
| `nodeSelector`                                    | `{}`                                                                          | Node Selector properties for the deployment
| `tolerations`                                     | `[]`                                                                          | Tolerations properties for the deployment
| `replicaCount`                                    | `1`                                                                           | Number of pods to deploy
| `image.registry`                                  | `gcr.io`                                                                      | Image repository host url
| `image.repository`                                | `hello-minikube-zero-install/hello-node`                                      | Image repository
| `image.tag`                                       | `latest`                                                                      | Image tag
| `image.pullPolicy`                                | `IfNotPresent`                                                                | Image pull policy
`resources.requests.cpu`                            | `250m`                                                                        | CPU resource requests for the deployment
| `resources.requests.memory`                       | `512Mi`                                                                       | Memory resource requests for the deployment
| `resources.limits.cpu`                            | `500m`                                                                        | CPU resource limits for the deployment
| `resources.limits.memory`                         | `1024Mi`                                                                      | Memory resource limits for the deployment
| `hpa.minReplicas`                                 | `1`                                                                           | minimum number of replicas for this service
| `hpa.maxReplicas`                                 | `2`                                                                           | maximum number of replicas for this service
| `hpa.targetCPU`                                   | `80`                                                                          | target cpu value that triggers the scale action
| `probes.livenessProbe.initialDelaySeconds`        | `60`                                                                          | number of seconds to delay the start of the livenessProbe
| `probes.livenessProbe.failureThreshold`           | `2`                                                                           | fail threashould to reboot the pod
| `probes.livenessProbe.httpGet.path`               | `/`                                                                           | url path were to perform http health check's
| `probes.livenessProbe.httpGet.port`               | `8080`                                                                        | port were the http webserver is running
| `probes.livenessProbe.httpGet.scheme`             | `HTTP`                                                                        | use `HTTP`or `HTTPS`
| `probes.livenessProbe.periodSeconds`              | `10`                                                                          | interval between liveness checks
| `probes.livenessProbe.successThreshold`           | `1`                                                                           | number of success calls to consider a pod live
| `probes.livenessProbe.timeoutSeconds`             | `20`                                                                          | time to wait for a server response before flagging it as failed
| `probes.readinessProbe.initialDelaySeconds`       | `60`                                                                          | number of seconds to delay the start of the readinessProbe
| `probes.readinessProbe.failureThreshold`          | `10`                                                                          | fail threashould to reboot a pod
| `probes.readinessProbe.httpGet.path`              | `/`                                                                           | url path were to perform http health check's
| `probes.readinessProbe.httpGet.port`              | `8080`                                                                        | port were the http webserver is running
| `probes.readinessProbe.httpGet.scheme`            | `HTTP`                                                                        | use `HTTP`or `HTTPS`
| `probes.readinessProbe.periodSeconds`             | `30`                                                                          | interval between readiness checks
| `probes.readinessProbe.successThreshold`          | `1`                                                                           | number of success calls to consider a pod live
| `probes.readinessProbe.timeoutSeconds`            | `10`                                                                          | time to wait for a server response before flagging it as failed
| `podSecurityContext`                              | `{}`                                                                          | Security context policies to add to the pod
| `securityContext`                                 | `{}`                                                                          | Security context policies to add to the container
| `extraEnvs`                                       | `[{"name": "XMS", "value": "512Mi"}, {"name": "XMX", "value": "1024Mi"}]`     | Extra environment variables for the Helm Operator pod(s)
| `extraVolumes`                                    | `[]`                                                                          | Additional volumes to the pod
| `extraVolumeMounts`                               | `[]`                                                                          | Additional volumeMounts to the controller main container 
| `extraContainerPorts`                             | `[]`                                                                          | Additional containerPorts to expose
| `service.enabled`                                 | `true`                                                                        | If `true` a ClusterIP service with the same name as the deployment/chart will be created and assigned to the deployment
| `service.http`                                    | `true`                                                                        | If `true` the service will route external traffic from port 80 to pod port 8080
| `service.ingress.enabled`                         | `true`                                                                        | If `true` an ingress route should be created for this service ( W.I.P. )
| `service.ingress.paths`                           | `[]`                                                                          | A list of items ( {path: "", targetPath: ""}) of gateway paths to route traffic from to a specific service url path (W.I.P.)
| `service.extraPorts`                              | `[]`                                                                          | A list of extra service ports to route traffic
| `configMaps`                                      | `[]`                                                                          | A list of configmap resources to create and their data
| `secrets`                                         | `[]`                                                                          | A list of secret resources to create and their data


# Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

# License

[License](LICENSE.md)