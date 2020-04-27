# Istio Policies

This is a template Helm Chart for generating istio traffic management manifest. All the templates present in this chart were created by following the [Istio Traffica Management Tasks](https://istio.io/docs/tasks/traffic-management/)

## Dependencies

- [Helm v3](https://helm.sh/docs/intro/quickstart/#install-helm)

## Installation

Add the Chemistry Group Helm repository:

```bash
helm repo add chemistry https://chemistrygroup.github.io/helm-charts
```

Install the service using the chart:

```bash
helm upgrade -i service chemistry/istio-policies
```

## Repo Structure

The following dir tree represents the repo directory structure:

```bash
- templates # folder were the k8s resources templates are stored
-- _helpers.tpl # helper's/utils file for the generation of shared helm values
-- destinationrules.yaml # template for destinationrules manifest generation
-- serviceentries.yaml # template for serviceentries manifest generation
-- virtualservices.yaml # template for virtualservices manifest generation
-- gateways.yaml # template for gateway manifest generation
- Chart.yaml # chart description file
- values.yaml # sample values yaml file 
- values.schema.json # yaml json schema used to validate the format of values.yaml
```


## Configuration

The following tables lists the configurable parameters of the Service
chart and their default values.

| Parameter                                         | Default                             | Description
| ------------------------------------------------- | ----------------------------------- | ---
| `trafficManagement.external.http`                 | `[]`                                | Allow connections to external http service
| `trafficManagement.external.https`                | `[]`                                | Allow connections to external https service
| `trafficManagement.external.httpAndHttps`         | `[]`                                | Allow connections to external service using http or https
| `trafficManagement.external.httpToHttps`          | `[]`                                | Allow connections to external service using internal http traffic and originating https traffic to the external service
| `trafficManagement.external.tcp`                  | `[]`                                | Allow connections to external tcp service 
| `trafficManagement.egress.http`                   | `[]`                                | Allow connections to external http service throught istio Egress gateway
| `trafficManagement.egress.https`                  | `[]`                                | Allow connections to external https service throught istio Egress gateway
| `trafficManagement.egress.httpToHttps`            | `[]`                                | Allow connections to external service using internal http traffic and originating https traffic to the external service throught istio Egress gateway
| `trafficManagement.egress.tcp`                    | `[]`                                | Allow connections to external tcp service throught istio Egress gateway
| `trafficManagement.ingress`                       | `[]`                                | Configure istio ingress gateway rules and routings
| `trafficManagement.extraDestinationRules`         | `[]`                                | Generate istio extra destinationRule manifests
| `trafficManagement.extraVirtualService`           | `[]`                                | Generate istio extra virtualService manifests
| `trafficManagement.extraServiceEntries`           | `[]`                                | Generate istio extra extraServiceEntries manifests
| `trafficManagement.extraGateways`                 | `[]`                                | Generate istio extra extraGateways manifests

## Sample Usage

Here's some sample operations that you can execute with the templates in this chart

### Access an external HTTP service

Values settings:
```yaml
trafficManagement:
  external:
    http: 
    - name: service
      host: example.com
      port: 80
...
```

Output:
```yaml
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  resolution: DNS
  location: MESH_EXTERNAL
  # Egress Rules
```


### Access an external HTTPS service

Values settings:
```yaml
trafficManagement:
  external:
  ...
    https: 
    - name: service
      host: example.com
      port: 443
...
```

Output:
```yaml
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 443
    name: https-port
    protocol: HTTPS
  resolution: DNS
  location: MESH_EXTERNAL
```

### Access an external HTTP/HTTPS service

Values settings:
```yaml
trafficManagement:
  external:
  ...
    httpAndHttps: 
    - name: service
      host: example.com
      port:
        http: 80
        https: 443
...
```

Output:
```yaml
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  - number: 443
    name: https-port
    protocol: HTTPS
  resolution: DNS
  # Egress Rules
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  tls:
  - match:
    - port: 443
      sni_hosts:
      - example.com
    route:
    - destination:
        host: example.com
        port:
          number: 443
      weight: 100
```


### Access an external HTTPS service from a HTTP request originating TLS in egress gateway 

Values settings:
```yaml
trafficManagement:
  external:
  ...
    httpToHttps:
    - name: service
      host: example.com
      port:
        http: 80
        https: 443
...
```

Output:
```yaml
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: example.com
  subsets:
  - name: service-tls-origination
    trafficPolicy:
      loadBalancer:
        simple: ROUND_ROBIN
      portLevelSettings:
      - port:
          number: 443
        tls:
          mode: SIMPLE
  # Egress Rules
  # Extra Destination Rules
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  - number: 443
    name: https-port
    protocol: HTTPS
  resolution: DNS
  # Egress Rules
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  tls:
  - match:
    - port: 80
    route:
    - destination:
        host: example.com
        subset: service-tls-origination
        port:
          number: 443
```


### Access an external TCP service

Values settings:
```yaml
trafficManagement:
  external:
  ...
    tcp:
    - name: service
      host: example.com
      ip: 127.0.0.1
      port: 5432
...
```

Output:

```yaml
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-ext
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  addresses:
  - 127.0.0.1/32
  ports:
  - number: 5432
    name: tcp-port
    protocol: TCP
  location: MESH_EXTERNAL
```


### Egress to external HTTP Service

Values settings:
```yaml
trafficManagement:
...
  egress:
    http: 
    - name: service
      host: example.com
      gateway:
        name: istio-egressgateway
        selector: egressgateway
      port: 80
...
```

Output:
```yaml
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: service-subset
  # Extra Destination Rules
---
# Source: istio-policies/templates/gateways.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 80
      name: http-port
      protocol: HTTP
    hosts:
    - example.com
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  resolution: DNS
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  gateways:
  - istio-egressgateway
  - mesh
  http:
  - match:
    - gateways:
      - mesh
      port: 80
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: service-subset
        port:
          number: 80
      weight: 100
  - match:
    - gateways:
      - istio-egressgateway
      port: 80
    route:
    - destination:
        host: example.com
        port:
          number: 80
      weight: 100
```


### Egress to external HTTPS Service

Values settings:
```yaml
trafficManagement:
...
  egress:
  ...
    https:
    - name: service
      host: example.com
      gateway:
        name: istio-egressgateway
        selector: egressgateway
      port: 443
...
```

Output:
```yaml
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: service-subset
  # Extra Destination Rules
---
# Source: istio-policies/templates/gateways.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 443
      name: http-port
      protocol: HTTP
    hosts:
    - example.com
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 443
    name: tls-port
    protocol: TLS
  resolution: DNS
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  gateways:
  - mesh
  - istio-egressgateway
  tls:
  - match:
    - gateways:
      - mesh
      port: 443
      sni_hosts:
      - example.com
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: service-subset
        port:
          number: 443
  - match:
    - gateways:
      - istio-egressgateway
      port: 443
      sni_hosts:
        - example.com
    route:
    - destination:
        host: example.com
        port:
          number: 443
      weight: 100
```


### Egress to external HTTP Service with Egress TLS origination to HTTPS

Values settings:
```yaml
trafficManagement:
...
  egress:
  ...
    httpToHttps:
    - name: service
      host: example.com
      gateway:
        name: istio-egressgateway
        selector: egressgateway
      port:
        http: 80
        https: 443
...
```

Output:
```yaml
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: service-subset
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress-orig
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: example.com
  trafficPolicy:
    loadBalancer:
      simple: ROUND_ROBIN
    portLevelSettings:
    - port:
        number: 443
      tls:
        mode: SIMPLE
  # Extra Destination Rules
---
# Source: istio-policies/templates/gateways.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 80
      name: http-port
      protocol: HTTP
    hosts:
    - example.com
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  - number: 443
    name: https-port
    protocol: HTTPS
  resolution: DNS
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  - number: 443
    name: https-port
    protocol: HTTPS
  resolution: DNS
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  gateways:
  - istio-egressgateway
  - mesh
  http:
  - match:
    - gateways:
      - mesh
      port: 80
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: service-subset
        port:
          number: 80
      weight: 100
  - match:
    - gateways:
      - istio-egressgateway
      port: 80
    route:
    - destination:
        host: example.com
        port:
          number: 443
      weight: 100
```




### Egress to external TCP Service

Values settings:
```yaml
trafficManagement:
...
  egress:
  ...
    tcp:
    - name: service
      host: example.com
      gateway:
        name: istio-egressgateway-postgresql
        selector: egressgateway-postgresql
      ip: 127.0.0.1
      port: 5432
...
```

Output:
```yaml
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress-tcp
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: service-sub
    trafficPolicy:
      loadBalancer:
        simple: ROUND_ROBIN
      portLevelSettings:
      - port:
          number: 5432
        tls:
          mode: ISTIO_MUTUAL
          sni: example.com
---
# Source: istio-policies/templates/destinationrules.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  host: example.com
  # Extra Destination Rules
---
# Source: istio-policies/templates/gateways.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 5432
      name: tcp-port
      protocol: TCP
    hosts:
    - example.com
    tls:
      mode: MUTUAL
      serverCertificate: /etc/certs/cert-chain.pem
      privateKey: /etc/certs/key.pem
      caCertificates: /etc/certs/root-cert.pem
---
# Source: istio-policies/templates/serviceentries.yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  addresses:
  - 127.0.0.1/32
  ports:
  - number: 5432
    name: tcp-port
    protocol: TCP
  location: MESH_EXTERNAL
  resolution: STATIC
  endpoints:
  - address: 127.0.0.1
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-egress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - example.com
  gateways:
  - mesh
  - istio-egressgateway
  tcp:
  - match:
    - gateways:
      - mesh
      destinationSubnets:
      - 127.0.0.1/32
      port: 5432
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: service-subset
        port:
          number: 5432
  - match:
    - gateways:
      - istio-egressgateway
      port: 5432
    route:
    - destination:
        host: example.com
        port:
          number: 5432
      weight: 100
```

### Ingress

Values settings:
```yaml
trafficManagement:
...
  ingress:
  ...
    - name: service
      host:
        origin: "*"
        target: service.default.svc.cluster.local
      gateway: istio-ingressgateway
      port: 80
      paths:
        - sourcePath: 
            exact: /api
          targetPath: /api
...
```

Output:
```yaml
---
# Source: istio-policies/templates/virtualservices.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: service-ingress
  namespace: default
  labels:
    app: istio-policies
    chart: istio-policies-1.0.0
    release: RELEASE-NAME
    heritage: Helm
    version: v1
    helm.sh/chart: istio-policies-1.0.0
    app.kubernetes.io/name: istio-policies
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/version: "1.5.1"
    app.kubernetes.io/managed-by: Helm
spec:
  hosts:
  - "*"
  gateways:
  - istio-ingressgateway
  http:
  - match:
    - uri:
        exact: /api
    rewrite:
      uri: /api
    route:
    - destination:
        port:
          number: 80
        host: service.default.svc.cluster.local
```


# Contibuting

Create a new branch and open a pull request to the master branch, to merge all checks have to pass and you have to have 1 approval from at least 2 reviewers

# License

[License](LICENSE.md)