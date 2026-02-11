Создай стэк приложений для ArgoCD по типу App of Apps:

1. Cert Manager. namespace cert-manager
2. Letsencrypt ClusterIssuer(prod и staging). Email: vakefuder@gmail.com
3. Gitea. Helm repo chart https://dl.gitea.com/charts/. Ingress gitea.malefstorm.ru.
Admin password: ViVa1994. Admin username: malef. Admin email: vakfuder@gmail.com
Disable valkey-cluster, postgresql-ha. Enable valkey, postgresql
4. Kube-prometheus-stack. Grafana Ingress grafana.malefstorm.ru. Admin Password prom-operator. namespace gitea
5. Ingress-nginx. namespace: ingress-nginx
6. Intsall loki-stack. storageClassName: local-path. pvc name loki