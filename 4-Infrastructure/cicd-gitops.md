# CI/CD & GitOps for Backend Engineers

> Delivery pipeline automation và GitOps practices cho production systems

---

## 1. CI/CD Fundamentals

### CI (Continuous Integration)
- **Goal:** detect integration issues sớm — mỗi commit trigger automated build + test
- **Pipeline stages:** Code → Build → Test (unit + integration) → Static Analysis → Package
- **Key metrics:** build time, test pass rate, flaky test rate

### CD (Continuous Delivery vs Deployment)
- **Delivery:** artifact sẵn sàng deploy bất kỳ lúc nào (human approval step for production)
- **Deployment:** auto-deploy tới production sau mỗi commit pass CI
- Phần lớn production systems dùng **Delivery** cho production, **Deployment** cho staging

### DevOps vs GitOps
- **DevOps:** culture + practices cho collaboration Dev + Ops
- **GitOps:** Git = single source of truth cho infrastructure state; changes via PR, controller reconcile

---

## 2. GitHub Actions

### Core Concepts
- **Workflow:** `.github/workflows/*.yml` — triggered by events (push, PR, schedule)
- **Job:** tập hợp steps, chạy trên runner (ubuntu-latest, windows, mac)
- **Step:** command hoặc action
- **Action:** reusable unit (GitHub Marketplace hoặc custom)
- **Secrets:** `${{ secrets.AWS_SECRET_KEY }}` — không expose trong logs

### Typical Java/Spring Boot Pipeline Stages
1. **Checkout** — `actions/checkout@v4`
2. **Setup Java** — `actions/setup-java@v4` with Java 21, Temurin, Maven cache
3. **Build & Test** — `mvn verify` (compile + unit + integration tests)
4. **Code Coverage** — JaCoCo report
5. **Static Analysis** — SonarQube: `mvn sonar:sonar`
6. **Build Docker Image** — `docker build -t <registry>/my-api:<commit-sha>`
7. **Push to ECR** — authenticate then push
8. **Update GitOps Repo** — commit new image tag → ArgoCD picks up

### Pipeline Best Practices
- **Cache dependencies:** Maven/Gradle cache → 5x faster builds
- **Fail fast:** lint/compile trước, expensive tests sau
- **Matrix builds:** test trên nhiều Java versions song song
- **Concurrency groups:** cancel in-progress runs khi có commit mới to same branch
- **Secrets management:** GitHub Secrets hoặc OIDC (không hardcode AWS keys)
- **OIDC với AWS:** GitHub Actions assume AWS IAM role — no long-lived credentials needed

---

## 3. GitOps

### GitOps Principles
1. **Declarative:** mô tả desired state (K8s manifests, Helm values)
2. **Versioned:** tất cả state trong Git — full audit trail, easy rollback
3. **Automatically applied:** controller liên tục reconcile Git state → cluster state
4. **Continuously reconciled:** drift detection → auto-correct

### Repository Structure
Typical gitops repo layout:

apps/
  my-api/
    base/           ← K8s manifests cơ bản (deployment, service)
    overlays/
      staging/      ← Kustomize patches cho staging
      production/   ← Kustomize patches cho production
  redis/
infrastructure/
  aws/              ← Terraform modules

### Repo Strategy
- **Monorepo:** app code + K8s manifests cùng repo → đơn giản cho small teams
- **Separate repos:** gitops repo riêng → better separation of concerns, cleaner history
- **Thực tế:** separate repos phổ biến hơn ở enterprise

---

## 4. ArgoCD (GitOps Controller)

### Concepts
- Syncs Git state → K8s cluster continuously
- **Application:** represents one app to sync (source repo + destination cluster/namespace)
- **Sync status:** Synced / OutOfSync / Unknown
- **Health status:** Healthy / Degraded / Progressing

### Application Config
Key fields: `repoURL`, `targetRevision: main`, `path: apps/my-api/overlays/production`, `namespace: production`

**syncPolicy.automated:**
- `prune: true` — delete resources no longer in Git
- `selfHeal: true` — auto-fix drift (someone kubectl-applies manually)

### Image Update Automation
- **ArgoCD Image Updater:** watch container registry, auto-update image tag in Git repo
- Flow: build image (CI) → push ECR → Image Updater detect → commit to gitops repo → ArgoCD sync

### FluxCD (alternative)
- Similar GitOps controller, more GitOps-native
- `Kustomization`, `HelmRelease` CRDs
- Preferred by some teams for its GitOps-first design

---

## 5. Deployment Strategies

### Rolling Update (K8s default)
- Pods được thay dần dần — zero downtime
- Cần backward-compatible API changes (v1 và v2 chạy song song tạm thời)

### Blue-Green
- Two Deployments (blue=current, green=new), one Service
- Test green → flip Service selector to green → instant traffic switch
- Instant rollback (flip back), 2x resources during deploy

### Canary
- Route small % traffic to v2 (e.g., 10%)
- Using Ingress weighted routing (nginx-ingress annotations) or Istio VirtualService
- Monitor error rate + latency → tăng dần lên 100%

### Feature Flags (decouple deploy from release)
- Deploy code với flag OFF → enable dần cho % users
- Libs: LaunchDarkly, Unleash (self-hosted), Split

---

## 6. Version Control Practices

### Conventional Commits
Format: `type(scope): description`

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`, `ci`

Examples:
- `feat(auth): add JWT refresh token endpoint`
- `fix(payment): handle timeout when gateway unreachable`
- `BREAKING CHANGE: user API response format changed`

**Tools:** commitlint + husky → enforce format at commit time
**Benefits:** auto-generate CHANGELOG, semantic versioning, readable history

### Branching Strategies

#### Trunk-Based Development (recommended)
- Mọi developer commit vào `main` / `trunk` ít nhất 1 lần/ngày
- Feature flags ẩn incomplete features
- Short-lived branches (< 1–2 ngày)
- **Pros:** ít merge conflicts, CI/CD hiệu quả, true CI

#### GitFlow
- `main` + `develop` + `feature/` + `release/` + `hotfix/`
- Phổ biến hơn với scheduled releases
- **Cons:** long-lived branches → merge conflicts, slower delivery

### Git Quick Reference (commands)
- `git clone <url>` — clone repository
- `git status` / `git add -A` / `git commit -m "msg"` — workflow chuẩn
- `git push origin <branch>` / `git pull --rebase` — sync với remote
- `git log --oneline` / `git diff` — inspect history & changes
- `git stash` / `git stash pop` — save/restore uncommitted changes
- `git rebase <base>` — rebase branch lên base mới
- `git cherry-pick <sha>` — apply commit cụ thể
- `git tag -a vX.Y.Z -m "release"` — tạo release tag

---

## 7. Quality Gates

### Code Quality Tools
- **SonarQube/SonarCloud:** static analysis, code smells, security hotspots, coverage
- **Checkstyle:** Java code style enforcement
- **SpotBugs:** find common bugs at compile time
- **OWASP Dependency Check:** detect vulnerable dependencies

### Branch Protection Rules
- Require PR reviews (minimum 1 approver)
- Require CI pass trước khi merge
- Require up-to-date với base branch
- Block force push to main

---

## 8. Interview Topics

### Câu hỏi hay gặp
- "Giải thích CI/CD pipeline của bạn?" → Trigger → build → test → image → deploy
- "GitOps vs DevOps?" → GitOps là subset, Git = source of truth, declarative, controller reconciles
- "Làm sao rollback nhanh?" → Blue-Green (flip selector), ArgoCD rollback to previous revision
- "Canary vs Blue-Green?" → Canary = gradual %, Blue-Green = binary switch
- "Làm sao test integration với external services trong CI?" → WireMock stubs, Testcontainers
- "Conventional commits lợi ích gì?" → Auto changelog, semantic versioning, readable history
