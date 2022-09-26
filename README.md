# ci-with-helm

```
.
├── .github
│   └── workflows
│       ├── ci-helm.yaml
│       ├── ci-oras.yaml
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
│   ├── k8spspallowedusers.yaml
│   ├── k8spspprivilegedcontainer.yaml
│   ├── psp-pods-allowed-user-ranges.yaml
│   └── psp-privileged-container.yaml
```

## With Helm chart

```mermaid
flowchart TB
    subgraph diff-job
    get(kpt get base-ref) --> build1(helm template current)
    build1 --> build2(helm template base-ref)
    build2 --> diff(diff base-ref current)
    end
    subgraph tests-job
    lint((helm lint)) --> template{{helm template}}
    template --> vet((nomos vet))
    vet --> kubeval((kubeval))
    kubeval --> gatekeeper((gatekeeper))
    gatekeeper --> trivy((trivy config))
    trivy --> kind{{kind cluster}}
    kind --> deploy((helm install))
    end
    subgraph artifact-job
    package(helm package) --> login(helm login)
    login --> push(helm push)
    end
    tests-job --> artifact-job
```

## With Kustomize

```mermaid
flowchart TB
    subgraph diff-job
    get(kpt get base-ref) --> build1(kustomize build current)
    build1 --> build2(kustomize build base-ref)
    build2 --> diff(diff base-ref current)
    end
    subgraph tests-job
    hydrate{{kustomize build}} --> vet((nomos vet))
    vet --> kubeval((kubeval))
    kubeval --> gatekeeper((gatekeeper))
    gatekeeper --> trivy((trivy config))
    trivy --> kind{{kind cluster}}
    kind --> deploy((kubectl apply -f))
    end
    subgraph artifact-job
    package(tar) --> login(oras login)
    login --> push(oras push)
    end
    tests-job --> artifact-job
```
