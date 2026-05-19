# k8s-devsecops-project

Multi-node Kubernetes infrastructure with a fully automated DevSecOps CI/CD pipeline - IaC provisioning via Ansible, zero-CVE Trivy scanning, HashiCorp Vault dynamic secret injection, and Kubernetes self-healing.

---

## What this project does

- **Infrastructure as Code** - entire cluster provisioned with Ansible (idempotent, reproducible)
- **CI/CD pipeline** - GitHub Actions builds, scans, and deploys automatically on every push
- **Zero-Trust security** - no secrets stored in plaintext, dynamic injection via Vault at runtime
- **Trivy scanning** - image scanned before every build, 0 CVE enforced as a pipeline gate
- **Self-healing** - Kubernetes Liveness Probes auto-restart failed containers, cluster stays stable

---

## Stack

| Layer | Technology |
|---|---|
| Provisioning | Ansible (IaC, idempotent playbooks) |
| Container runtime | Docker |
| Orchestration | Kubernetes v1.28 |
| CI/CD | GitHub Actions |
| Security scanning | Trivy |
| Secret management | HashiCorp Vault |
| OS | Ubuntu 24.04 LTS |

---

## I. Infrastructure Provisioning - Ansible

The entire cluster configuration (Master + 2 Workers) is managed by Ansible playbooks. No manual steps - infrastructure state is declared, not clicked.

The PLAY RECAP below confirms a clean deployment with perfect state synchronization across all three nodes.

![Ansible Playbook Success](https://github.com/user-attachments/assets/85ad43eb-71dd-44fb-8204-b3ad3249fa7d)

All nodes in Ready state confirmed via kubectl:

![Kubernetes Nodes Status](https://github.com/user-attachments/assets/6198c244-3cee-4f5d-9a5d-9a15733f0da4)

---

## II. CI/CD Pipeline - GitHub Actions

The pipeline enforces a strict promotion model - each stage must pass before the next one starts. A vulnerability in the image blocks the entire pipeline before anything reaches production.

![GitHub Actions Dashboard](https://github.com/user-attachments/assets/22277c23-64b2-4ac6-94bc-30774951ee3d)

Before every build, the base image is scanned by Trivy. The pipeline only continues if the result is 0 CVE. Any vulnerability blocks deployment automatically.

![Trivy Scan Report](https://github.com/user-attachments/assets/2ef76fe4-53da-412a-9ffc-02f9b51b260c)

The application is deployed via YAML manifests - desired state declared, Kubernetes enforces it:

![Deployment Logs](https://github.com/user-attachments/assets/d4da2fa8-2345-4bcb-955c-70717b434c0f)

---

## III. Observability and Self-Healing

During monitoring, CrashLoopBackOff events were detected - caused by VXLAN encapsulation overhead from the Flannel CNI plugin under load.

Rather than a failure, this validated the self-healing mechanism: Kubernetes Liveness Probes detected the unhealthy containers and restarted them automatically, keeping the service available throughout.

![Self-Healing Analysis](https://github.com/user-attachments/assets/90e04fa7-c3fa-4597-a6fe-d595d3c223f0)

---

## IV. Secret Management - HashiCorp Vault

No credentials are stored in plaintext - not in the repo, not in environment variables, not in Kubernetes Secrets as base64.

The application authenticates to Vault using its Kubernetes ServiceAccount. Vault returns the secret at runtime only to authorized pods. The screenshot below was taken directly from inside the application container - confirming live retrieval of api_key and db_password:

![Vault Secret Extraction](https://github.com/user-attachments/assets/24ca929d-c5c5-4d9f-a35b-63299c45e40c)

---

## Key results

- 0 CVE on every build - enforced by Trivy as a hard pipeline gate
- Full cluster provisioned from zero with a single Ansible command
- No plaintext secrets anywhere in the codebase or cluster
- Self-healing validated under real failure conditions (CrashLoopBackOff recovery)
- End-to-end pipeline: push to deploy in a fully automated, auditable chain

---

## Repository structure
k8s-devsecops-project/
├── .github/workflows/ # GitHub Actions CI/CD pipeline
├── Dockerfile # Application container definition
├── deployment.yaml # Kubernetes Deployment + Service manifests
└── index.html # Application frontend

---
*Author: Sidali - Cloud Infrastructure & DevSecOps - 2026*
