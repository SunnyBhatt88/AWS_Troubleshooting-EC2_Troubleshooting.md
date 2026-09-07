# S3 Troubleshooting Guide

## Overview

Amazon S3 (Simple Storage Service) is an object storage service used to store and retrieve files, application data, backups, logs, reports, and other objects.

This document covers common S3 troubleshooting activities relevant to Application Support and Production Support environments.

## Common S3 Issues

* Access Denied errors
* Unable to upload objects
* Unable to download objects
* Incorrect bucket permissions
* IAM permission issues
* Bucket policy issues
* Object not found
* Incorrect object path or key
* Application unable to access S3
* Connectivity or configuration issues

## 1. Check Bucket and Object Details

When an application reports an S3 issue, first verify:

* AWS account/environment
* S3 bucket name
* Object key/path
* Region
* Object existence
* Application configuration

Example:

```text
Bucket:
production-application-data

Object:
reports/orders/order_10001.json
```

A common issue is an incorrect bucket name or object key.

## 2. Access Denied Troubleshooting

A common S3 error is:

```text
AccessDenied
```

Check:

1. IAM role or user being used by the application.
2. IAM permissions.
3. S3 bucket policy.
4. Object-level permissions where applicable.
5. AWS account and region.
6. Any applicable restrictions or explicit denies.

Typical permissions may include:

```text
s3:GetObject
s3:PutObject
s3:ListBucket
```

Only grant the permissions required by the application.

## 3. IAM Role Troubleshooting

Applications running on EC2 may use an IAM role to access S3.

Verify:

* Correct IAM role is attached to the EC2 instance.
* Required S3 permissions are available.
* There is no explicit Deny.
* The application is using the expected AWS identity.
* The requested bucket/object is covered by the policy.

Example issue:

```text
AccessDenied: Access Denied
```

The support team should compare the required application operation with the permissions assigned to the IAM role.

## 4. Bucket Policy Troubleshooting

An S3 bucket policy can control access to bucket resources.

When investigating an access issue, check for:

* Explicit Deny statements
* Incorrect AWS account
* Incorrect IAM role/user
* Incorrect bucket ARN
* Incorrect object ARN
* Conditions restricting access

Example resource formats:

```text
arn:aws:s3:::bucket-name
```

```text
arn:aws:s3:::bucket-name/*
```

The bucket and object resources should match the operation being performed.

## 5. Object Not Found

If an application cannot find an object, verify:

1. Bucket name.
2. Object key.
3. File name.
4. File extension.
5. Case sensitivity.
6. Environment.
7. Region.
8. Whether the object actually exists.

Example:

```text
Expected:
reports/orders/Order10001.json

Actual:
reports/orders/order10001.json
```

Object keys are case-sensitive, so these can represent different objects.

## 6. Upload Troubleshooting

If an application cannot upload a file to S3, check:

* `s3:PutObject` permission
* Bucket name
* Object key/path
* File size
* Application configuration
* IAM role
* Bucket policy
* Application logs
* AWS error message

Example troubleshooting flow:

```text
Upload Failure
      ↓
Check Application Logs
      ↓
Check Bucket Name
      ↓
Check Object Key
      ↓
Check IAM Role
      ↓
Check s3:PutObject
      ↓
Check Bucket Policy
      ↓
Retry / Validate
```

## 7. Download Troubleshooting

If an application cannot download an S3 object, check:

* Object exists
* Correct bucket
* Correct object key
* `s3:GetObject` permission
* IAM role/user
* Bucket policy
* Application configuration
* Application logs

A typical error may be:

```text
NoSuchKey
```

This generally indicates that the requested object key could not be found.

## 8. Application Integration with S3

Applications may use S3 for:

* File uploads
* Reports
* Order files
* Product/catalog files
* Application documents
* Data exchange
* Logs
* Backups

During troubleshooting, verify the complete flow:

```text
Application
     ↓
IAM Authentication
     ↓
S3 Request
     ↓
Bucket
     ↓
Object
```

A failure at any stage can cause the application operation to fail.

## 9. AWS CLI Validation

Where authorized access is available, S3 can also be validated using the AWS CLI.

List buckets:

```bash
aws s3 ls
```

List objects:

```bash
aws s3 ls s3://bucket-name/
```

Check a specific path:

```bash
aws s3 ls s3://bucket-name/path/
```

**Production Note:** Use CLI commands only with approved credentials and access. Do not expose access keys, secrets, or sensitive production information.

## 10. CloudWatch and S3 Monitoring

CloudWatch can be used to monitor relevant AWS resource and application activity.

During an incident, correlate:

* Application logs
* CloudWatch metrics
* S3 access behavior
* IAM activity
* AWS error messages

This can help determine whether the issue is application-side, permission-related, or related to the AWS resource configuration.

## 11. Production Support Example

### Issue

An application is unable to upload an order file to an S3 bucket.

### Investigation

1. Checked application logs.
2. Identified an `AccessDenied` error.
3. Verified the target S3 bucket.
4. Checked the EC2 instance IAM role.
5. Reviewed the required S3 permissions.
6. Checked the bucket policy for any explicit Deny.
7. Identified the permission/configuration issue.
8. Followed the approved access/change process.
9. Retested the upload.
10. Validated that the file was successfully stored in S3.

### Result

The S3 integration was validated after the approved configuration change and application functionality was restored.

## Best Practices

* Follow least-privilege access.
* Never share AWS access keys or secrets.
* Verify bucket and object names carefully.
* Use appropriate IAM roles for applications.
* Review bucket policies carefully.
* Avoid making direct production changes without approval.
* Monitor application logs during S3 incidents.
* Document recurring S3 issues and resolutions.
* Validate application functionality after resolution.

## Summary

S3 troubleshooting requires checking the complete application-to-storage flow, including:

* Application configuration
* Bucket and object details
* IAM permissions
* IAM roles
* Bucket policies
* Object availability
* Application logs
* AWS monitoring

A structured troubleshooting approach helps identify S3 access, upload, download, and integration issues efficiently.
