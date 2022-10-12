# k8s-cluster-setup

Two options are documented below to have a GKE cluster ready to use with either Config Sync OSS or via Anthos Config Management (ACM). Optionally, you will have the same option for Gatekeeper OSS or via Policy Controller.

## Config Sync (OSS) on GKE

As prerequisites, you need to have these tools installed:
- [`gcloud`](https://cloud.google.com/sdk/docs/install)
- [`helm`]()

Set current project:
```bash
PROJECT_ID=FIXME
gcloud config set project ${PROJECT_ID}
```

Create a GKE cluster with Workload Identity enabled:
```bash
CLUSTER_NAME=oci-artifacts-with-acm-demo
CLUSTER_ZONE=us-east4-a

gcloud services enable container.googleapis.com

gcloud container clusters create ${CLUSTER_NAME} \
    --zone ${CLUSTER_ZONE}
```

Install Config Sync:
```bash
FIXME
```

(Optional) Install Gatekeeper:
```bash
helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts
helm install gatekeeper/gatekeeper \
    --name-template=gatekeeper \
    --namespace gatekeeper-system \
    --create-namespace \
    --set auditInterval=5
```

## Config Sync (ACM) on GKE

As prerequisites, you need to have these tools installed:
- [`gcloud`](https://cloud.google.com/sdk/docs/install)

Set current project:
```bash
PROJECT_ID=FIXME
gcloud config set project ${PROJECT_ID}
```

Create a GKE cluster with Workload Identity enabled:
```bash
CLUSTER_NAME=oci-artifacts-with-acm-demo
CLUSTER_ZONE=us-east4-a

gcloud services enable container.googleapis.com

gcloud container clusters create ${CLUSTER_NAME} \
    --zone ${CLUSTER_ZONE} \
    --workload-pool ${PROJECT_ID}.svc.id.goog

gcloud services enable gkehub.googleapis.com

gcloud container fleet memberships register ${CLUSTER_NAME} \
    --gke-cluster ${CLUSTER_ZONE}/${CLUSTER_NAME} \
    --enable-workload-identity
```

Enable Config Sync:
```bash
gcloud beta container fleet config-management enable

cat <<EOF > acm-config.yaml
applySpecVersion: 1
spec:
  configSync:
    enabled: true
EOF
gcloud beta container fleet config-management apply \
    --membership ${CLUSTER_NAME} \
    --config acm-config.yaml
```

(Optional) Enable Policy Controller if you want to deploy the policies illustrated in this repository:
```bash
cat <<EOF > acm-config.yaml
applySpecVersion: 1
spec:
  configSync:
    enabled: true
  policyController:
    enabled: true
    templateLibraryInstalled: false
    referentialRulesEnabled: false
EOF
gcloud beta container fleet config-management apply \
    --membership ${CLUSTER_NAME} \
    --config acm-config.yaml
```
_Note: we don't need to install the default template library since we are bringing our own `ConstraintTemplates` in this demo._