# ci-with-helm


With Helm chart:

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

With Kustomize:

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
