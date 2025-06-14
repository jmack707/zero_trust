# 🔐 MFA Lab Guide – Zero Trust POC

## 🎯 Objective
Demonstrate how F5 BIG-IP APM enforces Multi-Factor Authentication (MFA) to protect access to applications, in alignment with Zero Trust principles. In this lab, we are using **client certificate-based authentication** for MFA on the client side. On the server side, **SAML authentication** is used for App1 and **OIDC authentication** is used for App2. Note that other MFA methods like RSA tokens or YubiKey are also supported.

## 🧰 Tools & Components

| Component              | Purpose                                                    |
|------------------------|------------------------------------------------------------|
| F5 BIG-IP APM          | Acts as the access gateway enforcing MFA policies.         |
| Directory Service	     | Active Directory or LDAP                                   |
| Test Web Applications  | Protected apps (App1 and App2) behind BIG-IP to validate access control.  |

## 🧱 Lab Architecture
**User (Client Certificate) → BIG-IP APM → IdP → App1 (SAML) / App2 (OIDC)**

- BIG-IP enforces access policy with client certificate-based MFA challenge.
- App1 is protected using SAML authentication.
- App2 is protected using OIDC authentication.

## 🌐 IP Address Assignments

| Device     | Interface | IP Address     |
|------------|-----------|----------------|
| BIG-IP1    | MGMT      | 10.1.1.4       |
| BIG-IP1    | External  | 10.1.10.4      |
| BIG-IP1    | Internal  | 10.1.20.4      |
| BIG-IP2    | MGMT      | 10.1.1.5       |
| BIG-IP2    | External  | 10.1.10.5      |
| BIG-IP2    | Internal  | 10.1.20.5      |
| BIG-MGT    | P2p       | 10.1.30.2      |
| BIG-GMT    | Internal  | 10.1.20.100    |
| App1       | -         | 10.1.10.110    |
| App2       | -         | 10.1.10.120    |
| Webtop     | -         | 10.1.10.100    |

## 🧪 Lab Setup Instructions

**Configure Identity Provider (IdP)**
- Configure the BIG-IP IDP
- Register BIG-IP as a SAML client for App1.
- Register BIG-IP as an OIDC client for App2.
- Enable MFA (e.g., Microsoft Authenticator, Okta Verify).

**Deploy BIG-IP APM**
- Import SSL certs and configure virtual server.
- Configure client certificate authentication in SSL profile.
- Create Access Policy:  
  Client Cert Auth → SAML Auth (App1) / OIDC Auth (App2) → MFA Enforcement → Resource Assignment

**Protect Web Applications**
- Deploy two simple apps (e.g., NGINX Hello App for App1 and App2).
- Route traffic through BIG-IP with appropriate access policies.

## ✅ Test Scenarios

| Scenario         | Description                        | Expected Result                     |
|------------------|------------------------------------|-------------------------------------|
| Standard Login   | User logs in with valid client cert | Prompted for MFA                    |
| Untrusted Device | Login from device without cert     | Access denied or challenged         |
| App1 Access      | Access App1 via SAML               | SAML auth + MFA enforced            |
| App2 Access      | Access App2 via OIDC               | OIDC auth + MFA enforced            |

## 📊 Success Criteria
- 100% of login attempts require MFA.
- Unauthorized or risky access attempts are blocked or challenged.
- Logs show successful and failed MFA attempts.
- App1 and App2 are accessible only after successful MFA.