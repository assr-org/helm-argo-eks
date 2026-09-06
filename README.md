# helm-argo-eks

A minimal Helm chart for deploying a simple containerized application to
Amazon EKS via ArgoCD GitOps.

## Structure

```
helm-argo-eks/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    └── NOTES.txt
```

## Chart details

| Field       | Value                                          |
|-------------|-------------------------------------------------|
| Name        | `helm-argo-eks`                                 |
| Version     | `0.1.0`                                         |
| App Version | `v1`                                            |
| Description | A simple app Helm chart for EKS + ArgoCD GitOps demo |

## Default values (`values.yaml`)

| Key                    | Default                        | Description                          |
|------------------------|---------------------------------|---------------------------------------|
| `replicaCount`         | `2`                             | Number of pod replicas                |
| `image.repository`     | `achin16/kubernetes-argo-eks`   | Container image repository            |
| `image.tag`            | `v1`                            | Container image tag                   |
| `image.pullPolicy`     | `IfNotPresent`                  | Image pull policy                     |
| `service.type`         | `LoadBalancer`                  | Kubernetes Service type               |
| `service.port`         | `80`                            | Service port                          |
| `service.targetPort`   | `80`                            | Container port the service targets    |
| `resources`            | `{}`                            | Pod resource requests/limits          |

## Prerequisites

- [Helm](https://helm.sh/) v3+
- Access to a Kubernetes cluster (e.g. Amazon EKS)
- [ArgoCD](https://argo-cd.readthedocs.io/) installed on the cluster (for GitOps deployment)

## Local testing (optional, before pushing to Git)

Render the templates and validate them without installing anything:

```bash
helm template helm-argo-eks . --debug
```

Perform a dry-run install against your current kube context:

```bash
helm install helm-argo-eks . --dry-run --debug
```

Install for real:

```bash
helm install helm-argo-eks .
```

Override values at install time, e.g. to change the replica count or image tag:

```bash
helm install helm-argo-eks . --set replicaCount=3 --set image.tag=v2
```

## Deploy via ArgoCD

Create an ArgoCD Application pointing at this repository:

- **Repository URL**: this Git repo
- **Path**: `.` (repo root, where `Chart.yaml` lives)
- **Target revision**: `main` (or your desired branch/tag)
- **Source type**: Helm
- **Destination namespace**: `default`

Example `Application` manifest:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: helm-argo-eks
  namespace: argocd
spec:
  project: default
  source:
    repoURL: <this-repo-url>
    targetRevision: main
    path: .
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## Verifying the deployment

After ArgoCD syncs (or after a manual `helm install`), check status with:

```bash
kubectl get pods -l app=helm-argo-eks
kubectl get svc helm-argo-eks-svc
```

If `service.type` is `LoadBalancer`, retrieve the external endpoint with:

```bash
kubectl get svc helm-argo-eks-svc -o wide
```
