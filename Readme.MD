# Prometheus Helm Chart

This Helm chart deploys a [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator) `Prometheus` custom resource, along with optional ServiceAccount, RBAC (Role or ClusterRole), and binding configurations.

---

## 🚀 Installation

```bash
helm install prometheus-crds ./helm/prometheus-crds
```


To install with custom values:
```bash
helm install prometheus-crds ./helm/prometheus-crds -f ./custom-values.yaml
```

📦 Parameters
📌 Top-level
| Name          | Type   | Default           | Description                            |
| ------------- | ------ | ----------------- | -------------------------------------- |
| `name`        | string | `prometheus-eks2` | Name of the Prometheus resource        |
| `namespace`   | string | `infra`           | Kubernetes namespace for all resources |
| `replicas`    | int    | `1`               | Number of Prometheus replicas          |
| `clusterName` | string | `eks-2`           | Cluster name for external labels       |

🏷️ Labels
| Name                | Type   | Default           | Description                         |
| ------------------- | ------ | ----------------- | ----------------------------------- |
| `labels.prometheus` | string | `prometheus-eks2` | Label used for Prometheus selection |
| `labels.app`        | string | `prometheus-eks2` | App label added to pods             |

👤 ServiceAccount
| Name                            | Type   | Default      | Description                                    |
| ------------------------------- | ------ | ------------ | ---------------------------------------------- |
| `serviceAccount.name`           | string | `prometheus` | Name of the ServiceAccount                     |
| `serviceAccount.labels`         | object | `{}`         | Additional labels for the ServiceAccount       |
| `serviceAccount.annotations`    | object | `{}`         | Annotations for the ServiceAccount             |
| `serviceAccount.automountToken` | bool   | `true`       | Whether to automount the service account token |

⚠️ Alerting Configuration
| Name                     | Type | Default           | Description                    |
| ------------------------ | ---- | ----------------- | ------------------------------ |
| `alerting.alertmanagers` | list | see example below | List of Alertmanager endpoints |


```yaml

alerting:
  alertmanagers:
    - name: alertmanager
      namespace: default
      port: web

```

📡 Additional Scrape Configs (optional)

| Name                           | Type   | Default | Description                                       |
| ------------------------------ | ------ | ------- | ------------------------------------------------- |
| `additionalScrapeConfigs.name` | string | —       | Name of the Secret containing additional configs  |
| `additionalScrapeConfigs.key`  | string | —       | Key inside the Secret holding YAML scrape configs |

This section is optional — omit to disable.

🔐 RBAC (Role/ClusterRole and Binding)
| Name                              | Type   | Default                          | Description                                             |
| --------------------------------- | ------ | -------------------------------- | ------------------------------------------------------- |
| `serviceAccount.rbac.enabled`     | bool   | `true`                           | Whether to create Role/ClusterRole and Binding          |
| `serviceAccount.rbac.kind`        | string | `ClusterRole`                    | Use `Role` (namespaced) or `ClusterRole` (cluster-wide) |
| `serviceAccount.rbac.name`        | string | `prometheus-cluster-role`        | Name of the Role/ClusterRole                            |
| `serviceAccount.rbac.bindingName` | string | `prometheus-cluster-rolebinding` | Name of the binding                                     |
| `serviceAccount.rbac.rules`       | list   | see example below                | Rules granted to Prometheus via the role                |


```yaml
serviceAccount:
  rbac:
    enabled: true
    kind: ClusterRole
    name: prometheus-cluster-role
    bindingName: prometheus-cluster-rolebinding
    rules:
      - apiGroups: [""]
        resources: ["pods", "services", "endpoints"]
        verbs: ["get", "list", "watch"]

```

📁 Template Files

This chart includes the following templates:

    - templates/po-instance.yaml – Prometheus CRD
    - templates/po-sa.yaml – ServiceAccount (optional)
    - templates/po-clusterrole.yaml – Role or ClusterRole (optional)
    - templates/po-clusterrolebinding.yaml – RoleBinding or ClusterRoleBinding (optional)

📌 Notes

    This chart assumes the Prometheus Operator CRDs (like Prometheus) are already installed.

    You can toggle between namespace-scoped RBAC (Role) and cluster-wide (ClusterRole) with rbac.kind.



🧪 Testing the Chart

To test rendering the chart:

```bash
helm template my-prometheus ./my-prometheus-chart
```

