
# Zero Trust POC Test Plan for F5 Tools
The architecture is segmented into management, external, and internal networks, and supports both production and staging Kubernetes clusters. This setup enables testing of Zero Trust principles such as least privilege, continuous verification, and microsegmentation.
## 🗺️ Lab Network Diagram

![Lab Network Diagram](lab_diagram.png)

## 🧰 Software List

### 🔹 F5 Specific Components

- **F5 Kubernetes BIG-IP Controller**  
  - **Container Image:** `f5networks/k8s-bigip-ctlr`  
  - **Purpose:** Integrates BIG-IP with Kubernetes to manage ingress traffic seamlessly while leveraging F5's advanced networking capabilities.

- **NGINX Ingress**  
  - **Purpose:** An alternative ingress controller for Kubernetes clusters, enabling flexible routing and load balancing for applications.

- **BIG-IP OVA / Virtual Editions**  
  - **Purpose:** A virtual machine image for deploying BIG-IP in virtualized or cloud-based environments to enable application traffic management, security, and load balancing.

- **Declarative Onboarding (DO)**  
  - **Purpose:** Simplifies BIG-IP onboarding processes with declarative JSON-based configuration files for setting up networks, users, and system services.

- **Application Services 3 (AS3)**  
  - **Purpose:** Automates application deployments using declarative configuration models, streamlining application services provisioning workflows.

### 🔸 Non-F5 Components

- **Ansible Execution Environment with CIS Integration**  
  - **Container Image:** `dialtone21/f5_cis_ee:1.0.0`  
  - **Purpose:** Provides a ready-to-use Ansible execution environment for managing tasks like configuration updates and automated workflows in environments using the F5 Container Ingress Services (CIS).

- **NGINX Hello Demo App**  
  - **Purpose:** A simple web application for demonstrating NGINX configuration and features, useful for testing and validating ingress controller setups.

- **OWASP Juice Shop**  
  - **Purpose:** A vulnerable web application specifically designed for security testing and showcasing Zero Trust architecture (e.g., AAA controls and secure access workflows).

- **Cert-Manager**  
  - **Purpose:** Automates the issuance and management of TLS certificates for Kubernetes clusters, ensuring secure communication pathways.

- **Syslog-ng (Balabit)**  
  - **Container Image:** `balabit/syslog-ng`  
  - **Purpose:** Centralized logging solution for collecting, filtering, and forwarding log messages from multiple sources. Useful for monitoring and debugging in complex environments.

---

## 🔐 Privileged User Access (PUA) Documentation

### Overview and Highlights
- **Privileged User Access Solution Overview**  
  Explains how F5’s solutions enable secure and streamlined privileged user access, reducing risk through granular identity-based controls over sensitive resources.

### Tutorials and Training
- **F5 CloudDocs - IAM Lab**  
  Hands-on lab experience to understand identity and access management principles, particularly for privileged user environments.

---

## 🌐 Required IP Subnets

| Subnet | Purpose |
|--------|---------|
| `10.1.1.0/24` | **Management (MGT)** – Access to BIG-IP interfaces or admin portals |
| `10.1.10.0/24` | **External (EXT)** – Public-facing traffic and app access |
| `10.1.20.0/24` | **Internal (INT)** – Internal services and app communication |
