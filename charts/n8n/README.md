# n8n

![n8n](https://raw.githubusercontent.com/n8n-io/n8n-docs/refs/heads/main/docs/_images/n8n-docs-withWordmark.svg)

A Helm chart for fair-code workflow automation platform with native AI capabilities. Combine visual building with custom code, self-host or cloud, 400+ integrations.

![Version: 1.4.0](https://img.shields.io/badge/Version-1.4.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 1.83.2](https://img.shields.io/badge/AppVersion-1.83.2-informational?style=flat-square)

## Get Helm Repository Info

```console
helm repo add community-charts https://community-charts.github.io/helm-charts
helm repo update
```

_See [`helm repo`](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

```console
helm install [RELEASE_NAME] community-charts/n8n
```

_See [configuration](#configuration) below._

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

> **Tip**: Search all available chart versions using `helm search repo community-charts -l`. Please don't forget to run `helm repo update` before the command.

## Full Example

```yaml
log:
  level: warn

db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"

main:
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 512m
      memory: 512Mi

worker:
  mode: queue
  autoscaling:
    enabled: true
  resources:
    requests:
      cpu: 1000m
      memory: 250Mi
    limits:
      cpu: 2000m
      memory: 2Gi

externalRedis:
  host: "redis-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "default"
  password: "Pa33w0rd!"

ingress:
  enabled: true
  hosts:
    - host: n8n.mydomain.com
      paths:
        - path: /
          pathType: Prefix

webhook:
  mode: queue
  url: "https://webhook.mydomain.com"
  autoscaling:
    enabled: true
  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 512m
      memory: 512Mi
```

## Basic Deployment with Ingress

```yaml
ingress:
  enabled: true
  hosts:
    - host: n8n.mydomain.com
      paths:
        - path: /
          pathType: ImplementationSpecific
```

## Deployment with Bitnami's PostgreSQL

```yaml
db:
  type: postgresdb

postgresql:
  enabled: true
 
  primary:
    persistence:
      existingClaim: "my-n8n-claim"
```

## Deployment with External PostgreSQL

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"
```

## Deployment with External PostgreSQL and Exist Secret on Kubernetes

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  database: "n8n"

  existingSecret: "my-k8s-secret-contains-postgres-password-key-and-credential"
```

## Queue Mode with Bitnami's Redis

> **Tip**: Queue mode doesn't work with default SQLite mode

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"

worker:
  mode: queue

redis:
  enabled: true
```

## Queue Mode with External Redis

> **Tip**: Queue mode doesn't work with default SQLite mode

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"

worker:
  mode: queue

externalRedis:
  host: "redis-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "default"
  password: "Pa33w0rd!"
```

## Queue Mode with External Redis and Exist Secret on Kubernetes

> **Tip**: Queue mode doesn't work with default SQLite mode

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  database: "n8n"

  existingSecret: "my-k8s-secret-contains-postgres-password-key-and-credential"

worker:
  mode: queue

externalRedis:
  host: "redis-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "default"

  existingSecret: "my-k8s-secret-contains-redis-password-key-and-credential"
```

## Webhook Node Deployment

> **Tip**: Webhook needs PostgreSQL backend and Redis based queue mode.

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"

worker:
  mode: queue

externalRedis:
  host: "redis-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "default"
  password: "Pa33w0rd!"

ingress:
  enabled: true
  hosts:
    - host: n8n.mydomain.com
      paths:
        - path: /
          pathType: Prefix

webhook:
  mode: queue

  url: "https://webhook.mydomain.com"
```

## External Task Runner Example

Please find more detail about external task runners from [here](https://docs.n8n.io/hosting/securing/hardening-task-runners/).

```yaml
taskRunners:
  mode: external
```

## Autoscaling Configuration 

> **Note:** The `autoscaling` and `allNodes` options cannot be enabled simultaneously. 

### Deploying Worker and Webhook Pods on All Nodes 

To deploy worker or webhook pods on all Kubernetes nodes, set the `allNodes` flag to `true`: 

```yaml
db:
  type: postgresdb

worker:
  mode: queue
  allNodes: true

webhook:
  mode: queue
  allNodes: true
```

### Enabling Autoscaling 

To enable autoscaling, set the `enabled` field under `autoscaling` to `true`: 

```yaml
db:
  type: postgresdb

worker:
  mode: queue
  autoscaling:
    enabled: true

webhook:
  mode: queue
  autoscaling:
    enabled: true
```

Ensure that you configure either `allNodes` or `autoscaling` based on your deployment requirements.

## Service Monitor Examples

The n8n Helm chart supports optional integration with Prometheus via `ServiceMonitor` and `PodMonitor` resources, compatible with the Prometheus Operator (API version `monitoring.coreos.com/v1`). These resources allow Prometheus to scrape metrics from n8n services and worker pods. This section provides examples to help you enable and configure monitoring.

### Prerequisites

* The Prometheus Operator must be installed in your cluster.
* Your Prometheus instance must be configured to discover `ServiceMonitor` and `PodMonitor` resources (e.g., via a matching `release` label).

### Enabling the ServiceMonitor

To enable monitoring, set `serviceMonitor.enabled` to `true` in your `values.yaml` file or via a Helm override. By default, the `ServiceMonitor` is disabled (`false`).

#### Basic Example

Enable the `ServiceMonitor` with default settings:

```yaml
serviceMonitor:
  enabled: true
```

This deploys a `ServiceMonitor` in the same namespace as the chart (e.g., `n8n`), scraping the n8n service's `/metrics` endpoint every 30 seconds with a 10-second timeout. The default label `release: prometheus` is applied, which should match your Prometheus instance's service monitor selector.

#### Custom Namespace and Interval

Deploy the `ServiceMonitor` in a specific namespace (e.g., `monitoring`) with a custom scrape interval:

```yaml
serviceMonitor:
  enabled: true
  namespace: monitoring
  interval: 1m
```

This scrapes metrics every minute from the n8n service in the release namespace (e.g., `n8n`), with the `ServiceMonitor` itself residing in the `monitoring` namespace.

#### Relabeling Metrics

Add custom metric relabeling to drop unwanted labels:

```yaml
serviceMonitor:
  enabled: true
  labels:
    release: my-prometheus
  metricRelabelings:
    - regex: prometheus_replica
      action: labeldrop
```

This deploys a `ServiceMonitor` with a custom `release: my-prometheus` label and drops the `prometheus_replica` label from scraped metrics.

### Enabling the PodMonitor (Worker Mode)

If using a PostgreSQL database (`db.type: postgresdb`) and queue mode (`worker.mode: queue`), a `PodMonitor` is available to scrape metrics from n8n worker pods.

#### Worker Monitoring Example

Enable both `ServiceMonitor` and `PodMonitor` for a full setup:

```yaml
db:
  type: postgresdb

externalPostgresql:
  host: "postgresql-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "n8nuser"
  password: "Pa33w0rd!"
  database: "n8n"

worker:
  mode: queue

externalRedis:
  host: "redis-instance1.ab012cdefghi.eu-central-1.rds.amazonaws.com"
  username: "default"
  password: "Pa33w0rd!"

serviceMonitor:
  enabled: true
  interval: 15s
  timeout: 5s
```

This scrapes the main n8n service and worker pods every 15 seconds with a 5-second timeout. The `PodMonitor` targets worker pods with the label `role: worker`.

### Troubleshooting

* Ensure the `release` label matches your Prometheus configuration (see [Prometheus Operator docs](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/platform/troubleshooting.md#troubleshooting-servicemonitor-changes)).
* Verify RBAC permissions for the Prometheus service account (e.g., system:serviceaccount:monitoring:prometheus-k8s) to list/watch Pods, Services, and Endpoints. You can use something similar as the following command.

```bash
kubectl auth can-i list pods --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s
kubectl auth can-i list services --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s
kubectl auth can-i list endpoints --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s

kubectl auth can-i watch pods --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s
kubectl auth can-i watch services --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s
kubectl auth can-i watch endpoints --namespace n8n --as=system:serviceaccount:monitoring:prometheus-k8s
```

## Upgrading

This section outlines major updates and breaking changes for each version of the Helm Chart to help you transition smoothly between releases.

---

###  Version-Specific Upgrade Notes

#### Upgrading to Version 1.3.1

##### Deprecation Notices

* **Secret Name Change**: The secret `RELEASE_NAME-encryption-key-secret` is deprecated. Starting with this release, the chart now uses `RELEASE_NAME-encryption-key-secret-v2` to manage the `N8N_ENCRYPTION_KEY`. This change ensures compatibility with Helm’s resource ownership model and resolves previous upgrade issues.

##### Actions Required

* **Verify the Upgrade**: After successfully upgrading to version 1.3.1, confirm that your `n8n` deployment is using the new secret (`RELEASE_NAME-encryption-key-secret-v2`). The old secret (`RELEASE_NAME-encryption-key-secret`) is preserved in the namespace and will not be automatically deleted.
* **Optional Cleanup**: If the old secret is no longer needed, you may manually remove it from your namespace using the following command:
bash

```bash
kubectl delete secret RELEASE_NAME-encryption-key-secret -n YOUR_NAMESPACE
```

Replace `RELEASE_NAME` and `YOUR_NAMESPACE` with your specific release name and namespace.

#### Upgrading to Version 1.2.x

##### Deprecation Notices

- The top-level fields `extraEnvVars`, `extraSecretNamesForEnvFrom`, `resources`, `volumes` and `volumeMounts` are deprecated.

##### Action Required

Please consider using the corresponding fields in the `main`, `worker` and `webhook` blocks instead. The deprecated fields will be removed in the next major release.

#### Upgrading to Version 1.x.x

##### Breaking Changes

- The `diagnostics.externalTaskRunnersSentryDsn` setting has been relocated to `sentry.externalTaskRunnersDsn`.

##### Action Required

If you previously configured `diagnostics.externalTaskRunnersSentryDsn`, update your configuration to use `sentry.externalTaskRunnersDsn`. Ensure that any associated flags are enabled as needed.

## Requirements

Kubernetes: `>=1.23.0-0`

| Repository | Name | Version |
|------------|------|---------|
| https://charts.bitnami.com/bitnami | postgresql | 16.5.2 |
| https://charts.bitnami.com/bitnami | redis | 20.11.3 |

## Uninstall Helm Chart

```console
helm uninstall [RELEASE_NAME]
```

This removes all the Kubernetes components associated with the chart and deletes the release.

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

## Upgrading Chart

```console
helm upgrade [RELEASE_NAME] community-charts/n8n
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | For more information checkout: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity |
| api.enabled | bool | `true` | Whether to enable the Public API |
| api.path | string | `"api"` | Path segment for the Public API |
| api.swagger | object | `{"enabled":true}` | Whether to enable the Swagger UI for the Public API |
| db | object | `{"logging":{"enabled":false,"maxQueryExecutionTime":0,"options":"error"},"sqlite":{"database":"database.sqlite","poolSize":0,"vacuum":false},"tablePrefix":"","type":"sqlite"}` | n8n database configurations |
| db.logging.enabled | bool | `false` | Whether database logging is enabled. |
| db.logging.maxQueryExecutionTime | int | `0` | Only queries that exceed this time (ms) will be logged. Set `0` to disable. |
| db.logging.options | string | `"error"` | Database logging level. Requires `maxQueryExecutionTime` to be higher than `0`. Valid values 'query' | 'error' | 'schema' | 'warn' | 'info' | 'log' | 'all' |
| db.sqlite.database | string | `"database.sqlite"` | SQLite database file name |
| db.sqlite.poolSize | int | `0` | SQLite database pool size. Set to `0` to disable pooling. |
| db.sqlite.vacuum | bool | `false` | Runs VACUUM operation on startup to rebuild the database. Reduces file size and optimizes indexes. This is a long running blocking operation and increases start-up time. |
| db.tablePrefix | string | `""` | Prefix to use for table names. |
| db.type | string | `"sqlite"` | Type of database to use. Valid values 'sqlite' | 'postgresdb' |
| defaultLocale | string | `"en"` | A locale identifier, compatible with the Accept-Language header. n8n doesn't support regional identifiers, such as de-AT. |
| diagnostics.backendConfig | string | `"1zPn7YoGC3ZXE9zLeTKLuQCB4F6;https://telemetry.n8n.io"` | Diagnostics config for backend. |
| diagnostics.enabled | bool | `false` | Whether diagnostics are enabled. |
| diagnostics.frontendConfig | string | `"1zPn9bgWPzlQc0p8Gj1uiK6DOTn;https://telemetry.n8n.io"` | Diagnostics config for frontend. |
| diagnostics.postHog.apiHost | string | `"https://ph.n8n.io"` | API host for PostHog. |
| diagnostics.postHog.apiKey | string | `"phc_4URIAm1uYfJO7j8kWSe0J8lc8IqnstRLS7Jx8NcakHo"` | API key for PostHog. |
| encryptionKey | string | `""` | If you install n8n first time, you can keep this empty and it will be auto generated and never change again. If you already have a encryption key generated before, please use it here. |
| existingEncryptionKeySecret | string | `""` | The name of an existing secret with encryption key. The secret must contain a key with the name N8N_ENCRYPTION_KEY. |
| externalPostgresql | object | `{"database":"n8n","existingSecret":"","host":"","password":"","port":5432,"username":"postgres"}` | External PostgreSQL parameters |
| externalPostgresql.database | string | `"n8n"` | The name of the external PostgreSQL database. For more information: https://docs.n8n.io/hosting/configuration/supported-databases-settings/#required-permissions |
| externalPostgresql.existingSecret | string | `""` | The name of an existing secret with PostgreSQL (must contain key `postgres-password`) and credentials. When it's set, the `externalPostgresql.password` parameter is ignored |
| externalPostgresql.host | string | `""` | External PostgreSQL server host |
| externalPostgresql.password | string | `""` | External PostgreSQL password |
| externalPostgresql.port | int | `5432` | External PostgreSQL server port |
| externalPostgresql.username | string | `"postgres"` | External PostgreSQL username |
| externalRedis | object | `{"existingSecret":"","host":"","password":"","port":6379,"username":""}` | External Redis parameters |
| externalRedis.existingSecret | string | `""` | The name of an existing secret with Redis (must contain key `redis-password`) and Sentinel credentials. When it's set, the `externalRedis.password` parameter is ignored |
| externalRedis.host | string | `""` | External Redis server host |
| externalRedis.password | string | `""` | External Redis password |
| externalRedis.port | int | `6379` | External Redis server port |
| externalRedis.username | string | `""` | External Redis username |
| extraEnvVars | object | `{}` | DEPRECATED: Use main, worker, and webhook blocks extraEnvVars fields instead. This field will be removed in a future release. |
| extraSecretNamesForEnvFrom | list | `[]` | DEPRECATED: Use main, worker, and webhook blocks extraSecretNamesForEnvFrom fields instead. This field will be removed in a future release. |
| fullnameOverride | string | `""` |  |
| gracefulShutdownTimeout | int | `30` | graceful shutdown timeout in seconds |
| image | object | `{"pullPolicy":"IfNotPresent","repository":"n8nio/n8n","tag":""}` | This sets the container image more information can be found here: https://kubernetes.io/docs/concepts/containers/images/ |
| image.pullPolicy | string | `"IfNotPresent"` | This sets the pull policy for images. |
| image.tag | string | `""` | Overrides the image tag whose default is the chart appVersion. |
| imagePullSecrets | list | `[]` | This is for the secretes for pulling an image from a private repository more information can be found here: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/ |
| ingress | object | `{"annotations":{},"className":"","enabled":false,"hosts":[{"host":"n8n.local","paths":[{"path":"/","pathType":"Prefix"}]}],"tls":[]}` | This block is for setting up the ingress for more information can be found here: https://kubernetes.io/docs/concepts/services-networking/ingress/ |
| livenessProbe | object | `{"httpGet":{"path":"/healthz","port":"http"}}` | This is to setup the liveness probe more information can be found here: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/ |
| log | object | `{"file":{"location":"logs/n8n.log","maxcount":"100","maxsize":16},"level":"info","output":["console"],"scopes":[]}` | n8n log configurations |
| log.file.location | string | `"logs/n8n.log"` | Location of the log files inside `~/.n8n`. Only for `file` log output. |
| log.file.maxcount | string | `"100"` | Max number of log files to keep, or max number of days to keep logs for. Once the limit is reached, the oldest log files will be rotated out. If using days, append a `d` suffix. Only for `file` log output. |
| log.file.maxsize | int | `16` | The maximum size (in MB) for each log file. By default, n8n uses 16 MB. |
| log.level | string | `"info"` | The log output level. The available options are (from lowest to highest level) are error, warn, info, and debug. The default value is info. You can learn more about these options [here](https://docs.n8n.io/hosting/logging-monitoring/logging/#log-levels). |
| log.output | list | `["console"]` | Where to output logs to. Options are: `console` or `file` or both. |
| log.scopes | list | `[]` | Scopes to filter logs by. Nothing is filtered by default. Supported log scopes: concurrency, external-secrets, license, multi-main-setup, pubsub, redis, scaling, waiting-executions |
| main | object | `{"count":1,"extraEnvVars":{},"extraSecretNamesForEnvFrom":[],"pdb":{"enabled":true,"maxUnavailable":null,"minAvailable":1},"resources":{},"volumeMounts":[],"volumes":[]}` | Main node configurations |
| main.count | int | `1` | Number of main nodes. Only enterprise license users can have two main nodes. |
| main.extraEnvVars | object | `{}` | Extra environment variables |
| main.extraSecretNamesForEnvFrom | list | `[]` | Extra secrets for environment variables |
| main.pdb | object | `{"enabled":true,"maxUnavailable":null,"minAvailable":1}` | Whether to enable the PodDisruptionBudget for the main pod. |
| main.pdb.enabled | bool | `true` | Whether to enable the PodDisruptionBudget |
| main.pdb.maxUnavailable | string | `nil` | Maximum number of unavailable replicas |
| main.pdb.minAvailable | int | `1` | Minimum number of available replicas |
| main.resources | object | `{}` | This block is for setting up the resource management for the main pod more information can be found here: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/ |
| main.volumeMounts | list | `[]` | Additional volumeMounts on the output Deployment definition. |
| main.volumes | list | `[]` | Additional volumes on the output Deployment definition. |
| nameOverride | string | `""` | This is to override the chart name. |
| nodeSelector | object | `{}` | For more information checkout: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector |
| podAnnotations | object | `{}` | This is for setting Kubernetes Annotations to a Pod. For more information checkout: https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/ |
| podLabels | object | `{}` | This is for setting Kubernetes Labels to a Pod. For more information checkout: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ |
| podSecurityContext | object | `{"fsGroup":1000,"fsGroupChangePolicy":"OnRootMismatch"}` | This is for setting Security Context to a Pod. For more information checkout: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ |
| postgresql | object | `{"architecture":"standalone","auth":{"database":"n8n","password":"","username":""},"enabled":false,"primary":{"persistence":{"enabled":true,"existingClaim":""},"service":{"ports":{"postgresql":5432}}}}` | Bitnami PostgreSQL configuration |
| postgresql.auth.database | string | `"n8n"` | The name of the PostgreSQL database. For more information: https://docs.n8n.io/hosting/configuration/supported-databases-settings/#required-permissions |
| postgresql.enabled | bool | `false` | Enable postgresql |
| readinessProbe | object | `{"httpGet":{"path":"/healthz/readiness","port":"http"}}` | This is to setup the readiness probe more information can be found here: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/ |
| redis | object | `{"architecture":"standalone","enabled":false,"master":{"persistence":{"enabled":false}}}` | Bitnami Redis configuration |
| redis.enabled | bool | `false` | Enable redis |
| resources | object | `{}` | DEPRECATED: Use main, worker, and webhook blocks resources fields instead. This field will be removed in a future release. |
| securityContext | object | `{"allowPrivilegeEscalation":false,"capabilities":{"drop":["ALL"]},"privileged":false,"readOnlyRootFilesystem":false,"runAsGroup":1000,"runAsNonRoot":true,"runAsUser":1000}` | This is for setting Security Context to a Container. For more information checkout: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ |
| sentry.backendDsn | string | `""` | Sentry DSN for backend. |
| sentry.enabled | bool | `false` | Whether sentry is enabled. |
| sentry.externalTaskRunnersDsn | string | `""` | Sentry DSN for external task runners. |
| sentry.frontendDsn | string | `""` | Sentry DSN for frontend. |
| service | object | `{"annotations":{},"name":"http","port":5678,"type":"ClusterIP"}` | This is for setting up a service more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/ |
| service.annotations | object | `{}` | Additional service annotations |
| service.name | string | `"http"` | Default Service name |
| service.port | int | `5678` | This sets the ports more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#field-spec-ports |
| service.type | string | `"ClusterIP"` | This sets the service type more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types |
| serviceAccount | object | `{"annotations":{},"automount":true,"create":true,"name":""}` | This section builds out the service account more information can be found here: https://kubernetes.io/docs/concepts/security/service-accounts/ |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.automount | bool | `true` | Automatically mount a ServiceAccount's API credentials? |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and create is true, a name is generated using the fullname template |
| serviceMonitor | object | `{"enabled":false,"include":{"apiEndpoints":false,"apiMethodLabel":false,"apiPathLabel":false,"apiStatusCodeLabel":false,"cacheMetrics":false,"credentialTypeLabel":false,"defaultMetrics":true,"messageEventBusMetrics":false,"nodeTypeLabel":false,"queueMetrics":false,"workflowIdLabel":false},"interval":"30s","labels":{"release":"prometheus"},"metricRelabelings":[],"metricsPrefix":"n8n_","namespace":"","targetLabels":[],"timeout":"10s"}` | The service monitor configuration. Please refer to the following link for more information: https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/api-reference/api.md |
| serviceMonitor.enabled | bool | `false` | When set true then use a ServiceMonitor to configure scraping |
| serviceMonitor.include | object | `{"apiEndpoints":false,"apiMethodLabel":false,"apiPathLabel":false,"apiStatusCodeLabel":false,"cacheMetrics":false,"credentialTypeLabel":false,"defaultMetrics":true,"messageEventBusMetrics":false,"nodeTypeLabel":false,"queueMetrics":false,"workflowIdLabel":false}` | Whether to include metrics |
| serviceMonitor.include.apiEndpoints | bool | `false` | Whether to include api endpoints |
| serviceMonitor.include.apiMethodLabel | bool | `false` | Whether to include api method label |
| serviceMonitor.include.apiPathLabel | bool | `false` | Whether to include api path label |
| serviceMonitor.include.apiStatusCodeLabel | bool | `false` | Whether to include api status code label |
| serviceMonitor.include.cacheMetrics | bool | `false` | Whether to include cache metrics |
| serviceMonitor.include.credentialTypeLabel | bool | `false` | Whether to include credential type label |
| serviceMonitor.include.defaultMetrics | bool | `true` | Whether to include default metrics |
| serviceMonitor.include.messageEventBusMetrics | bool | `false` | Whether to include message event bus metrics |
| serviceMonitor.include.nodeTypeLabel | bool | `false` | Whether to include node type label |
| serviceMonitor.include.queueMetrics | bool | `false` | Whether to include queue metrics |
| serviceMonitor.include.workflowIdLabel | bool | `false` | Whether to include workflow id label |
| serviceMonitor.interval | string | `"30s"` | Set how frequently Prometheus should scrape |
| serviceMonitor.labels | object | `{"release":"prometheus"}` | Set labels for the ServiceMonitor, use this to define your scrape label for Prometheus Operator |
| serviceMonitor.labels.release | string | `"prometheus"` | default `kube prometheus stack` helm chart serviceMonitor selector label Mostly it's your prometheus helm release name. Please find more information from here: https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/platform/troubleshooting.md#troubleshooting-servicemonitor-changes |
| serviceMonitor.metricRelabelings | list | `[]` | Set of rules to relabel your exist metric labels |
| serviceMonitor.metricsPrefix | string | `"n8n_"` | The prefix for the metrics |
| serviceMonitor.namespace | string | `""` | Set the namespace the ServiceMonitor should be deployed. If empty, the ServiceMonitor will be deployed in the same namespace as the n8n chart. |
| serviceMonitor.targetLabels | list | `[]` | Set of labels to transfer on the Kubernetes Service onto the target. |
| serviceMonitor.timeout | string | `"10s"` | Set timeout for scrape |
| strategy | object | `{"rollingUpdate":{"maxSurge":"25%","maxUnavailable":"25%"},"type":"RollingUpdate"}` | This will set the deployment strategy more information can be found here: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy |
| taskRunners | object | `{"broker":{"address":"127.0.0.1","port":5679},"external":{"autoShutdownTimeout":15,"mainNodeAuthToken":"","nodeOptions":["--max-semi-space-size=16","--max-old-space-size=300"],"port":5680,"resources":{"limits":{"cpu":"2000m","memory":"512Mi"},"requests":{"cpu":"100m","memory":"32Mi"}},"workerNodeAuthToken":""},"maxConcurrency":5,"mode":"internal","taskHeartbeatInterval":30,"taskTimeout":60}` | Task runners mode. Please follow the documentation for more information: https://docs.n8n.io/hosting/configuration/task-runners/ |
| taskRunners.broker | object | `{"address":"127.0.0.1","port":5679}` | The address for the broker of the external task runner |
| taskRunners.broker.address | string | `"127.0.0.1"` | The address for the broker of the external task runner |
| taskRunners.broker.port | int | `5679` | The port for the broker of the external task runner |
| taskRunners.external | object | `{"autoShutdownTimeout":15,"mainNodeAuthToken":"","nodeOptions":["--max-semi-space-size=16","--max-old-space-size=300"],"port":5680,"resources":{"limits":{"cpu":"2000m","memory":"512Mi"},"requests":{"cpu":"100m","memory":"32Mi"}},"workerNodeAuthToken":""}` | The configuration for the external task runner |
| taskRunners.external.autoShutdownTimeout | int | `15` | The auto shutdown timeout for the external task runner in seconds |
| taskRunners.external.mainNodeAuthToken | string | `""` | The auth token for the main node |
| taskRunners.external.nodeOptions | list | `["--max-semi-space-size=16","--max-old-space-size=300"]` | The node options for the external task runner |
| taskRunners.external.port | int | `5680` | The port for the external task runner |
| taskRunners.external.resources | object | `{"limits":{"cpu":"2000m","memory":"512Mi"},"requests":{"cpu":"100m","memory":"32Mi"}}` | The resources for the external task runner |
| taskRunners.external.resources.limits | object | `{"cpu":"2000m","memory":"512Mi"}` | The limits for the external task runner |
| taskRunners.external.resources.limits.cpu | string | `"2000m"` | The CPU limit for the external task runner |
| taskRunners.external.resources.limits.memory | string | `"512Mi"` | The memory limit for the external task runner |
| taskRunners.external.resources.requests | object | `{"cpu":"100m","memory":"32Mi"}` | The resources requests for the external task runner |
| taskRunners.external.resources.requests.cpu | string | `"100m"` | The CPU request for the external task runner |
| taskRunners.external.resources.requests.memory | string | `"32Mi"` | The memory request for the external task runner |
| taskRunners.external.workerNodeAuthToken | string | `""` | The auth token for the worker node |
| taskRunners.maxConcurrency | int | `5` | The maximum concurrency for the task |
| taskRunners.mode | string | `"internal"` | Use `internal` to use internal task runner, or use `external` to have external sidecar task runner. For more information please follow the documentation: https://docs.n8n.io/hosting/configuration/task-runners/#task-runner-modes |
| taskRunners.taskHeartbeatInterval | int | `30` | The heartbeat interval for the task in seconds |
| taskRunners.taskTimeout | int | `60` | The timeout for the task in seconds |
| timezone | string | `"Europe/Berlin"` | For instance, the Schedule node uses it to know at what time the workflow should start. Find you timezone from here: https://momentjs.com/timezone/ |
| tolerations | list | `[]` | For more information checkout: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/ |
| versionNotifications.enabled | bool | `false` | Whether to request notifications about new n8n versions |
| versionNotifications.endpoint | string | `"https://api.n8n.io/api/versions/"` | Endpoint to retrieve n8n version information from |
| versionNotifications.infoUrl | string | `"https://docs.n8n.io/hosting/installation/updating/"` | URL for versions panel to page instructing user on how to update n8n instance |
| volumeMounts | list | `[]` | DEPRECATED: Use main, worker, and webhook blocks volumeMounts fields instead. This field will be removed in a future release. |
| volumes | list | `[]` | DEPRECATED: Use main, worker, and webhook blocks volumes fields instead. This field will be removed in a future release. |
| webhook | object | `{"allNodes":false,"autoscaling":{"behavior":{},"enabled":false,"maxReplicas":10,"metrics":[{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}],"minReplicas":2},"count":2,"extraEnvVars":{},"extraSecretNamesForEnvFrom":[],"mode":"regular","pdb":{"enabled":true,"maxUnavailable":null,"minAvailable":1},"resources":{},"url":"","volumeMounts":[],"volumes":[]}` | Webhook node configurations |
| webhook.allNodes | bool | `false` | If true, all k8s nodes will deploy exatly one webhook pod |
| webhook.autoscaling | object | `{"behavior":{},"enabled":false,"maxReplicas":10,"metrics":[{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}],"minReplicas":2}` | If true, the number of webhooks will be automatically scaled based on default metrics. On default, it will scale based on CPU. Scale by requests can be done by setting a custom metric. For more information can be found here: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/ |
| webhook.autoscaling.behavior | object | `{}` | The behavior of the autoscaler. |
| webhook.autoscaling.enabled | bool | `false` | Whether autoscaling is enabled. |
| webhook.autoscaling.maxReplicas | int | `10` | The maximum number of replicas. |
| webhook.autoscaling.metrics | list | `[{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}]` | The metrics to use for autoscaling. |
| webhook.autoscaling.minReplicas | int | `2` | The minimum number of replicas. |
| webhook.count | int | `2` | Static number of webhooks. If allNodes or autoscaling is enabled, this value will be ignored. |
| webhook.extraEnvVars | object | `{}` | Extra environment variables |
| webhook.extraSecretNamesForEnvFrom | list | `[]` | Extra secrets for environment variables |
| webhook.mode | string | `"regular"` | Use `regular` to use main node as webhook node, or use `queue` to have webhook nodes |
| webhook.pdb | object | `{"enabled":true,"maxUnavailable":null,"minAvailable":1}` | Whether to enable the PodDisruptionBudget for the webhook pod |
| webhook.pdb.enabled | bool | `true` | Whether to enable the PodDisruptionBudget |
| webhook.pdb.maxUnavailable | string | `nil` | Maximum number of unavailable replicas |
| webhook.pdb.minAvailable | int | `1` | Minimum number of available replicas |
| webhook.resources | object | `{}` | This block is for setting up the resource management for the webhook pod more information can be found here: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/ |
| webhook.url | string | `""` | Webhook url together with http or https schema |
| webhook.volumeMounts | list | `[]` | Additional volumeMounts on the output Deployment definition. |
| webhook.volumes | list | `[]` | Additional volumes on the output Deployment definition. |
| worker | object | `{"allNodes":false,"autoscaling":{"behavior":{},"enabled":false,"maxReplicas":10,"metrics":[{"resource":{"name":"memory","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"},{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}],"minReplicas":2},"concurrency":10,"count":2,"extraEnvVars":{},"extraSecretNamesForEnvFrom":[],"mode":"regular","pdb":{"enabled":true,"maxUnavailable":null,"minAvailable":1},"resources":{},"volumeMounts":[],"volumes":[]}` | Worker node configurations |
| worker.allNodes | bool | `false` | If true, all k8s nodes will deploy exatly one worker pod |
| worker.autoscaling | object | `{"behavior":{},"enabled":false,"maxReplicas":10,"metrics":[{"resource":{"name":"memory","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"},{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}],"minReplicas":2}` | If true, the number of workers will be automatically scaled based on default metrics. On default, it will scale based on CPU and memory. For more information can be found here: https://kubernetes.io/docs/concepts/workloads/autoscaling/ |
| worker.autoscaling.behavior | object | `{}` | The behavior of the autoscaler. |
| worker.autoscaling.enabled | bool | `false` | Whether autoscaling is enabled. |
| worker.autoscaling.maxReplicas | int | `10` | The maximum number of replicas. |
| worker.autoscaling.metrics | list | `[{"resource":{"name":"memory","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"},{"resource":{"name":"cpu","target":{"averageUtilization":80,"type":"Utilization"}},"type":"Resource"}]` | The metrics to use for autoscaling. |
| worker.autoscaling.minReplicas | int | `2` | The minimum number of replicas. |
| worker.concurrency | int | `10` | number of concurrency for each worker |
| worker.count | int | `2` | Static number of workers. If allNodes or autoscaling is enabled, this value will be ignored. |
| worker.extraEnvVars | object | `{}` | Extra environment variables |
| worker.extraSecretNamesForEnvFrom | list | `[]` | Extra secrets for environment variables |
| worker.mode | string | `"regular"` | Use `regular` to use main node as executer, or use `queue` to have worker nodes |
| worker.pdb | object | `{"enabled":true,"maxUnavailable":null,"minAvailable":1}` | Whether to enable the PodDisruptionBudget for the worker pod |
| worker.pdb.enabled | bool | `true` | Whether to enable the PodDisruptionBudget |
| worker.pdb.maxUnavailable | string | `nil` | Maximum number of unavailable replicas |
| worker.pdb.minAvailable | int | `1` | Minimum number of available replicas |
| worker.resources | object | `{}` | This block is for setting up the resource management for the worker pod more information can be found here: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/ |
| worker.volumeMounts | list | `[]` | Additional volumeMounts on the output Deployment definition. |
| worker.volumes | list | `[]` | Additional volumes on the output Deployment definition. |
| workflowHistory | object | `{"enabled":true,"pruneTime":336}` | The workflow history configuration |
| workflowHistory.enabled | bool | `true` | Whether to save workflow history versions |
| workflowHistory.pruneTime | int | `336` | Time (in hours) to keep workflow history versions for. To disable it, use -1 as a value |

**Homepage:** <https://n8n.io>

## Source Code

* <https://github.com/community-charts/helm-charts>
* <https://github.com/n8n-io/n8n>

## Chart Development

Please install unittest helm plugin with `helm plugin install https://github.com/helm-unittest/helm-unittest.git` command and use following command to run helm unit tests.

```console
helm unittest --strict --file 'unittests/**/*.yaml' charts/n8n
```

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| burakince | <burak.ince@linux.org.tr> | <https://www.burakince.com> |
