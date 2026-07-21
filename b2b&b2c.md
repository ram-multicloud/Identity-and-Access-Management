# Azure AD B2B (Business-to-Business)

**Purpose:** Let external partners, vendors, or contractors access your internal apps/resources using **their own existing identity** (their own company's Azure AD, Google account, etc.).

**Key idea:** The external user is invited as a **guest** into *your* tenant. They authenticate with their home organization, but authorization (what they can access) is governed by your tenant.

```mermaid
flowchart LR
    subgraph PartnerOrg["Partner Organization"]
        PU["External User\n(has own credentials)"]
        PIDP["Partner's Identity Provider\n(Azure AD / Google / etc.)"]
    end

    subgraph YourTenant["Your Azure AD Tenant"]
        Invite["Guest Invitation"]
        GuestObj["Guest User Object\n(userType: Guest)"]
        RBAC["Azure RBAC / App Permissions"]
        App["Your Internal App\n/ Resource"]
    end

    PU -- "1. Invited via email" --> Invite
    Invite -- "2. Creates guest object" --> GuestObj
    PU -- "3. Authenticates using\nhome org credentials" --> PIDP
    PIDP -- "4. Federated sign-in" --> GuestObj
    GuestObj -- "5. Assigned access" --> RBAC
    RBAC -- "6. Grants access to" --> App
```

**Characteristics:**
- One tenant inviting users who belong to *another* organization.
- Guest users authenticate at their **home IdP** — you never manage their password.
- Access is controlled via normal Azure RBAC / Conditional Access in your tenant.
- Typical use case: giving a contractor access to a SharePoint site, a partner engineer access to an internal DevOps project, etc.

---

# Azure AD B2C (Business-to-Consumer)

**Purpose:** Let **customers/consumers** (the public) sign up and sign in to a **customer-facing application** you build — using local accounts (email/password) or social identity providers (Google, Facebook, etc.).

**Key idea:** B2C is a **separate, dedicated tenant type** used purely as an identity platform for your app's end users. It is not about accessing your internal Azure resources.

```mermaid
flowchart LR
    subgraph Consumers["End Users / Customers"]
        C1["Customer A\n(email/password)"]
        C2["Customer B\n(Google account)"]
        C3["Customer C\n(Facebook account)"]
    end

    subgraph B2CTenant["Azure AD B2C Tenant"]
        Policy["Custom User Flow\n(sign-up / sign-in policy)"]
        Directory["B2C Consumer Directory"]
    end

    subgraph YourApp["Your Customer-Facing App"]
        Login["Login Page"]
        Token["Receives ID/Access Token"]
    end

    C1 -- "Local account signup" --> Policy
    C2 -- "Social login" --> Policy
    C3 -- "Social login" --> Policy
    Policy --> Directory
    Login -- "Redirects to" --> Policy
    Policy -- "Issues token" --> Token
```

**Characteristics:**
- Millions of consumer identities, not a handful of partner staff.
- Fully customizable sign-up/sign-in UI (branding, custom policies via Identity Experience Framework).
- Supports local accounts + social IdPs (Google, Facebook, Apple, etc.) + custom OIDC/SAML IdPs.
- Lives in its own dedicated B2C tenant, separate from your corporate Entra ID tenant.
- Typical use case: a retail app's customer login, a SaaS product's public sign-up flow.

# One-Line Summary

- **B2B** = "Let *another company's* employee into *my* tenant as a guest."
- **B2C** = "Let *the general public* sign up and log into *my app*."
