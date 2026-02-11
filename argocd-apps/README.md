# ArgoCD App of Apps Stack

This repository contains an ArgoCD App of Apps configuration for deploying a complete Kubernetes application stack.

## Structure

```
argocd-apps/
├── apps/
│   ├── root-app-of-apps.yaml    # Root application that deploys the app-of-apps
│   └── app-of-apps.yaml          # ApplicationSet that deploys all child applications
├── base/
│   └── namespaces.yaml           # Namespace definitions
├── cert-manager/
│   └── cert-manager.yaml         # Cert Manager application
├── letsencrypt/
│   ├── letsencrypt.yaml          # Letsencrypt ClusterIssuer application
│   └── resources/
│       └── cluster-issuers.yaml  # ClusterIssuer manifests (prod and staging)
├── gitea/
│   └── gitea.yaml                # Gitea application with custom values
├── kube-prometheus/
│   └── kube-prometheus.yaml      # Kube-prometheus-stack with Grafana
├── ingress-nginx/
│   └── ingress-nginx.yaml        # Ingress-nginx controller
└── loki/
    ├── loki.yaml                 # Loki-stack application
    └── pvc.yaml                  # PersistentVolumeClaim for Loki
```

## Applications

### 1. Cert Manager
- **Namespace**: `cert-manager`
- **Chart**: `jetstack/cert-manager` (v1.15.1)
- **Description**: Manages SSL/TLS certificates

### 2. Letsencrypt ClusterIssuers
- **Namespace**: `cert-manager`
- **Email**: `vakefuder@gmail.com`
- **Issuers**:
  - `letsencrypt-staging`: For testing
  - `letsencrypt-prod`: For production certificates

### 3. Gitea
- **Namespace**: `gitea`
- **Chart**: `gitea` (v10.6.0) from `https://dl.gitea.com/charts/`
- **Ingress**: `gitea.malefstorm.ru`
- **Admin Credentials**:
  - Username: `malef`
  - Password: `ViVa1994`
  - Email: `vakfuder@gmail.com`
- **Configuration**:
  - Enabled: `valkey`, `postgresql`
  - Disabled: `valkey-cluster`, `postgresql-ha`

### 4. Kube-prometheus-stack
- **Namespace**: `gitea`
- **Chart**: `kube-prometheus-stack` (v66.2.1)
- **Grafana**:
  - Ingress: `grafana.malefstorm.ru`
  - Admin Password: `prom-operator`
- **Components**: Prometheus, Grafana, Alertmanager, Node Exporter, Kube State Metrics

### 5. Ingress-nginx
- **Namespace**: `ingress-nginx`
- **Chart**: `ingress-nginx` (v4.11.3)
- **Type**: LoadBalancer with externalTrafficPolicy: Local

### 6. Loki-stack
- **Namespace**: `loki`
- **Chart**: `loki-stack` (v2.10.2)
- **Storage**: `local-path` storage class, 50Gi PVC named `loki`
- **Components**: Loki, Promtail

## Deployment Order

The applications are deployed in the following order (defined in `app-of-apps.yaml`):

1. Namespaces
2. Ingress-nginx
3. Cert Manager
4. Letsencrypt ClusterIssuers
5. Gitea
6. Kube-prometheus-stack
7. Loki-stack

## Usage

### Prerequisites

1. ArgoCD installed in your cluster
2. Git repository: `https://github.com/LeoEliot/argocd`

### Installation

1. Fork/clone this repository
2. The `repoURL` is already set to `https://github.com/LeoEliot/argocd`
3. Apply the root application:

```bash
kubectl apply -f argocd-apps/apps/root-app-of-apps.yaml
```

4. The App of Apps will automatically deploy all child applications

### Configuration Updates

To update any application:
1. Modify the values in the respective `*.yaml` file
2. Commit and push to your Git repository
3. ArgoCD will automatically sync the changes

## Notes

- All applications use automated sync with `prune` and `selfHeal` enabled
- The `local-path` storage class is used for all persistent storage
- The `repoURL` is configured to `https://github.com/LeoEliot/argocd`
- Consider using Kustomize or Helm for more complex value management
