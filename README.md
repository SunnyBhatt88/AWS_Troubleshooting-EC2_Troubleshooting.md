# EC2 Troubleshooting Guide

## Overview

Amazon EC2 (Elastic Compute Cloud) provides scalable virtual servers in the AWS Cloud.

This document covers common EC2 troubleshooting activities relevant to Application Support and Production Support.

## Common EC2 Issues

* EC2 instance is unreachable
* Instance status checks failed
* High CPU utilization
* High memory utilization
* High disk utilization
* Application is not responding
* Network connectivity issues
* Security Group configuration issues
* IAM permission issues
* Application or system errors in logs

## 1. Check EC2 Instance Status

First, verify the EC2 instance state from the AWS Management Console.

Common states:

* Running
* Stopped
* Pending
* Rebooting
* Terminated

If the instance is not in the `Running` state, investigate the reason before proceeding.

## 2. Check Status Checks

EC2 provides status checks to identify infrastructure or instance-level problems.

Check:

**EC2 → Instances → Select Instance → Status Checks**

Important checks include:

* System Status Check
* Instance Status Check

If a status check fails, investigate the underlying infrastructure or operating system issue.

## 3. Check CPU Utilization

High CPU utilization can affect application performance.

Check CPU metrics using:

**EC2 → Monitoring → CPUUtilization**

You can also check the metric through Amazon CloudWatch.

### Troubleshooting Steps

1. Check current CPU utilization.
2. Identify when the CPU increased.
3. Check application and system processes.
4. Review application logs.
5. Check for batch jobs or scheduled processes.
6. Identify the process consuming high CPU.
7. Take appropriate corrective action.
8. Validate application performance after resolution.

## 4. Check Memory Utilization

EC2 does not provide memory utilization as a standard basic metric.

Where monitoring is configured using the CloudWatch Agent, memory metrics can be reviewed in CloudWatch.

For Linux systems, basic commands can also be used:

```bash
free -m
top
ps -ef
```

Check for:

* High memory consumption
* Memory-intensive processes
* Application processes consuming excessive resources
* Swap utilization

## 5. Check Disk Utilization

Check disk usage on Linux servers using:

```bash
df -h
```

To identify large directories:

```bash
du -sh /*
```

For a specific directory:

```bash
du -sh /var/log/*
```

### Troubleshooting Steps

1. Check filesystem utilization.
2. Identify the filesystem with high usage.
3. Find large files or directories.
4. Check application and system logs.
5. Verify whether log rotation is working.
6. Remove or archive files according to the approved process.
7. Recheck disk utilization.

**Production Note:** Do not delete application or system files without confirming their purpose and following the approved change/incident process.

## 6. Check Application Connectivity

If an application running on an EC2 instance is not accessible:

Check:

* EC2 instance status
* Application/service status
* Server CPU and memory
* Disk space
* Application logs
* Network connectivity
* Security Groups
* Network ACLs
* Application port configuration

Useful Linux commands:

```bash
ping <hostname>
```

```bash
curl -I http://<hostname>:<port>
```

```bash
netstat -tulnp
```

or:

```bash
ss -tulnp
```

## 7. Security Group Troubleshooting

Security Groups control inbound and outbound traffic for EC2 instances.

Verify:

* Correct inbound port
* Correct source IP/range
* Correct protocol
* Required outbound access
* Application listening on the expected port

For example, if an application runs on port `8080`, verify that the required traffic is allowed and that the application is actually listening on that port.

## 8. IAM Permission Issues

If an EC2-based application cannot access an AWS resource, check the IAM configuration.

Validate:

* IAM Role attached to the EC2 instance
* Required permissions
* Resource policy, where applicable
* Access denied errors
* CloudTrail events, where appropriate

Example issue:

```text
AccessDenied
User is not authorized to perform the requested action
```

Do not provide excessive permissions just to resolve an issue. Follow the principle of least privilege.

## 9. Check CloudWatch Metrics

CloudWatch can be used to monitor EC2 resources and application-related metrics.

Common metrics include:

* CPUUtilization
* NetworkIn
* NetworkOut
* StatusCheckFailed
* StatusCheckFailed_Instance
* StatusCheckFailed_System

Where additional monitoring is configured, CloudWatch can also provide memory, disk, and application log information.

## 10. Log Analysis

For application or system issues, review the relevant logs.

Common Linux locations include:

```bash
/var/log/
```

Application logs may be located in application-specific directories.

Useful commands:

```bash
tail -f application.log
```

```bash
grep -i "error" application.log
```

```bash
grep -i "exception" application.log
```

Look for:

* Error messages
* Exceptions
* Connection failures
* Timeout errors
* Authentication failures
* Resource-related errors
* Application startup failures

## 11. Common EC2 Troubleshooting Flow

```text
Issue Reported
      ↓
Check EC2 Instance Status
      ↓
Check Status Checks
      ↓
Check CPU / Memory / Disk
      ↓
Check Application Status
      ↓
Review Logs
      ↓
Check Network / Security Groups
      ↓
Check IAM Permissions
      ↓
Identify Root Cause
      ↓
Apply Approved Resolution
      ↓
Validate Application
      ↓
Document Resolution
```

## 12. Production Support Example

### Issue

Application hosted on an EC2 instance is not responding.

### Investigation

1. Checked EC2 instance status.
2. Verified instance status checks.
3. Checked CPU utilization in CloudWatch.
4. Checked disk space using `df -h`.
5. Verified application/service status.
6. Reviewed application logs.
7. Checked network connectivity and required ports.
8. Identified the probable root cause.
9. Applied the approved resolution.
10. Performed application and transaction validation.

### Result

Application availability was restored and post-resolution monitoring was performed to confirm system stability.

## Best Practices

* Monitor EC2 resources regularly.
* Use CloudWatch for monitoring and alerting.
* Review application and system logs during incidents.
* Follow least-privilege IAM practices.
* Validate Security Group rules carefully.
* Document recurring issues and their resolutions.
* Follow approved change and incident management processes.
* Avoid making unapproved changes directly in production.
* Perform post-resolution validation after every major incident.

## Disclaimer

This document is created for learning, hands-on practice, and demonstration of AWS troubleshooting knowledge. Production changes should always follow the organization's approved incident, change, security, and access-management procedures.
