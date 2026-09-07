# CloudWatch Monitoring Guide

## Overview

Amazon CloudWatch is an AWS monitoring and observability service used to monitor AWS resources, applications, system performance, and operational health.

CloudWatch is useful for Application Support and Production Support teams to identify performance issues, investigate incidents, monitor resource utilization, and troubleshoot application problems.

## Key CloudWatch Monitoring Areas

Common CloudWatch monitoring activities include:

* EC2 resource monitoring
* CPU utilization monitoring
* Network monitoring
* Status check monitoring
* CloudWatch Alarms
* Application and system log monitoring
* Log Groups and Log Streams
* Incident troubleshooting
* Performance monitoring

## 1. EC2 CPU Monitoring

CPU utilization is one of the most commonly monitored EC2 metrics.

Navigate to:

**AWS Console → CloudWatch → Metrics → EC2**

Common metric:

```text
CPUUtilization
```

### Troubleshooting High CPU

If CPU utilization is consistently high:

1. Check the CPUUtilization metric.
2. Identify when the CPU increase started.
3. Check whether the increase is temporary or continuous.
4. Review application and system logs.
5. Check running processes on the Linux server.
6. Identify resource-intensive processes.
7. Check for scheduled jobs or batch processes.
8. Take the appropriate approved corrective action.
9. Continue monitoring after resolution.

Useful Linux commands:

```bash
top
```

```bash
ps -ef
```

## 2. Network Monitoring

CloudWatch provides network-related EC2 metrics that can help identify unusual network activity.

Common metrics include:

```text
NetworkIn
NetworkOut
```

These metrics can help during investigation of:

* Unexpected traffic
* Application connectivity issues
* Network performance problems
* Sudden traffic increases
* Application communication issues

Compare the metric behavior with the time of the reported incident.

## 3. EC2 Status Check Monitoring

CloudWatch can monitor EC2 status check metrics.

Important metrics include:

```text
StatusCheckFailed
StatusCheckFailed_Instance
StatusCheckFailed_System
```

A status check failure may indicate an instance-level or underlying system-level problem.

### Troubleshooting Approach

1. Check the EC2 instance status.
2. Review the failed status check.
3. Check recent CloudWatch metrics.
4. Review system and application logs where accessible.
5. Determine whether the issue is instance-level or infrastructure-related.
6. Follow the approved recovery procedure.
7. Validate the application after recovery.

## 4. CloudWatch Alarms

CloudWatch Alarms can monitor metrics and trigger actions when defined thresholds are reached.

Example:

```text
Metric: CPUUtilization
Threshold: 80%
Condition: Greater than 80%
Evaluation Period: 5 minutes
```

Possible alarm states:

* OK
* ALARM
* INSUFFICIENT_DATA

### Production Support Usage

For example, a CPU alarm can notify the support team when CPU utilization remains above the configured threshold.

The support team can then:

1. Acknowledge the alert.
2. Check the affected EC2 instance.
3. Review CPU metrics.
4. Investigate application processes.
5. Check logs.
6. Identify the root cause.
7. Resolve or escalate the issue.
8. Monitor the instance after resolution.

## 5. CloudWatch Logs

CloudWatch Logs can centralize logs from applications and AWS resources when logging has been configured.

Important concepts:

### Log Group

A logical container used to organize related log streams.

Example:

```text
/application/production
```

### Log Stream

A sequence of log events from a particular source.

Logs can be useful for investigating:

* Application errors
* Exceptions
* Authentication failures
* API failures
* Database connection errors
* Application startup problems
* Timeout issues

## 6. Application Log Troubleshooting

When an application issue is reported:

1. Identify the affected application.
2. Identify the relevant Log Group.
3. Select the appropriate Log Stream.
4. Review logs around the incident time.
5. Search for errors or exceptions.
6. Correlate the logs with CloudWatch metrics.
7. Identify the probable root cause.
8. Apply the approved resolution.
9. Validate the application.

Example error patterns:

```text
ERROR
Exception
Timeout
Connection refused
AccessDenied
Database connection failed
```

## 7. CloudWatch Logs Insights

CloudWatch Logs Insights can be used to search and analyze log data when the relevant logs are available in CloudWatch.

Example query:

```text
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

This can help identify recent application errors.

Another example:

```text
fields @timestamp, @message
| filter @message like /Exception/
| sort @timestamp desc
| limit 20
```

**Note:** Logs Insights queries depend on the log format and the logs available in the environment.

## 8. High CPU Incident Example

### Issue

An application running on an EC2 instance is experiencing slow response times.

### Investigation

1. Checked the EC2 instance status.
2. Reviewed `CPUUtilization` in CloudWatch.
3. Observed sustained high CPU utilization.
4. Checked running processes on the Linux server.
5. Reviewed application logs.
6. Checked for scheduled or batch processes.
7. Identified the probable source of high CPU usage.
8. Followed the approved resolution process.
9. Monitored CPU utilization after resolution.
10. Performed application validation.

### Result

Application performance was validated after the corrective action and CloudWatch monitoring was continued to confirm system stability.

## 9. Correlating Metrics and Logs

CloudWatch becomes more useful when metrics and logs are analyzed together.

For example:

```text
High CPU
   ↓
Check application logs
   ↓
Identify errors/process activity
   ↓
Check network activity
   ↓
Check recent changes
   ↓
Identify probable root cause
   ↓
Resolve and monitor
```

This approach helps reduce troubleshooting time during production incidents.

## 10. Production Support Monitoring Process

A typical monitoring process:

1. Receive monitoring alert or incident.
2. Identify affected AWS resource.
3. Check CloudWatch metrics.
4. Review relevant logs.
5. Check application and server health.
6. Compare current behavior with normal patterns.
7. Identify probable root cause.
8. Resolve or escalate according to the incident process.
9. Validate application functionality.
10. Continue monitoring.
11. Document the incident and resolution.

## Best Practices

* Configure meaningful CloudWatch alarms.
* Avoid unnecessary or overly sensitive alerts.
* Monitor important application and infrastructure metrics.
* Correlate CloudWatch metrics with application logs.
* Investigate alerts within the defined SLA.
* Maintain appropriate log retention.
* Avoid storing sensitive information unnecessarily in logs.
* Document recurring alerts and their resolutions.
* Follow approved production change procedures.
* Continue monitoring after incident resolution.

## Key CloudWatch Metrics

| Metric                     | Purpose                                       |
| -------------------------- | --------------------------------------------- |
| CPUUtilization             | Monitor EC2 CPU usage                         |
| NetworkIn                  | Monitor incoming network traffic              |
| NetworkOut                 | Monitor outgoing network traffic              |
| StatusCheckFailed          | Identify EC2 status check failures            |
| StatusCheckFailed_Instance | Identify instance-level status check failures |
| StatusCheckFailed_System   | Identify system-level status check failures   |

## Summary

CloudWatch is an important monitoring tool for Application and Production Support teams.

It helps support teams:

* Monitor AWS infrastructure
* Detect performance issues
* Investigate production incidents
* Analyze application logs
* Configure alerts
* Identify potential root causes
* Validate system health after incident resolution

This document demonstrates practical CloudWatch monitoring and troubleshooting knowledge relevant to AWS-based Application and Production Support environments.
