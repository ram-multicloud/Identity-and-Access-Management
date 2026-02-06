# AWS IAM Role with SSM Access for EC2

## Overview
This document explains how to configure an **IAM Role for EC2** that enables access to **AWS Systems Manager (SSM)** without using SSH keys or opening inbound ports.

With this setup, you can:
- Use **Session Manager** for secure shell access
- Perform **patching**, **inventory**, and **automation**
- Eliminate bastion hosts and inbound security group rules

---

## Architecture (High Level)
```
User / Admin
     |
     v
AWS Systems Manager
     |
     v
SSM Agent on EC2 Instance
     |
     v
IAM Role (EC2SSMRole)
```

---

## Prerequisites
- EC2 instance running Amazon Linux 2, Amazon Linux 2023, Ubuntu, or RHEL
- SSM Agent installed (installed by default on most AMIs)
- Instance has outbound internet access **or** VPC endpoints for SSM

---

## Step-by-Step Implementation

### 1. Create IAM Role

**Role Name:** `EC2SSMRole`

**Trusted Entity:** EC2 service

#### Trust Policy (Required)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

---

### 2. Attach SSM Permissions

#### Option A: Recommended (Managed Policy)
Attach AWS-managed policy:

```
AmazonSSMManagedInstanceCore
```

This policy includes all required permissions for:
- Session Manager
- Patch Manager
- Inventory
- Automation

---

#### Option B: Minimal Custom Policy (Corrected)

Use this **least-privilege** policy if you want tighter control:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:UpdateInstanceInformation",
        "ssm:ListInstanceAssociations",
        "ssm:DescribeInstanceProperties",
        "ec2messages:AcknowledgeMessage",
        "ec2messages:DeleteMessage",
        "ec2messages:FailMessage",
        "ec2messages:GetEndpoint",
        "ec2messages:GetMessages",
        "ec2messages:SendReply",
        "ssmmessages:CreateControlChannel",
        "ssmmessages:CreateDataChannel",
        "ssmmessages:OpenControlChannel",
        "ssmmessages:OpenDataChannel"
      ],
      "Resource": "*"
    }
  ]
}
```

---

### 3. Create Instance Profile

IAM roles must be attached to EC2 via an **instance profile**.

```bash
aws iam create-instance-profile \
  --instance-profile-name EC2SSMInstanceProfile

aws iam add-role-to-instance-profile \
  --instance-profile-name EC2SSMInstanceProfile \
  --role-name EC2SSMRole
```

---

### 4. Attach Role to EC2 Instance

#### New EC2 Instance
- Launch EC2
- **Advanced details → IAM instance profile**
- Select `EC2SSMInstanceProfile`

#### Existing EC2 Instance (No Reboot Required)
```bash
aws ec2 associate-iam-instance-profile \
  --instance-id i-xxxxxxxxxxxx \
  --iam-instance-profile Name=EC2SSMInstanceProfile
```

---

## Verify SSM Access

### Console Verification
1. Go to **AWS Systems Manager**
2. Fleet Manager → Managed instances
3. Instance status should be **Online**

---

### CLI Verification

```bash
aws ssm describe-instance-information \
  --query 'InstanceInformationList[].InstanceId'
```

Expected output: instance ID listed

---

### Start Session (CLI)

```bash
aws ssm start-session --target i-xxxxxxxxxxxx
```

If successful, a shell opens without SSH.

---

## Common Troubleshooting

| Issue | Check |
|-----|------|
| Instance not showing | IAM role attached, SSM agent running |
| Session fails | Outbound access or VPC endpoints |
| Permission error | Correct policy attached |

---

## Best Practices
- Prefer **AmazonSSMManagedInstanceCore** unless compliance requires custom policy
- Use **VPC Endpoints** for private subnets
- Enable **CloudTrail** for SSM auditing
- Combine with **Session Manager logging** (S3 / CloudWatch)

---

## Summary
This IAM role setup enables secure, keyless EC2 access using AWS Systems Manager. It improves security posture, simplifies access management, and aligns with AWS best practices.

---

**Document Type:** Single-page Markdown
**Use Case:** Production-ready EC2 SSM access

