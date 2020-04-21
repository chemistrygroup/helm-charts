# Service

This is a template Helm Chart for deploying services into our cluster. This template contains all the required resources to successfully deploy a service into our kuberentes cluster.

# Dependencies

- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)
- [yamllint](https://yamllint.readthedocs.io/en/stable/)

# Repo Structure

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

# Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

# License

[License](LICENSE.md)