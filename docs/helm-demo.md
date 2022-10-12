# CI/GitOps with Helm chart demo

CI workflow with Helm chart:
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

## More resources

- CI/GitOps with Helm, GitHub Actions, and Config Sync
  - [with Google Artifact Registry](https://medium.com/google-cloud/b48604191fda)
  - [with GitHub Container Registry](https://medium.com/google-cloud/836913e74e79)