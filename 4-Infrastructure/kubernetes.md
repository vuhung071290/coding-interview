# Kubernetes for Backend Engineers

> Container orchestration cho production-scale backend systems

## 1. Core Architecture

### Control Plane (Master)
- **API Server:** gateway duy nhất, validate & persist state vào etcd
- **etcd:** distributed key-value store — source of truth cho cluster state
- **Scheduler:** quyết định Pod chạy trên Node nào (resource fit + affinity)
- **Controller Manager:** watch state → reconcile (Deployment, ReplicaSet controllers)

### Worker Node
- **kubelet:** agent trên mỗi node, chạy Pods, report health
- **kube-proxy:** network rules, load balancing cho Services (iptables/ipvs)
- **Container Runtime:** containerd hoặc CRI-O (không còn Docker runtime)

**Key insight:** K8s = declarative desired state → controllers liên tục reconcile actual state về desired state

---

## 2. Core Workload Objects

### Pod
- Đơn vị nhỏ nhất — 1+ containers chia sẻ network namespace + volumes
- **Không tạo Pod trực tiếp** — dùng Deployment/StatefulSet
- Có IP riêng, ephemeral (mất khi Node chết)

### Deployment (stateless apps)
- Desired state: `replicas: 3`, container image, resource limits
- Rolling update strategy (default): `maxSurge: 1`, `maxUnavailable: 0`
- Key config: immutable image tag (never `latest`), resource `requests` + `limits`
- Health: `livenessProbe` → restart on fail; `readinessProbe` → remove from Service endpoints

### StatefulSet (stateful apps)
- Pods có tên ổn định (pod-0, pod-1, pod-2)
- Stable network identity per pod
- Ordered, graceful deploy/scale (pod-0 → pod-1 → pod-2)
- Dùng cho: MySQL, Kafka, ZooKeeper, Elasticsearch

### DaemonSet
- Chạy 1 Pod trên mỗi Node
- Dùng cho: log collectors (Fluentd), monitoring agents (Prometheus node-exporter)

### Job / CronJob
- **Job:** chạy 1 lần đến completion (migration, batch)
- **CronJob:** scheduled jobs (cleanup, report generation)

---

## 3. Networking

### Service Types
| Type | Access | Use When |
|------|--------|----------|
| ClusterIP (default) | Internal only, stable virtual IP | Service-to-service |
| NodePort | External via Node IP:Port | Dev/testing |
| LoadBalancer | Cloud LB (AWS ALB/NLB) | Production external traffic |
| ExternalName | DNS alias for external service | Access external systems |

### Ingress
- L7 HTTP routing — route by host/path → Service
- Needs **Ingress Controller** (nginx-ingress, AWS ALB Ingress Controller, Traefik)
- Config: `host: api.example.com`, `path: /v1`, `backend: service-name:port`

### DNS in K8s
- Each Service: `<service>.<namespace>.svc.cluster.local`
- Same namespace: use short `<service>`
- Cross-namespace: `<service>.<namespace>`

### NetworkPolicy
- Default: all traffic allowed
- NetworkPolicy restricts ingress/egress by label selectors
- Needs CNI plugin (Calico, Cilium)

---

## 4. Configuration & Storage

### ConfigMap — non-sensitive config
- Key-value config data
- Mount as: environment variables (`envFrom: configMapRef`) OR volume files
- Use for: app config, feature flags, connection strings (non-sensitive parts)

### Secret — sensitive data
- Base64 encoded (not encrypted!) — use external secrets operator for production
- **Production:** AWS Secrets Manager + External Secrets Operator — không lưu Secret thẳng trong Git
- Mount same as ConfigMap

### PersistentVolume (PV) & PVC
- **PV:** actual storage (AWS EBS, NFS, local disk)
- **PVC:** request storage — K8s binds PVC to matching PV
- **StorageClass:** dynamic provisioning (gp3, io1 on AWS)

---

## 5. Scaling

### Horizontal Pod Autoscaler (HPA)
- Scale số Pods dựa trên CPU/memory/custom metrics
- Config: `minReplicas: 2`, `maxReplicas: 10`, `targetCPUUtilizationPercentage: 70`
- Requires `metrics-server` installed
- Custom metrics: Kafka lag, queue depth via KEDA (Kubernetes Event-Driven Autoscaling)

### Vertical Pod Autoscaler (VPA)
- Điều chỉnh resource requests/limits automatically
- Không dùng cùng HPA trên cùng metric
- Good for: rightsizing over time

### Cluster Autoscaler
- Scale số Nodes khi Pods cannot schedule (Pending state)
- Cloud-specific: AWS Cluster Autoscaler, Karpenter (better, newer)

---

## 6. Health Checks (quan trọng!)

### Three Probe Types
- **livenessProbe:** nếu fail → K8s restart container. Use for: deadlock detection
- **readinessProbe:** nếu fail → remove from Service endpoints (traffic không vào). Use for: startup, overload
- **startupProbe:** cho apps khởi động chậm (Java JVM) — `failureThreshold * periodSeconds` = max startup time

### Configuration
- `httpGet: { path: /health/live, port: 8080 }` or `exec` or `tcpSocket`
- `initialDelaySeconds`, `periodSeconds`, `failureThreshold`
- For Java: `startupProbe` with `failureThreshold: 30, periodSeconds: 10` = 300s startup window

### Spring Boot Mapping
- `/actuator/health/liveness` → livenessProbe
- `/actuator/health/readiness` → readinessProbe

---

## 7. Deployment Strategies

### Rolling Update (default)
- Từng Pod được thay thế dần
- `maxSurge: 1` (tạo thêm 1 Pod mới), `maxUnavailable: 0` (không down any pods)
- Zero downtime, nhưng v1 và v2 chạy song song tạm thời

### Blue-Green
- 2 Deployments (blue=current, green=new), 1 Service
- Flip Service label selector khi green is ready → instant switch
- Instant rollback (flip back), nhưng tốn 2x resources

### Canary
- Route % traffic nhỏ sang version mới (5% → 25% → 100%)
- Using Ingress weighted routing hoặc Service Mesh (Istio VirtualService)
- Phát hiện issues sớm trước khi full rollout

---

## 8. Helm — Package Manager cho K8s

### Concepts
- **Chart:** package của K8s manifests + templates
- **Values:** tham số hóa config (`values.yaml`) — override per environment
- **Release:** một instance của chart được deploy
- **Repository:** nơi lưu charts (ArtifactHub, ECR, Harbor)

### Commands Reference
- `helm install my-api ./my-chart -f prod-values.yaml` — deploy
- `helm upgrade my-api ./my-chart -f prod-values.yaml` — update
- `helm rollback my-api 1` — rollback về revision 1
- `helm list` — xem releases
- `helm status my-api` — trạng thái release
- `helm diff upgrade` — preview changes (plugin)

---

## 9. RBAC & Security

### Role-Based Access Control
- **Role/ClusterRole:** defines permissions (verbs: get, list, create, delete)
- **RoleBinding/ClusterRoleBinding:** assigns Role to User/Group/ServiceAccount
- **ServiceAccount:** identity cho Pods — dùng để gọi K8s API hoặc AWS IAM via IRSA

### IRSA (IAM Roles for Service Accounts)
- Pods assume AWS IAM roles via service account annotations
- No credentials stored in Pods — federated identity
- Fine-grained per-service AWS permissions

### Pod Security
- **SecurityContext:** `runAsNonRoot: true`, `readOnlyRootFilesystem: true`, `capabilities: drop: ALL`
- **Pod Disruption Budget:** ensures minimum pods available during node drains

---

## 10. Debugging Commands Reference

- `kubectl get pods -n <ns> -o wide` — xem pods + node placement
- `kubectl describe pod <pod> -n <ns>` — events, lý do fail (resource, image pull, etc.)
- `kubectl logs <pod> -c <container> --tail=100` — xem logs
- `kubectl logs <pod> --previous` — logs from last (crashed) container instance
- `kubectl exec -it <pod> -- sh` — vào container debug
- `kubectl top pods -n <ns>` — resource usage
- `kubectl get events -n <ns> --sort-by=.lastTimestamp` — cluster events
- `kubectl port-forward svc/my-api 8080:8080` — test locally

---

## 11. Interview Topics

### Câu hỏi hay gặp
- "Deployment vs StatefulSet?" → Stateless (Deployment) vs stateful with stable identity (StatefulSet)
- "Liveness vs Readiness probe?" → Restart container vs remove from Service load balancer
- "Làm sao scale Kafka consumers trong K8s?" → HPA với custom lag metric (KEDA)
- "Làm sao xử lý secret securely?" → External Secrets Operator + AWS Secrets Manager
- "Rolling update fail giữa chừng?" → Tune maxSurge/maxUnavailable, dùng readiness probe đúng
- "Làm sao debug Pod CrashLoopBackOff?" → `kubectl logs --previous`, `kubectl describe`

### System Design Applications
- Microservices → mỗi service là 1 Deployment với HPA
- Kafka → StatefulSet, PVC cho storage
- Redis → Sentinel dùng StatefulSet hoặc Redis Operator
- Database → thường chạy ngoài K8s (managed service RDS/Cloud SQL)
- Ingress → AWS ALB Ingress Controller với SSL termination
- Config → ConfigMap + External Secrets Operator (không hardcode)
