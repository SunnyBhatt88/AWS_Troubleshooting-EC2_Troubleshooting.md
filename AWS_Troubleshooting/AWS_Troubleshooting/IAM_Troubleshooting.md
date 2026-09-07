# IAM Troubleshooting Guide

## Overview

AWS Identity and Access Management (IAM) is used to control access to AWS resources and services.

IAM troubleshooting is important for Application Support and Production Support teams when applications or users receive permission-related errors.

This document covers common IAM troubleshooting scenarios involving EC2, S3, application access, and AWS services.

## Common IAM Issues

* Access Denied errors
* Unauthorized API operations
* Incorrect IAM permissions
* Incorrect IAM role attached to EC2
* Missing permissions
* Explicit Deny in a policy
* Incorrect resource ARN
* Application unable to access AWS services
* User or role authentication issues

## 1. Understand the IAM Access Flow

For an application running on EC2, access commonly follows this flow:

```text
Application
     ↓
EC2 Instance
     ↓
IAM Role
     ↓
IAM Policy
     ↓
AWS Service
     ↓
Resource
```

For example:

```text
EC2 Application
      ↓
IAM Role
      ↓
S3 Permissions
      ↓
S3 Bucket
      ↓
S3 Object
```

A problem at any stage can result in an authorization failure.

## 2. AccessDenied Troubleshooting

A common IAM error is:

```text
AccessDenied
```

Example:

```text
User is not authorized to perform this action
```

### Troubleshooting Steps

1. Identify the affected application or user.
2. Identify the AWS operation being performed.
3. Identify the IAM user or role involved.
4. Check attached policies.
5. Verify the required permission.
6. Check the resource ARN.
7. Look for explicit Deny statements.
8. Check resource-based policies where applicable.
9. Review CloudTrail events when available.
10. Follow the approved resolution process.

## 3. IAM Role Attached to EC2

Applications running on EC2 can use an IAM role to access AWS services.

Check:

**AWS Console → EC2 → Instances → Select Instance → Security → IAM Role**

Verify:

* Correct IAM role is attached.
* Role is intended for the application.
* Required permissions are available.
* No unnecessary permissions have been granted.

Example:

```text
EC2 Instance
     ↓
Application Server Role
     ↓
S3 Access
```

If the application suddenly starts receiving `AccessDenied`, verify whether the IAM role or its policies were recently changed.

## 4. IAM Policies

IAM policies define what actions are allowed or denied.

A policy generally contains:

* Effect
* Action
* Resource
* Conditions, where applicable

Example:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::example-bucket/*"
}
```

This example allows the specified principal to perform `s3:GetObject` against objects covered by the resource ARN.

## 5. Missing Permission

Example:

```text
AccessDenied: User is not authorized to perform s3:GetObject
```

Investigation:

1. Identify the IAM role/user.
2. Check attached policies.
3. Verify whether `s3:GetObject` is allowed.
4. Verify the bucket/object ARN.
5. Check for an explicit Deny.
6. Check the S3 bucket policy.
7. Follow the approved process for any permission change.

Do not immediately add administrator-level permissions to resolve the issue.

## 6. Explicit Deny

An explicit Deny takes precedence over an Allow.

Example:

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Resource": "*"
}
```

If an applicable policy explicitly denies the operation, simply adding another Allow policy may not resolve the problem.

Check:

* Identity-based policies
* Resource-based policies
* Permission boundaries
* Service control policies, where applicable
* Relevant conditions

## 7. Resource ARN Troubleshooting

Incorrect resource ARNs are a common cause of permission problems.

For example, an S3 bucket and its objects use different ARN patterns:

```text
arn:aws:s3:::bucket-name
```

and:

```text
arn:aws:s3:::bucket-name/*
```

When troubleshooting, verify that the policy resource matches the AWS operation being performed.

## 8. IAM and S3 Troubleshooting

For an S3 access issue, investigate the complete flow:

```text
Application
     ↓
EC2 IAM Role
     ↓
IAM Policy
     ↓
S3 Bucket Policy
     ↓
S3 Bucket
     ↓
S3 Object
```

Check:

* IAM role
* IAM permissions
* Bucket policy
* Resource ARN
* Object key
* Explicit Deny
* Application logs

## 9. IAM and EC2 Troubleshooting

If an EC2-hosted application cannot access another AWS service:

Check:

1. EC2 instance status.
2. IAM role attached to EC2.
3. Required permissions.
4. Resource ARN.
5. Application configuration.
6. AWS service response.
7. CloudTrail events where appropriate.

Example:

```text
Application → EC2 → IAM Role → S3
```

If the IAM role does not have the required S3 permission, the application may receive an authorization error.

## 10. CloudTrail for IAM Investigation

AWS CloudTrail can help investigate API activity and identify authorization failures.

Useful information may include:

* User or role identity
* AWS service
* API operation
* Time of request
* Source information
* Error code
* Error message

Example:

```text
Event:
GetObject

Result:
AccessDenied
```

This information can help correlate the application incident with the AWS API request.

## 11. Production Support Example

### Issue

An application running on an EC2 instance is unable to retrieve files from an S3 bucket.

### Error

```text
AccessDenied
```

### Investigation

1. Checked application logs.
2. Confirmed the application was requesting an S3 object.
3. Identified the EC2 IAM role.
4. Reviewed IAM policies.
5. Checked the required `s3:GetObject` permission.
6. Verified the S3 bucket and object ARN.
7. Reviewed the bucket policy.
8. Checked for explicit Deny statements.
9. Reviewed relevant CloudTrail information.
10. Identified the permission/configuration issue.
11. Followed the approved change process.
12. Retested the application.

### Result

S3 access was successfully validated after the approved configuration change.

## 12. IAM Troubleshooting Flow

```text
Access Issue Reported
        ↓
Identify User / Application
        ↓
Identify IAM Role or User
        ↓
Identify AWS API Operation
        ↓
Check IAM Policy
        ↓
Check Resource ARN
        ↓
Check Explicit Deny
        ↓
Check Resource Policy
        ↓
Review CloudTrail
        ↓
Apply Approved Resolution
        ↓
Retest Application
        ↓
Monitor and Document
```

## Best Practices

* Follow the principle of least privilege.
* Use IAM roles for applications where appropriate.
* Avoid using root credentials for normal operations.
* Never share AWS access keys or secrets.
* Do not grant AdministratorAccess just to bypass an authorization issue.
* Review explicit Deny statements carefully.
* Validate resource ARNs.
* Review CloudTrail during relevant investigations.
* Follow approved access and change-management procedures.
* Document recurring IAM issues and their resolutions.

## Key IAM Concepts

| Concept    | Purpose                                                    |
| ---------- | ---------------------------------------------------------- |
| IAM User   | Identity representing a person or service where applicable |
| IAM Role   | Identity that can be assumed by trusted entities           |
| IAM Policy | Defines permissions                                        |
| Action     | AWS operation being allowed or denied                      |
| Resource   | AWS resource to which the permission applies               |
| Effect     | Allow or Deny                                              |
| Condition  | Additional rule controlling when access applies            |
| ARN        | Unique identifier for AWS resources                        |
| CloudTrail | Records AWS API activity                                   |

## Summary

IAM troubleshooting requires understanding the relationship between identities, permissions, policies, and AWS resources.

A structured investigation should verify:

* IAM user or role
* Required action
* IAM policies
* Resource ARN
* Explicit Deny
* Resource-based policies
* CloudTrail activity
* Application logs

Following least-privilege principles and approved change procedures helps maintain secure and reliable AWS environments.

## Disclaimer

This document is created for learning, hands-on practice, and demonstration of AWS troubleshooting knowledge. Production IAM changes should always follow the organization's approved security, access-management, incident, and change-management procedures.
