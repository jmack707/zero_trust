   📜 Apply application-specific configurations and enable advanced routing/security profiles using AS3.

4. **Test Zero Trust Use Cases:**  
   🔒 Verify the environment using Kubernetes workloads, simulated applications, and virtual network segmentation.

---

## 📦 Software Components

Below is an outline of the software used in this lab environment:

| Component | Type | Container/Image/Package | Purpose |
|-----------|------|-------------------------|---------|
| **BIG-IP VE** | F5 | `BIGIP-17.5.0-0.0.15.ALL-vmware.ova` | Deploy BIG-IP in a virtualized environment. |
| **Declarative Onboarding (DO)** | F5 | `f5-declarative-onboarding-1.46.0-7.noarch.rpm` | Simplifies BIG-IP onboarding processes with declarative API. |
| **Application Services 3 (AS3)** | F5 | `f5-appsvcs-3.54.0-7.noarch.rpm` | Enables automated application service deployments on BIG-IP. |
| **Kubernetes Ingress Controller** | F5 | `nginx-plus-ingress` | Integrates BIG-IP with Kubernetes for managing application traffic. |
| **BIG-IP Controller (CIS)** | F5 | `f5networks/k8s-bigip-ctlr` | Provides an alternative ingress controller to manage Kubernetes applications. |
| **Ansible Execution Environment** | Non-F5 | `dialtone21/f5_cis_ee:1.0.0` | Contains Ansible modules for interacting with BIG-IP. |
| **Web Applications** | Non-F5 | `nginxdemos`, `bkimminich/juice-shop` | Applications for traffic simulation and security testing. |
| **Cert-Manager** | Non-F5 | `cert-manager` | Manages the provisioning of TLS certificates within Kubernetes. |

---

## 🌐 Network Architecture

The lab environment includes several IP subnets to segregate management, external, and internal communications, as illustrated by the following network diagram:

### Network Subnets

| Subnet | Purpose |
|--------|---------|
| `10.1.1.0/24` | **Management (MGT)**: Provides access to BIG-IP admin interfaces and monitoring portals. |
| `10.1.10.0/24` | **External (EXT)**: Handles external-facing traffic and application access. |
| `10.1.20.0/24` | **Internal (INT)**: Manages internal application communication. |
| `10.1.30.0/30` | **Point-to-Point (MGT-BIG-IP P2P)**: Point-to-point connection between the management router and BIG-IP. |

### Network Topology

The following network diagram represents the environment's topology, highlighting Kubernetes clusters, BIG-IP devices, applications, and management tools:

![Network Architecture](lab_diagram.png)

---

## 🛠️ Setup Instructions

### 📥 Provision BIG-IP
1. Download the BIG-IP VE OVA file (`BIGIP-17.5.0-0.0.15.ALL-vmware.ova`) from the F5 website.
2. Deploy BIG-IP VE on your virtualization platform, e.g., VMware or VirtualBox.
3. Assign the management interface an IP address from the `10.1.1.0/24` network.

---

### 📦 Deploy Declarative Onboarding (DO) and Application Services 3 (AS3)
1. Use the following Ansible Playbook to upload DO and AS3 RPMs onto the BIG-IP:

```yaml
---
- name: Upload DO and AS3 RPMs to BIG-IP
  hosts: bigip
  tasks:
    - name: Push Declarative Onboarding RPM
      bigip_upload:
        src: "path/to/f5-declarative-onboarding-1.46.0-7.noarch.rpm"
        dest: "/var/config/rest/downloads/"
    - name: Push Application Services RPM
      bigip_upload:
        src: "path/to/f5-appsvcs-3.54.0-7.noarch.rpm"
        dest: "/var/config/rest/downloads/"
    - name: Install DO RPM
      bigip_command:
        commands:
          - "tmsh install /var/config/rest/downloads/f5-declarative-onboarding-1.46.0-7.noarch.rpm"
    - name: Install AS3 RPM
      bigip_command:
        commands:
          - "tmsh install /var/config/rest/downloads/f5-appsvcs-3.54.0-7.noarch.rpm"
```

---

### ⚙️ Update Device Configuration
- Use an Ansible Playbook to configure device attributes such as VLANs, Self-IPs, Route tables, and interface settings:

```yaml
---
- name: Configure BIG-IP device settings
  hosts: bigip
  tasks:
    - name: Configure VLANs
      bigip_vlan:
        name: "external"
        tagged_interfaces: ["1.1"]
        self_ip: "10.1.10.254"
        netmask: "255.255.255.0"

    - name: Configure Self-IPs
      bigip_self_ip:
        name: "external-self"
        vlan: "external"
        address: "10.1.10.254/24"
```

---

### 📜 Push Device Configuration
- Apply application-specific policies using Declarative Onboarding (DO) and AS3:

```yaml
---
- name: Deploy Applications via AS3
  hosts: bigip
  tasks:
    - name: Push AS3 Declaration
      bigip_json:
        content: "{{ lookup('file', 'application-declaration.json') }}"
```

---

## 🔒 Testing Zero Trust Use Cases

Once the BIG-IP, Kubernetes, and web applications are configured, you can proceed to test Zero Trust principles:

1. Simulate application traffic using the `nginxdemos` container; verify ingress behavior.
2. Deploy a vulnerable application (`bkimminich/juice-shop`) and inspect security configurations.
3. Use Kubernetes resources (`cert-manager`) to test automated certificate provisioning.
4. Verify segmentation between the `EXT` and `INT` subnets using network tools like `tcpdump`.

---

## 📖 References

1. [F5 Declarative Onboarding Documentation](https://clouddocs.f5.com/products/extensions/f5-declarative-onboarding/latest/)
2. [F5 Application Services 3 (AS3) Documentation](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/)
3. [Ansible Documentation for BIG-IP Modules](https://docs.ansible.com/ansible/latest/collections/f5networks/f5_modules/index.html)
4. [Docker Hub Container Images](https://hub.docker.com/)

---

Feel free to open issues or create pull requests if you have improvements or questions. 

---

Enjoy automated cloud networking and Zero Trust application testing with F5 BIG-IP! 🚀

--- 
