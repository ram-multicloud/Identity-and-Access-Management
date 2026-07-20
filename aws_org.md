## IAM Identity Center (formerly AWS SSO)

For organizations managing many human users across multiple AWS accounts, **IAM Identity Center** is the recommended entry point:

- Centralized sign-in (can federate with Okta, Azure AD, Google Workspace, etc.)
- Users are assigned **Permission Sets** (which are essentially IAM roles) mapped to specific AWS accounts
- Avoids creating individual IAM Users in every account


## Governance & Guardrails

### Governance Is a Separate Layer

IAM policies and roles control what **one identity** can do. But in a real organization with dozens or hundreds of AWS accounts, you need controls that apply **across every account, every identity, all the time** — regardless of how an individual account is configured. That's what governance and guardrails provide.

Think of it as two layers:

- **IAM policies** = what a *specific identity* is allowed to do
- **Guardrails** = the *outer boundary* that no identity, not even an account's own administrators, can exceed

### AWS Organizations
**AWS Organizations** lets you centrally manage multiple AWS accounts as a single structure:

```
Root
 ├── OU: Production
 │     ├── Account: prod-app-1
 │     └── Account: prod-app-2
 ├── OU: Development
 │     ├── Account: dev-app-1
 │     └── Account: dev-app-2
 └── OU: Sandbox
       └── Account: sandbox-1
```

- **OU (Organizational Unit)** — a folder used to group accounts that should share the same policies (e.g., all Production accounts)
- Policies attached at a higher level (Root or an OU) automatically apply to every account beneath it

### Service Control Policies (SCPs)

An SCP is a guardrail that sets the **maximum permissions** available in an account. It does not grant any permissions by itself — it only restricts.

**Key mental model:** Think of SCPs as a filter sitting between every identity in the account and AWS itself.

```
Request → Does an IAM policy allow this action?  → Does the SCP also allow it? → Action executes
                     |                                        |
                    No → Denied                              No → Denied (even if IAM policy said yes)
```

**Important facts about SCPs:**
- They apply to **every identity in the account**, including the account's own root user and admins
- They **never grant** permissions — a user still needs an IAM policy granting the action; the SCP just cannot be more permissive than what it allows
- Attached at the Organization Root, an OU, or an individual account
- Written in the same JSON policy language as IAM policies, but using `Deny` or explicit `Allow` lists

**Example — deny use of any region except two approved ones:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "NotAction": [
        "iam:*",
        "organizations:*",
        "support:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": ["us-east-1", "ap-south-1"]
        }
      }
    }
  ]
}
```

**Example — prevent anyone from disabling CloudTrail:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "cloudtrail:StopLogging",
        "cloudtrail:DeleteTrail"
      ],
      "Resource": "*"
    }
  ]
}
```

### Other Guardrail-Related Tools

| Tool | Purpose | Explained Simply |
|---|---|---|
| **Permission Boundaries** | Sets the max permissions for a *single* IAM User or Role (not account-wide like an SCP) | A manager delegating "you can create IAM users, but never with more access than X" |
| **AWS Config + Conformance Packs** | Continuously checks resource configurations against rules | "Alert or flag me if any S3 bucket becomes public" |
| **IAM Access Analyzer** | Scans for resources shared outside the account/org | "Tell me if anything is accidentally exposed to the internet or another account" |
| **AWS Control Tower** | Automates setting up a well-governed multi-account environment | Pre-packages SCP guardrails, account baselines, and logging when creating new accounts |
