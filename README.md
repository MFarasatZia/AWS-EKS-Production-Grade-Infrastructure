# 🧩 AWS EKS Production-Grade Infrastructure
🚀 A real-world Kubernetes infrastructure template showcasing best practices for deploying and managing production workloads on **Amazon EKS**.
Automates networking, security, autoscaling, secret management, and application delivery using **KEDA**, **Karpenter**, **IRSA**, **Helm**, and **SonarQube** — built entirely around AWS-native integrations.

---

## 📌 About This Project

This repository demonstrates a **fully production-grade Kubernetes architecture** that I personally designed and implemented for a real client environment (sanitized for portfolio use).
It reflects my expertise in **cloud-native DevOps automation**, covering:

✅ Secure AWS EKS setup (multi-env, private subnets, OIDC & IRSA)  
✅ Event-driven autoscaling using KEDA (SQS integration)  
✅ Dynamic node provisioning with Karpenter  
✅ AWS Secrets Manager integration for runtime secrets  
✅ Shared ALB Ingress with ACM TLS for multiple domains  
✅ Self-hosted SonarQube for static code analysis  
✅ Helm charts for reusable and environment-driven deployments  
✅ Postgres setup with PVC and gp3 storage  
✅ Zero hardcoded secrets — full IRSA-based access control  

---

## 🧠 Architecture Overview

AWS Cloud
│
├── EKS Cluster (IRSA Enabled)
│ ├── Namespaces: dev / mylera / tools
│ ├── Pods → IAM Roles via IRSA
│ ├── KEDA → Event-driven autoscaling from SQS
│ ├── Karpenter → Node autoscaling (on-demand/spot mix)
│ ├── Secrets Manager → Centralized secret management
│ ├── Helm → Declarative application deployments
│ └── ALB Ingress Controller → HTTPS + multi-domain routing
│
├── PostgreSQL (StatefulSet / RDS)
├── SonarQube (StatefulSet + PVC + gp3)
└── SQS Queue (message-driven scaling trigger)
---

## ✨ Features

- 🔐 **IRSA Integration:** Pods assume IAM roles using OIDC without static credentials  
- ⚙️ **KEDA Autoscaling:** Scales workloads automatically from SQS queue metrics  
- 🧠 **Karpenter:** Dynamically provisions EC2 nodes on demand  
- 🔑 **Secrets Manager:** All env vars and credentials fetched securely at runtime  
- 🌍 **Shared ALB Ingress:** Routes multiple apps (API, SonarQube) over HTTPS  
- 🧩 **Helm Templates:** Application deployments parameterized via `values.yaml`  
- 🧱 **SonarQube Integration:** Self-hosted quality gate inside EKS  
- 💾 **StorageClass (gp3):** Default encrypted persistent volumes  
- 🛡 **Namespace Isolation:** RBAC, policies, and resource quotas per environment  

---

## ⚙️ Tech Stack

| Category | Tool / Service |
|-----------|----------------|
| **Cloud Provider** | AWS |
| **Orchestration** | Amazon EKS (Kubernetes 1.29+) |
| **Autoscaling** | KEDA 3.x, Karpenter v1.12 |
| **Secrets Management** | AWS Secrets Manager |
| **Networking** | VPC, Subnets, NAT, Route53, ALB Ingress |
| **Storage** | EBS CSI (gp3), PVCs |
| **Database** | PostgreSQL (StatefulSet / RDS) |
| **Code Quality** | SonarQube (self-hosted) |
| **Packaging** | Helm 3.x |
| **Event Source** | AWS SQS |
| **Security** | IRSA, RBAC, TLS, OIDC |

---

## 🔄 Deployment Workflow

1️⃣ **Apply Base Cluster Resources**
```bash
kubectl apply -f manifests/

2️⃣ Deploy Monitoring Stack (SonarQube + Postgres)
kubectl apply -f sonarqube/

3️⃣ Enable Autoscaling

kubectl apply -f keda/
kubectl apply -f karpenter/

4️⃣ Deploy Application via Helm
helm install mylera helm-charts/node-mylera/ -n mylera

5️⃣ Apply Shared Ingress
kubectl apply -f ingress/

🧱 Repository Structure
.
├── manifests/        # Base cluster-level configs (namespaces, storage, secrets, postgres)
├── ingress/          # Shared ALB ingress configs (multi-domain setup)
├── keda/             # Event-driven autoscaling YAMLs
├── karpenter/        # Node provisioning and IAM roles
├── krr/              # Resource rightsizing reports
├── sonarqube/        # Self-hosted SonarQube deployment
├── helm-charts/      # Helm chart for Node.js API (IRSA-enabled)
└── README.md         # Documentation (this file)

🧩 Key Highlights
🔐 Security by Design

IAM → Pod mapping via IRSA
Secrets → Fetched from AWS Secrets Manager
TLS → Managed via ACM + ALB
EBS → Encrypted gp3 persistent volumes
Namespaces → Isolated and RBAC protected

⚡ Scalability

KEDA scales pods dynamically based on queue length
Karpenter spins up and down nodes automatically
Shared ALB reduces cost and centralizes ingress control

🧠 Maintainability

Helm values files for each environment (dev/staging/prod)
Modular YAML structure for easy customization
Stateless apps + stateful databases separated cleanly

📊 Observability

SonarQube dashboards for code quality
Health/liveness probes for every workload
Slack notifications (optional) for build and deploy events

📈 Outcomes

✅ Zero manual scaling — everything event-driven
✅ Zero hardcoded secrets — IRSA + Secrets Manager only
✅ 60–70% cost optimization via shared ALB + Karpenter
✅ Multi-environment ready — dev, stg, prod
✅ Complete isolation with RBAC and namespaces

🛠 Example Commands

Check pods by namespace:
kubectl get pods -n mylera

View Karpenter provisioned nodes:
kubectl get nodes -l karpenter.sh/provisioner-name=default

Monitor autoscaling decisions:
kubectl describe scaledobject -n dev

Validate ingress rules:
kubectl get ingress -A

🧠 Key Learnings

IRSA completely replaces static AWS credentials for pods
Shared ALB setup simplifies multi-domain TLS management
gp3 storage gives better performance and lower cost
KEDA + Karpenter combination provides 100% elastic scaling
Secrets Manager centralizes configuration safely for teams
