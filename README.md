# ci-with-helm

Structure of the folders and files in this repository:
```
.
├── .github
│   └── workflows
│       ├── ci-helm.yaml
│       ├── ci-oras.yaml
│       └── ci-policies.yaml
├── docs
│   ├── helm-demo.md
│   ├── k8s-cluster-setup.md
│   ├── kustomize-demo.md
│   └── policies-demo.md
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

This repository contains 3 main demos:
- [CI/GitOps with Gatekeeper policies as OCI artifact](docs/policies-demo.md)
- [CI/GitOps with Helm chart](docs/helm-demo.md)
- [CI/GitOps with Kustomize overlay as OCI artifact](docs/kustomize-demo.md)