# Identity and Access Management (IAM)

IAM ensures the right individuals and systems have appropriate access to resources.  
Core concepts, common use cases, MFA options, and governance practices for secure, auditable access control.

---

## 1. Authentication and Authorization
- **Authentication**: Verifies identity (who you are) using credentials or factors.
- **Authorization**: Grants permissions (what you can do and where you can go) after successful authentication.
- Accounts have assigned privileges (roles/permissions) that determine access scope.

---

## 2. Identity Management Systems
- **What they store**: Users, groups, devices/systems, credentials, attributes, and privileges (roles/permissions).

### Examples
- **Centralized directories** (e.g., Active Directory via LDAP)  
  - Standardize user, group, and device management.  
  - Apps query AD/LDAP for authentication and group-based authorization.  

- **Local application databases** (e.g., a timesheet app with local users)  
  - User records and passwords stored in the app’s database.  
  - Pros: Simple setup.  
  - Cons: Password sprawl, siloed accounts, weak lifecycle controls.  

### Principles
- **Least privilege** and **role-based access control (RBAC)** reduce risk by limiting permissions to what’s necessary.

---

## 3. Single Sign-On (SSO)
- One login to access multiple apps via standards like **SAML** or **OpenID Connect**.

### Benefits
- Improves user experience with fewer authentications.
- Centralizes policy enforcement (MFA, session controls, password policy).

---

## 4. Federated Authentication
- Trust external identity providers (e.g., Google, Facebook, Azure AD) using **OAuth 2.0/OIDC**.
- Reduces credential storage and relies on secure token exchange.

---

## 5. Multi-Factor Authentication (MFA)
Adds an extra security layer beyond passwords.

### Types
- **SMS/Phone Call**: One-time codes via text or call; convenient but less resistant to SIM swap.  
- **Soft Authenticators**:  
  - Microsoft Authenticator: Push approvals, TOTP codes, number matching.  
  - Google Authenticator: TOTP codes; can be used offline.  

> Prefer app-based or **FIDO2/WebAuthn** where possible for stronger phishing resistance.

---

## 6. Auditing
- Centralize logging from IdMS, SSO, apps, and endpoints.

### Audit trail should capture:
- **What was changed**: objects, attributes, permissions.  
- **Who did it**: user/service identity, source IP/device.  
- **When it was done**: timestamp, session ID, correlation ID.  

---

## 7. Policy and Governance
- Enforce **joiner-mover-leaver** processes, access reviews, and segregation of duties.
- Automate provisioning/deprovisioning and require approvals for elevated access.

### Policy Types and Standards
- **Simple Policies**: Password strength (length, complexity, rotation, lockout).  
- **Industry Standards**:  
  - PCI-DSS: Cardholder data access controls and monitoring.  
  - HIPAA/ePHI: Safeguards for healthcare data (access, audit, integrity).  

---

## 8. AWS IAM
AWS IAM is Amazon's identity and access management service for securely controlling access to AWS resources.

### Core Components
- **Users**: Individual identities with permanent credentials (access keys, passwords).  
- **Groups**: Collections of users with shared permissions.  
- **Roles**: Temporary credentials assumed by users, applications, or services.  
- **Policies**: JSON documents defining permissions (Allow/Deny actions on resources).  

### Policy Types
- Identity-based policies: Attached to users, groups, or roles.  
- Resource-based policies: Attached directly to resources (S3 buckets, Lambda functions).  
- Service Control Policies (SCPs): Organization-level guardrails in AWS Organizations.  
- Permission boundaries: Maximum permissions an identity can have.  

### Best Practices
- Use roles instead of long-term access keys for applications and services.  
- Enable MFA for privileged users and root account.  
- Apply least privilege principle with granular policies.  
- Use IAM Access Analyzer to identify unintended resource access.  
- Rotate credentials regularly and audit with CloudTrail.  

### Key Features
- Temporary Security Credentials: Via AWS STS (Security Token Service).  
- Federation: Integrate with corporate directories (SAML, OIDC).  
- Cross-Account Access: Roles for secure resource sharing between AWS accounts.  
- Fine-Grained Permissions: Condition keys for context-aware access (IP, time, MFA status, tags).  

---

## 9. Azure Active Directory (Microsoft Entra ID)
Azure AD (now rebranded as Microsoft Entra ID) is Microsoft's cloud-based identity and access management service.

### Core Components
- **Users and Groups**: Cloud identities or synchronized from on-premises AD.  
- **Enterprise Applications**: Pre-integrated SaaS apps and custom applications.  
- **App Registrations**: OAuth 2.0/OIDC applications with service principals.  
- **Managed Identities**: System-assigned or user-assigned identities for Azure resources (no credential management).  

### Identity Types
- Cloud-only identities: Created and managed entirely in Azure AD.  
- Hybrid identities: Synchronized from on-premises Active Directory via Azure AD Connect.  
- Guest identities: B2B collaboration with external users.  

### Access Management
- Azure RBAC: Role-based access control for Azure resource management plane.  
- Azure AD roles: Administrative roles for managing the directory itself (Global Admin, User Admin, etc.).  
- Conditional Access: Risk-based and context-aware access policies (location, device compliance, sign-in risk).  
- Privileged Identity Management (PIM): Just-in-time privileged access with approval workflows.  

### Authentication Features
- Multi-Factor Authentication (MFA): SMS, phone call, Microsoft Authenticator, FIDO2 keys.  
- Passwordless Authentication: Windows Hello, FIDO2, Microsoft Authenticator.  
- Self-Service Password Reset (SSPR): Reduce helpdesk burden.  
- Identity Protection: ML-based risk detection for sign-ins and users.  

### Integration and Federation
- SAML, OAuth 2.0, OpenID Connect: Standards-based SSO.  
- Azure AD B2C: Customer identity and access management.  
- Azure AD B2B: Partner and guest collaboration.  
- AD FS Integration: Federation with on-premises Active Directory Federation Services.  

### Governance and Compliance
- Access Reviews: Periodic certification of user access rights.  
- Entitlement Management: Automated access request and approval workflows.  
- Audit Logs and Sign-in Logs: Comprehensive activity tracking.  
- Microsoft Graph API: Programmatic access to directory data.  

<p align="center"> <img src="images/0.png" alt="AWS Infrastructure" width="600"> </p>
