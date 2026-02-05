## 1. Azure Role-Based Access Control (RBAC)

Azure RBAC is an **authorization system** that controls **who can do what** on **which Azure resources**.

RBAC answers three questions:
- **Who** → User, Group, Service Principal, Managed Identity
- **What** → Role (set of permissions)
- **Where** → Scope (subscription, resource group, resource)

---

## 2. Core Components of Azure RBAC

### 2.1 Security Principal (Who)

A security principal is an identity that can be assigned a role:
- User
- Group
- Service Principal (application identity)
- Managed Identity (Azure resource identity)

---

### 2.2 Role Definition (What)

A role definition is a **collection of permissions**.

Permissions define:
- Allowed actions (read, write, delete)
- Denied actions (optional)

#### Built-in Roles (Common Examples)

| Role | Description |
|----|----|
| Owner | Full access including role assignment |
| Contributor | Full access except role assignment |
| Reader | View-only access |
| User Access Administrator | Manage access only |

Custom roles can also be created for fine-grained control.

---

### 2.3 Scope (Where)

Scope defines **where the role applies**.

Scopes (from broad to narrow):
- Management Group
- Subscription
- Resource Group
- Resource (VM, Storage Account, etc.)

> Permissions are inherited down the hierarchy.

---

## 3. Azure RBAC Flow

```mermaid
flowchart LR
    User[User / Group] --> RoleAssignment[Role Assignment]
    RoleAssignment --> RoleDef[Role Definition]
    RoleDef --> Scope[Scope]
    Scope --> AzureResource[Azure Resource]
