## Azure Policy Structure

### Every Azure Policy contains:
```json
{
  "properties": {
    "displayName": "",
    "policyType": "",
    "mode": "",
    "description": "",
    "metadata": {},
    "parameters": {},
    "policyRule": {
      "if": {},
      "then": {}
    }
  }
}
```

### Key Components Explained
1. displayName: Friendly name shown in Azure Portal.

2️. policyType: BuiltIn Custom (most common for enterprise rules)

3. mode:
   All → evaluates all resource types
   Indexed → evaluates only resource types with tags and location (recommended for most policies)

4. policyRule
     Contains:
       * if → condition
       * then → effect

# Azure Policy -- Deny VM Creation if Size is NOT D2

## Overview

This document describes how to create and assign an Azure Policy that:

-   Allows only VM sizes starting with `Standard_D2`
-   Denies all other VM sizes
-   Can be assigned at Subscription / Management Group / Resource Group
    level

------------------------------------------------------------------------

## Requirement

Allow only Virtual Machine sizes that start with:

Standard_D2

Examples allowed: - Standard_D2s_v3 - Standard_D2_v2 - Standard_D2as_v5

All other VM sizes must be denied.

------------------------------------------------------------------------

## Policy JSON

Save the following as `policy.json

```json
{
  "properties": {
    "displayName": "Allow Only D2 VM Sizes",
    "policyType": "Custom",
    "mode": "Indexed",
    "description": "This policy denies VM creation if the size is not Standard_D2.",
    "metadata": {
      "category": "Compute"
    },
    "parameters": {},
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "not": {
              "field": "Microsoft.Compute/virtualMachines/sku.name",
              "like": "Standard_D2*"
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```
------------------------------------------------------------------------

## Deployment Using Azure CLI

### Create Policy Definition

az policy definition create\
--name "allow-only-d2-vm-size"\
--display-name "Allow Only D2 VM Sizes"\
--description "Deny VM creation if size is not Standard_D2"\
--rules policy.json\
--mode Indexed

### Assign Policy to Subscription

az policy assignment create\
--name "enforce-d2-vm-size"\
--policy "allow-only-d2-vm-size"\
--scope /subscriptions/`<SUBSCRIPTION_ID>`{=html}

------------------------------------------------------------------------

## Compliance Monitoring

Azure Portal → Policy → Compliance

------------------------------------------------------------------------

## Summary

Azure Policy controls what can be deployed in Azure and enforces
governance standards.
