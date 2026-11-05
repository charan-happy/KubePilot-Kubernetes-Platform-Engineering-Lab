# KubePilot-Kubernetes-Platform-Engineering-Lab

## 1. The project we're going to build

A self-service Kubernetes platform on AWS that lets application teams deploy services through GitOps, while a custom Go operator manages application configuration and lifecycle.AWS EKS

Go Operator

Terraform

Argo CD

DevSecOps

Observability



> Internal developer platform. A developer submits a custom resource describing an application. Your platform controller reconciles that desired state, while GitOps handles deployment and monitoring gives us visibility into the results.

This single project will demonstrate infrastructure engineering, Kubernetes internals, software development, delivery automation, security, and operational troubleshooting.


## 2. Architecture and How pieces connect

![1792805532101](image/README/1792805532101.png)

The main engineering workflow

1. You create an application definition in GitHub.
2. GitHub Actions tests the code, scans it, builds a container image, and publishes the image.
3. Argo CD detects the desired deployment configuration and synchronizes it with EKS.
4. Our Go operator watches a custom resource such as PlatformApp and reconciles the Kubernetes resources associated with it.
5. Kubernetes schedules the pods, manages service discovery, applies access controls, and scales workloads.
6. Prometheus and Grafana help us understand application health and troubleshoot failures.
7. You deliberately introduce a few failures and demonstrate recovery.


One important architectural decision: our Go operator and Argo CD will have distinct responsibilities. The operator will manage a small, clearly defined set of platform resources; Argo CD will manage the Git-declared installation of the platform and application. We won't have two controllers fighting over the same fields.


## 3. Mapping the project to Lilly's job description



Kubernetes operations

EKS, kubectl, Helm

Provision, deploy, upgrade considerations, networking, storage, scheduling and troubleshooting.

Core

Go controllers and operators

Kubebuilder/controller-runtime, CRDs

Build a PlatformApp API, reconcile resources, handle status and owner references; add tests.

Core

Infrastructure as code

Terraform, Kustomize, Helm

Reusable modules, remote state, environment configuration, validation and drift considerations.

Core

CI/CD and GitOps

GitHub Actions, Argo CD, ECR

Pull-request checks, build and publish, GitOps sync, rollout validation and rollback.

Core

Security and supply chain

Trivy, Gitleaks, IAM, IRSA

Image and dependency scans, secret detection, least privilege and workload identity.

Core

Scaling

HPA, Karpenter, KEDA, VPA

Implement HPA; explain metrics requirements and trade-offs. Add Karpenter or KEDA if time permits.

Core + stretch

Observability

Prometheus, Grafana, logs

Metrics, dashboards, alerts, troubleshooting and basic service-level indicators.

Core

Advanced Kubernetes APIs

Finalizers, webhooks, client-go

Implement a finalizer or admission validation; demonstrate deeper API patterns with tests and documentation.

Stretch

Enterprise integrations

Artifactory, Checkmarx, Splunk

Use ECR, open-source security scanners and a practical log-analysis alternative; document enterprise substitutions.

Equivalent



kubepilot-platform/
├── README.md
├── Makefile
├── docs/
│   ├── architecture.md
│   ├── decisions/
│   ├── runbooks/
│   └── interview-guide.md
├── infra/terraform/
│   ├── modules/{vpc,eks,iam}/
│   └── environments/dev/
├── platform/
│   ├── operator/{api,internal/controller,config}/
│   ├── charts/
│   └── gitops/
├── apps/demo-api/
├── deploy/{helm,kustomize}/
├── observability/{dashboards,alerts}/
├── policies/
└── .github/workflows/


## AWS Cost & Safety

The control plane, worker nodes, NAT gateways, load balancers, storage, and observability components can create charges.

Best practices 

- Use a dedicated development environment and the smallest sensible worker capacity.
- Avoid unnecessary NAT gateways, public load balancers and managed add-ons.
- Set AWS Budgets alerts before provisioning, while remembering that budget alerts are not hard spending caps.
- Avoid committing AWS credentials, kubeconfig files, Terraform state or secrets to GitHub.
- Add a teardown procedure and run terraform destroy when you're finished with the lab. Check for leftover resources and charges afterward.
- Treat production-grade private networking, multi-AZ availability and enterprise policy enforcement as explicit design decisions rather than claiming a small lab is production-ready.
  Karpenter and some additional AWS components may introduce extra permissions and configuration. We'll add them only after the core platform works.
