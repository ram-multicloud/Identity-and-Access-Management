## aws iam policy only uses EC2 and S3 resources tagged with Environment=dev


```json
{
  "Version": "2012-10-17",
  "Statement": [

    {
      "Sid": "AllowS3DevOnly",
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::*",
        "arn:aws:s3:::*/*"
      ],
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Environment": "dev"
        }
      }
    },

    {
      "Sid": "AllowEC2DevOnly",
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Environment": "dev"
        }
      }
    },

    {
      "Sid": "DenyS3IfNotDev",
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::*",
        "arn:aws:s3:::*/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceTag/Environment": "dev"
        }
      }
    },

    {
      "Sid": "DenyEC2IfNotDev",
      "Effect": "Deny",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceTag/Environment": "dev"
        }
      }
    },

    {
      "Sid": "DenyCreateWithoutDevTag",
      "Effect": "Deny",
      "Action": [
        "ec2:RunInstances",
        "ec2:CreateTags",
        "s3:CreateBucket"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestTag/Environment": "dev"
        }
      }
    },

    {
      "Sid": "AllowDescribe",
      "Effect": "Allow",
      "Action": [
        "ec2:Describe*",
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    }

  ]
}

```
