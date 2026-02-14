# AWS IAM Policy Conditions – Complete Guide

## Table of Contents

1. Introduction  
2. IAM Policy Structure  
3. Condition Block Structure  
4. Condition Operator Types  
5. Global Condition Keys  
6. Service-Specific Condition Keys  
7. Multi-Value Operators  
8. Policy Evaluation Logic  
9. Real-World Examples  
10. ABAC (Tag-Based Access Control)  
---

# 1. Introduction

AWS IAM Policy Conditions allow you to control **when** a policy statement applies.

They help enforce:
- IP restrictions
- MFA requirements
- Time-based access
- Region restrictions
- Tag-based access control (ABAC)
- Encryption enforcement
- HTTPS enforcement

---

# 2. IAM Policy Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ExampleStatement",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {}
    }
  ]
}
```
3. Condition Block Structure
```json
"Condition": {
   "ConditionOperator": {
      "ConditionKey": "ConditionValue"
   }
}
```
ConditionOperator → Type of comparison

ConditionKey → AWS context key

ConditionValue → Value to compare

4. Condition Operator Types

4.1 String Operators

StringEquals

StringNotEquals

StringEqualsIgnoreCase

StringLike

StringNotLike

Example:

```json
"Condition": {
  "StringEquals": {
    "aws:RequestedRegion": "us-east-1"
  }
}
```
4.2 Numeric Operators
NumericEquals

NumericLessThan

NumericGreaterThan

NumericLessThanEquals

NumericGreaterThanEquals

Example:

```json
"Condition": {
  "NumericLessThan": {
    "aws:MultiFactorAuthAge": "3600"
  }
}
```
4.3 Date Operators
DateEquals

DateLessThan

DateGreaterThan

Example:

```json
"Condition": {
  "DateLessThan": {
    "aws:CurrentTime": "2026-12-31T23:59:59Z"
  }
}
```
4.4 Boolean Operator
Bool

Example:

```json
"Condition": {
  "Bool": {
    "aws:MultiFactorAuthPresent": "true"
  }
}
```
4.5 IP Address Operators
IpAddress

NotIpAddress

Example:

json
```
"Condition": {
  "IpAddress": {
    "aws:SourceIp": "203.0.113.0/24"
  }
}
```
4.6 ARN Operators
ArnEquals

ArnLike

ArnNotEquals

ArnNotLike

Example:

```json
"Condition": {
  "ArnLike": {
    "aws:SourceArn": "arn:aws:lambda:us-east-1:123456789012:function:*"
  }
}
```
4.7 Null Operator
Used to check if a key exists.

```json
"Condition": {
  "Null": {
    "aws:RequestTag/Environment": "false"
  }
}
```

5. Global Condition Keys
Common keys:

aws:SourceIp

aws:CurrentTime

aws:PrincipalArn

aws:RequestedRegion

aws:MultiFactorAuthPresent

aws:SecureTransport

aws:SourceVpc

aws:RequestTag/tag-key

aws:ResourceTag/tag-key

6. Service-Specific Condition Keys
Examples:

s3:prefix

ec2:InstanceType

kms:ViaService

lambda:FunctionArn

Example:

```json
"Condition": {
  "StringEquals": {
    "ec2:InstanceType": "t3.micro"
  }
}
```
7. Multi-Value Operators
ForAnyValue:StringEquals

ForAllValues:StringEquals

Example:

```json
"Condition": {
  "ForAnyValue:StringEquals": {
    "aws:TagKeys": ["Environment", "Owner"]
  }
}
```
8. Policy Evaluation Logic
Evaluation order:

Explicit Deny

Explicit Allow

Default Deny

Important rules:

Deny overrides Allow

Conditions must evaluate TRUE

Multiple conditions = logical AND

9. Real-World Examples
Enforce HTTPS for S3
```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Resource": "*",
  "Condition": {
    "Bool": {
      "aws:SecureTransport": "false"
    }
  }
}
```
Restrict to Specific Region
```json
{
  "Effect": "Deny",
  "Action": "*",
  "Resource": "*",
  "Condition": {
    "StringNotEquals": {
      "aws:RequestedRegion": "us-east-1"
    }
  }
}
```
10. ABAC (Tag-Based Access Control)
Example:

```json
{
  "Effect": "Allow",
  "Action": "ec2:StartInstances",
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "aws:ResourceTag/Department": "${aws:PrincipalTag/Department}"
    }
  }
}
```
11. Best Practices
Use Explicit Deny for guardrails

Enforce MFA for admin roles

Restrict by IP and Region

Enforce HTTPS

Use ABAC for scalability

Avoid wildcard actions

12. Troubleshooting

Tools:

* IAM Policy Simulator

* AWS CloudTrail

* Access Analyzer

Common Issues:

* Typo in condition key

* Case sensitivity problems

* Explicit Deny overriding Allow

* Missing required tags

yaml

---
