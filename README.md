## Reflection application

This service is intended to be used in a Kubernetes cluster to provide a way to reflect the incoming requests. It is a simple service that returns the request headers and body in the response as well as pod metadata and its upstream services metadata/headers/body if any. The real advantage is deploying multiple instances of this service and configuring its environment variables to simulate a service mesh.

**Supported protocols**
- HTTP
  - GET

### Sample deploy
There are two manifests in the `manifests` folder that can be used to deploy to a Kubernetes cluster.

```bash
kubectl apply -f manifests/sample-mesh-manifest.yaml # Or big-mesh-manifest.yaml to deploy multiple services
```

```bash
# Requesting
curl -SsL -v http://localhost:8080 
```

### Generating a new mesh
To generate a new mesh in case we change image, namespace or add tolerations etc... we can use the scripts in the `scripts` folder.

```bash
# Generate a sample mesh with a couple of services
cd scripts
./sample-mesh.sh # Or big-mesh.sh to deploy multiple services
```

The `reflection.sh` can be used to generate the manifests so you don't have to touch on them if wants to change something, once you run the script it will create the workload in the cluster then patch it and finally write the manifests to the `manifests` folder.


### Response example
```yaml
client_ip: 172.24.72.4
downstreamServices:
  beta:
    client_ip: 127.0.0.6
    downstreamServices: null
    headers:
      Accept-Encoding:
        - gzip
      User-Agent:
        - Go-http-client/1.1
      X-Envoy-Attempt-Count:
        - "1"
      X-Forwarded-Client-Cert:
        - By=spiffe://cluster.local/ns/mesh-example/sa/default;Hash=e0d9770e78a3ad40242f735ed89c4d2332cb9fe2aa67a7a0fcec9f5ff81e27d9;Subject="";URI=spiffe://cluster.local/ns/mesh-example/sa/default
      X-Forwarded-Proto:
        - http
      X-Request-Id:
        - e6bdf151-26e5-4ae4-bc59-63edc69fff40
    metadata:
      hostIP: 172.24.72.7
      namespace: mesh-example
      node: aks-default-13582736-vmss000000
      podIP: 10.244.2.214
      pod_name: beta-7c7dd8dd9b-db6cp
      service_account: default
  gamma:
    client_ip: 127.0.0.6
    downstreamServices:
      delta:
        client_ip: 127.0.0.6
        downstreamServices: null
        headers:
          Accept-Encoding:
            - gzip
          User-Agent:
            - Go-http-client/1.1
          X-Envoy-Attempt-Count:
            - "1"
          X-Forwarded-Client-Cert:
            - By=spiffe://cluster.local/ns/mesh-example/sa/default;Hash=376a7769bc7b57e2b9a0cf2243b2b4f20e69597ef1f972ca696fd00361c95f1e;Subject="";URI=spiffe://cluster.local/ns/mesh-example/sa/default
          X-Forwarded-Proto:
            - http
          X-Request-Id:
            - 9a8fbd46-13b2-458e-b71f-91da29aab52f
        metadata:
          hostIP: 172.24.72.7
          namespace: mesh-example
          node: aks-default-13582736-vmss000000
          podIP: 10.244.2.251
          pod_name: delta-6dd56dd5c6-cc8rj
          service_account: default
    headers:
      Accept-Encoding:
        - gzip
      User-Agent:
        - Go-http-client/1.1
      X-Envoy-Attempt-Count:
        - "1"
      X-Forwarded-Client-Cert:
        - By=spiffe://cluster.local/ns/mesh-example/sa/default;Hash=e0d9770e78a3ad40242f735ed89c4d2332cb9fe2aa67a7a0fcec9f5ff81e27d9;Subject="";URI=spiffe://cluster.local/ns/mesh-example/sa/default
      X-Forwarded-Proto:
        - http
      X-Request-Id:
        - bef78eb3-c0a6-499d-ad02-ceb5882a5d85
    metadata:
      hostIP: 172.24.72.7
      namespace: mesh-example
      node: aks-default-13582736-vmss000000
      podIP: 10.244.2.165
      pod_name: gamma-79754fb6b5-jj72s
      service_account: default
headers:
  Accept:
    - '*/*'
  User-Agent:
    - curl/7.68.0
  X-Envoy-Attempt-Count:
    - "1"
  X-Envoy-Internal:
    - "true"
  X-Forwarded-Client-Cert:
    - By=spiffe://cluster.local/ns/mesh-example/sa/default;Hash=fedc9522df08edf40f6d372beb7c7c01aff89a5b78c24c6ccb1df4c87a2e3243;Subject="";URI=spiffe://cluster.local/ns/mesh-example/sa/api-gateway-istio-istio
  X-Forwarded-For:
    - 172.24.72.4
  X-Forwarded-Proto:
    - http
  X-Request-Id:
    - b9d082d9-849c-4f2f-b3a7-f5d9c184a791
metadata:
  hostIP: 172.24.72.7
  namespace: mesh-example
  node: aks-default-13582736-vmss000000
  podIP: 10.244.2.7
  pod_name: alpha-7979ddc4c7-tdpfn
  service_account: default
```