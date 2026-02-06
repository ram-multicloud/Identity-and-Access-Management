### Tools
- Azure CLI ≥ 2.58
- Az PowerShell ≥ 11
- Azure Portal access

### Required Permissions
- Entra ID **User Administrator** or **Global Administrator** (for users & groups)
- Azure **Owner** or **User Access Administrator** (for RBAC assignments)
---

## Sign In and Set Context

### Azure CLI
```bash
az login
az account set --subscription <subscription-id>
```

### PowerShell
```powershell
Connect-AzAccount -Subscription <subscription-id>
```

---

## 1. IAM & RBAC Fundamentals

### Identities (Entra ID)
- **Users** – human identities
- **Groups** – collections of users
- **Service Principals** – app identities
- **Managed Identities** – Azure-managed service identities

### Roles (RBAC)
- **Built-in roles** (Owner, Contributor, Reader, etc.)
- **Custom roles** (JSON-defined permissions)

### Scope Hierarchy
```
Management Group
   └── Subscription
        └── Resource Group
             └── Resource
```

> Permissions inherit **downward** from parent scopes.

---

## 2. Create and Manage Users (Entra ID)

### Portal
1. Entra ID → Users → **New user**
2. Set username, display name, groups
3. Save and share temporary password

Verify: user appears and can sign in (if allowed)

---

### Azure CLI
```bash
az ad user create \
  --display-name "Alex Meyer" \
  --user-principal-name alex@contoso.com \
  --password "<TempPass#123>" \
  --force-change-password-next-sign-in true
```

---

### PowerShell
```powershell
$pwd = ConvertTo-SecureString "<TempPass#123>" -AsPlainText -Force
New-AzADUser \
  -DisplayName "Alex Meyer" \
  -UserPrincipalName "alex@contoso.com" \
  -Password $pwd
```

---

## 3. Create and Manage Groups

### Portal
Entra ID → Groups → **New group** (Security) → Add members

---

### Azure CLI
```bash
az ad group create \
  --display-name "RG-Admins" \
  --mail-nickname "rgadmins"

az ad group member add \
  --group "RG-Admins" \
  --member-id <user-object-id>
```

---

### PowerShell
```powershell
$g = New-AzADGroup -DisplayName "RG-Admins" -MailNickname "rgadmins"
Add-AzADGroupMember -TargetGroupObjectId $g.Id -MemberObjectId <user-object-id>
```

---

## 4. Azure RBAC Built-in Roles (Examples)

- **Owner** – full access + role assignments
- **Contributor** – manage resources (no RBAC)
- **Reader** – read-only
- **Virtual Machine Contributor** – manage VMs
- **Storage Blob Data Reader** – read blob data

---

## 5. Create Custom Role Definitions

### Example A: Storage Read-Only Role
```json
{
  "Name": "Storage Read Only",
  "IsCustom": true,
  "Description": "Read storage accounts and blob data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/read"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/<subscription-id>"
  ]
}
```

---

### Example B: VM Operator (Start/Stop)
```json
{
  "Name": "VM Operator",
  "IsCustom": true,
  "Description": "Start and deallocate virtual machines",
  "Actions": [
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Resources/subscriptions/resourceGroups/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<subscription-id>"
  ]
}
```

---

### Example C: Reader Without Delete
```json
{
  "Name": "RG Reader No Delete",
  "IsCustom": true,
  "Description": "Read access but deny delete operations",
  "Actions": [
    "*/read"
  ],
  "NotActions": [
    "*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription-id>"
  ]
}
```

---

### Create Custom Role

#### Azure CLI
```bash
az role definition create --role-definition ./vm-operator.json
```

#### PowerShell
```powershell
New-AzRoleDefinition -InputFile .\vm-operator.json
```

---

## 6. Assign Roles (RBAC)

### Azure CLI Examples

#### Management Group
```bash
az role assignment create \
  --assignee <object-id> \
  --role Reader \
  --scope /providers/Microsoft.Management/managementGroups/<mg-id>
```

#### Subscription
```bash
az role assignment create \
  --assignee <user-object-id> \
  --role Contributor \
  --scope /subscriptions/<subscription-id>
```

#### Resource Group
```bash
az role assignment create \
  --assignee-object-id <group-object-id> \
  --assignee-principal-type Group \
  --role "VM Operator" \
  --scope /subscriptions/<subscription-id>/resourceGroups/<rg-name>
```

#### Resource
```bash
az role assignment create \
  --assignee <sp-object-id> \
  --role "Storage Blob Data Reader" \
  --scope /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<account>
```

---

### PowerShell Examples
```powershell
New-AzRoleAssignment -ObjectId <objId> -RoleDefinitionName Reader \
  -Scope "/providers/Microsoft.Management/managementGroups/<mg-id>"

New-AzRoleAssignment -ObjectId <userObjId> -RoleDefinitionName Contributor \
  -Scope "/subscriptions/<subId>"

New-AzRoleAssignment -ObjectId <groupObjId> -RoleDefinitionName "VM Operator" \
  -Scope "/subscriptions/<subId>/resourceGroups/<rg>"

New-AzRoleAssignment -ObjectId <spObjId> -RoleDefinitionName "Storage Blob Data Reader" \
  -Scope "/subscriptions/<subId>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<acct>"
```

---

### Verify Role Assignments
```bash
az role assignment list --assignee <object-id> --all
```

---

## 7. Azure Policy (Custom Policy Example)

### Policy Structure
- `displayName`
- `policyRule` (if / then)
- `parameters` (optional)

---

### Example: Require Tag `environment`
```json
{
  "properties": {
    "displayName": "Require tag: environment",
    "policyRule": {
      "if": {
        "field": "tags['environment']",
        "equals": null
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

---

### Create and Assign Policy
```bash
az policy definition create \
  --name require-env \
  --rules ./require-env.json \
  --mode All

az policy assignment create \
  --name require-env \
  --policy require-env \
  --scope /subscriptions/<subscription-id>
```
---

