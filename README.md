# ci-with-helm

```
.
├── kustomization.yaml
├── my-chart
│   ├── charts
│   ├── Chart.yaml
│   ├── templates
│   │   ├── deployment.yaml
│   │   ├── _helpers.tpl
│   │   ├── hpa.yaml
│   │   ├── ingress.yaml
│   │   ├── NOTES.txt
│   │   ├── serviceaccount.yaml
│   │   ├── service.yaml
│   │   └── tests
│   └── values.yaml
├── policies
    ├── k8spspallowedusers.yaml
    ├── psp-pods-allowed-user-ranges.yaml
```

## With Helm chart

```mermaid
flowchart TB
    subgraph tests
    lint((helm lint)) --> template{{helm template}}
    template --> vet((nomos vet))
    vet --> kubeval((kubeval))
    kubeval --> gatekeeper((gatekeeper))
    gatekeeper --> trivy((trivy config))
    trivy --> kind{{kind cluster}}
    kind --> deploy((helm install))
    end
    subgraph artifact
    package(helm package) --> login(helm login)
    login --> push(helm push)
    end
    tests --> artifact
```

## With Kustomize

```mermaid
flowchart TB
    subgraph tests
    hydrate{{kustomize build}} --> vet((nomos vet))
    vet --> kubeval((kubeval))
    kubeval --> gatekeeper((gatekeeper))
    gatekeeper --> trivy((trivy config))
    trivy --> kind{{kind cluster}}
    kind --> deploy((kubectl apply -k))
    end
    subgraph artifact
    package(tar) --> login(oras login)
    login --> push(oras push)
    end
    tests --> artifact
```
