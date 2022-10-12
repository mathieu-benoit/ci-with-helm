# ci-with-helm

Structure of the folders and files:
```
.
├── docs
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
│   ├── kustomization.yaml
│   ├── privileged-containers
│   │   ├── kustomization.yaml
│   │   ├── samples
│   │   │   ├── constraint.yaml
│   │   │   ├── example_allowed.yaml
│   │   │   └── example_disallowed.yaml
│   │   ├── suite.yaml
│   │   └── template.yaml
│   └── users
│       ├── kustomization.yaml
│       ├── samples
│       │   ├── constraint.yaml
│       │   ├── example_allowed.yaml
│       │   └── example_disallowed.yaml
│       ├── suite.yaml
│       └── template.yaml
└── README.md
```

## CI workflow with Gatekeeper policies

```mermaid
flowchart TB
    subgraph tests-job
    gator((gator verify))
    end
    subgraph artifact-job
    login(oras login) --> build(kustomize build)
    build --> package(tar)
    package --> push(oras push)
    end
    tests-job --> artifact-job
```

## CI workflow with Helm chart

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
    login(helm login) --> package(helm package)
    package --> push(helm push)
    end
    tests-job --> artifact-job
```

## CI workflow with Kustomize

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
    login(oras login) --> package(tar)
    package --> push(oras push)
    end
    tests-job --> artifact-job
```