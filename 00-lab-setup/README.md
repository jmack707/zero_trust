
# Zero Trust POC Test Plan for F5 Tools
The architecture is segmented into management, external, and internal networks, and supports both production and staging Kubernetes clusters. This setup enables testing of Zero Trust principles such as least privilege, continuous verification, and micro-segmentation.
## 🗺️ Lab Network Diagram

![Lab Network Diagram](lab_diagram.png)

## 🧰 Software List

| Component | Type | Container Image | Purpose |
|-----------|------|-----------------|---------|
| GitHub Repository  | F5 | nginx-plus-ingress | Integrates BIG-IP with Kubernetes to manage ingress traffic |
| GitHub Repository | F5 | f5networks/k8s-bigip-ctlr | An alternative ingress controller for Kubernetes clusters, |
| BIG-IP Virtual Editions | F5 | BIGIP-17.5.0-0.0.15.ALL-vmware.ova | A virtual machine image for deploying BIG-IP in virtualized |
| GitHub Repository | F5 | f5-declarative-onboarding-1.46.0-7.noarch.rpm | Simpliﬁes BIG-IP onboarding processes with declarative |
| GitHub Repository | F5 | f5-appsvcs-3.54.0-7.noarch.rpm | Allows BIG-IP users to automate application deployments |
| Docker Hub | Non-F5 | dialtone21/f5_cis_ee:1.0.0 | Provides a ready-to-use Ansible execution environment for |
| Docker Hub | F5 | nginxdemos | A simple web application for demonstrating NGINX |
| Docker Hub | Non-F5 | bkimminich/juice-shop | A vulnerable web application specifically designed for |
| Docker Hub | Non-F5 | cert-manager | Automates the issuance and management of TLS |
| Docker Hub | Non-F5 | balabit/syslog-ng | Centralized logging solution for collecting, filtering, and |

---

## 🌐 Required IP Subnets

| Subnet | Purpose |
|--------|---------|
| `10.1.1.0/24` | **Management (MGT)** – Access to BIG-IP interfaces or admin portals |
| `10.1.10.0/24` | **External (EXT)** – Public-facing traffic and app access |
| `10.1.20.0/24` | **Internal (INT)** – Internal services and app communication |
| `10.1.30.0/30` | **MGT-BIG-IP P2P** - Point-2-Point link between MTG RTR and BIG-IP |
