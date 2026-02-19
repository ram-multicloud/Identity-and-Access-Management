#ARM

# Azure Resource Manager (ARM) Templates

## Overview
Azure Resource Manager (ARM) templates are JSON files that declare the desired state of Azure infrastructure. Developers define resources and configurations, submit the template to ARM, and Azure provisions and configures everything automatically. This is declarative Infrastructure as Code.

---

## Introduction to ARM Templates
- Describe **what to deploy**, not **how to deploy**.
- Enable repeatable, idempotent deployments across environments.
- Support dependencies, parameterization, and outputs for modular, reusable infrastructure.


---


## How to Write ARM Templates
- Define **parameters** for environment-specific values.
- Add **variables** for computed names/strings.
- List **resources** with types, API versions, properties, and dependencies.
- Include **outputs** to return IDs, connection strings, etc.
- Validate and deploy via **Azure CLI / PowerShell / Portal**.

---


## Template Structure and Anatomy
- **$schema**: Template schema URL.
- **contentVersion**: Your version tag (e.g., `1.0.0.0`).
- **parameters**: Inputs (names, types, defaults).
- **variables**: Derived values.
- **functions**: Built-in and user-defined template functions.
- **resources**: Array of resource declarations.
- **outputs**: Values returned after deployment.

---

## Core Properties and Uses
- **$schema**: Enables tooling validation.
- **contentVersion**: Controls versioning.
- **parameters**: e.g., location, names, sizes.
- **variables**: String concat, resource names.
- **resources**: type, apiVersion, name, location, properties, dependsOn.
- **outputs**: Expose resource data (IDs, endpoints).
- **functions**: `concat`, `format`, `reference`, `resourceId`, `listKeys`, `if`, `copy` loops.

---


```json
{
  "$schema": "",
  "contentVersion": "",
  "parameters": {},
  "variables": {},
  "functions": [],
  "resources": [],
  "outputs": {}
}

```

---

---

## Practical Examples


### Storage Account


```json
{
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2023-01-01",
  "name": "[parameters('storageName')]",
  "location": "[parameters('location')]",
  "sku": { "name": "Standard_LRS" },
  "kind": "StorageV2"
}


```

---
