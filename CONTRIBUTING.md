# Contributing to helm-argo-eks

Thanks for your interest in contributing to this Helm chart! This repo
contains a minimal chart used to deploy a simple app to Amazon EKS via
ArgoCD GitOps.

## Getting started

1. Fork the repository and clone your fork.
2. Create a feature branch off `main`:

   ```bash
   git checkout -b feature/my-change
   ```

3. Make sure you have [Helm](https://helm.sh/) v3+ and `kubectl` installed
   locally, with access to a Kubernetes cluster if you want to test a real
   install.

## Making changes

- Keep changes focused and minimal — one logical change per pull request.
- If you add or rename a value in `values.yaml`, update `README.md` to
  document it.
- If you add a new template, keep it consistent with the existing style
  (use `{{ .Chart.Name }}` for resource naming, `{{ .Values.* }}` for
  configuration).

## Validating your changes

Before opening a pull request, validate the chart locally:

```bash
# Lint the chart
helm lint .

# Render templates and inspect the output
helm template helm-argo-eks . --debug

# Dry-run install against your current kube context
helm install helm-argo-eks . --dry-run --debug
```

If you have a real cluster available, verify an actual install/upgrade:

```bash
helm upgrade --install helm-argo-eks .
kubectl get pods -l app=helm-argo-eks
kubectl get svc helm-argo-eks-svc
```

## Commit messages

Write clear, descriptive commit messages that explain **what** changed and
**why**. Reference related issues where applicable.

## Submitting a pull request

1. Push your branch and open a pull request against `main`.
2. Fill in a clear description of the change and how you tested it.
3. Ensure `helm lint .` passes and the PR has no unrelated changes.
4. A code owner (see `CODEOWNERS`) will review and may request changes.

## Reporting issues

If you find a bug or have a feature request, please open an issue with:

- A clear description of the problem or request
- Steps to reproduce (for bugs)
- Relevant chart version / Kubernetes version

## Code of conduct

Be respectful and constructive in all interactions within this project.
