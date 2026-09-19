# Task 4 — Security Tool Mastery Project

## 1. Project Overview

This project demonstrates practical use of AWS security tools for monitoring, assessment, detection, and protection of cloud resources.

The objective was to gain hands-on experience with security tools used in AWS environments, configure security controls, perform security assessments, analyze findings and alerts, identify potential cloud security issues, and document the results in a practical security portfolio.

The lab was conducted in the AWS US East (N. Virginia) region (`us-east-1`) using a dedicated cloud-security laboratory environment.

The following AWS security services and resources were assessed:

- AWS IAM
- IAM Access Analyzer
- AWS Config
- AWS CloudTrail
- Amazon CloudWatch
- Amazon SNS
- Amazon S3
- Amazon EC2
- Amazon VPC
- Security Groups
- GuardDuty availability assessment

The project focuses on preventive, detective, and monitoring controls rather than relying on a single security mechanism.

---

## 2. Objectives

The main objectives of the project were:

1. Identify security tools relevant to cloud environments.
2. Configure tools for monitoring cloud resources and activities.
3. Perform basic security assessments using selected tools.
4. Analyze logs, alerts, and security findings.
5. Identify and document potential cloud security issues.
6. Create a practical portfolio demonstrating security tool usage and findings.

---

## 3. AWS Lab Environment

### Region

- AWS Region: US East (N. Virginia)
- Region code: `us-east-1`

### Main resources

| Resource | Configuration |
|---|---|
| VPC | `CloudSecurityLab-VPC` |
| VPC CIDR | `10.0.0.0/16` |
| Subnet | `CloudSecurityLab-Public-Subnet` |
| Subnet CIDR | `10.0.1.0/24` |
| Internet Gateway | `CloudSecurityLab-IGW` |
| Security Group | `CloudSecurityLab-SG` |
| EC2 Instance | `CloudSecurityLab-EC2-Test` |
| S3 Bucket | `cloud-security-lab-2026-847291` |
| CloudTrail | `CloudSecurityLab-Trail` |
| CloudWatch Alarm | `CloudSecurityLab-UnauthorizedAccess-Alarm` |
| IAM Access Analyzer | `CloudSecurityLab-AccessAnalyzer` |
| AWS Config Rules | 4 managed security rules |

---

# 4. Tool 1 — IAM

## Purpose

AWS Identity and Access Management (IAM) was used to control access to AWS resources and demonstrate the principle of least privilege.

### Configuration

An IAM user named:

`cloud-security-lab-user`

was created for the laboratory environment.

The user was placed in:

`CloudSecurityLab-Auditors`

The AWS managed:

`SecurityAudit`

policy

was attached through the group.

Console access was disabled for this audit-oriented identity.

### Security relevance

The SecurityAudit policy provides read-oriented permissions required for reviewing security configuration and identifying security-related settings.

Using groups instead of attaching policies independently to individual users also improves administration and policy consistency.

### Security consideration

The laboratory audit identity should not retain unnecessary long-term credentials. Any access key associated with the audit user should be removed when it is no longer required.

---

# 5. Tool 2 — IAM Access Analyzer

## Purpose

IAM Access Analyzer was used to identify resources that could potentially be accessed from outside the configured zone of trust.

### Configuration

Analyzer name:

`CloudSecurityLab-AccessAnalyzer`

Finding type:

`Resource analysis – External access`

Zone of trust:

Current AWS account

Region:

`us-east-1`

### Result

The analyzer was successfully created and reached:

`Active`

The findings count was:

`0`

### Interpretation

The zero-finding result indicates that the analyzer did not identify unintended external-access findings within the analyzed scope at the time of assessment.

This does not mean that the account has zero security risks. Access Analyzer focuses on specific resource-based external access conditions and should therefore be combined with other security controls.

### Security value

IAM Access Analyzer can help identify:

- Unintended cross-account access
- Public resource access
- Resource policies that provide external access
- Overly broad resource-based permissions

---

# 6. Tool 3 — AWS Config

## Purpose

AWS Config was configured to continuously record resource configuration and evaluate resources against security rules.

### Recording configuration

Recording strategy:

`All resource types with customizable overrides`

Recording frequency:

`Continuous`

IAM global resource types were included.

Configuration history was delivered to:

`config-bucket-351668480264`

### Security rules configured

Four AWS managed rules were enabled:

1. `s3-bucket-public-read-prohibited`
2. `s3-bucket-server-side-encryption-enabled`
3. `root-account-mfa-enabled`
4. `restricted-ssh`

### Compliance results

All four configured rules returned compliant results during the assessment.

| AWS Config Rule | Security Objective | Result |
|---|---|---|
| `s3-bucket-public-read-prohibited` | Prevent public S3 read access | COMPLIANT |
| `s3-bucket-server-side-encryption-enabled` | Require S3 encryption | COMPLIANT |
| `root-account-mfa-enabled` | Protect root account with MFA | COMPLIANT |
| `restricted-ssh` | Restrict SSH exposure | COMPLIANT |

### Security value

AWS Config provides continuous configuration visibility and allows security teams to detect configuration drift.

For example, a security group that is later changed to allow SSH from `0.0.0.0/0` could be detected by the restricted SSH rule.

Similarly, a change that removes encryption from an S3 bucket can be identified through configuration evaluation.

---

# 7. Tool 4 — Amazon S3 Security Assessment

## Bucket assessed

`cloud-security-lab-2026-847291`

### Security configuration

The bucket was configured with:

- Block Public Access enabled
- ACLs disabled
- Bucket-owner-controlled object ownership
- Server-side encryption enabled
- S3 Bucket Key enabled in the observed configuration

### Security assessment

The S3 configuration was evaluated using AWS Config and IAM Access Analyzer together with direct review of the bucket settings.

The bucket was not intentionally configured for public access.

### Security recommendations

Sensitive objects should never be made publicly accessible unless there is a documented business requirement.

Additional recommended controls include:

- Restrictive bucket policies
- Least-privilege IAM policies
- Encryption at rest
- Versioning for important data
- Access logging or CloudTrail data-event monitoring where required
- Lifecycle policies
- Continuous configuration assessment

---

# 8. Tool 5 — Amazon EC2 and Security Groups

## EC2 resource

Instance:

`CloudSecurityLab-EC2-Test`

Instance type:

`t3.micro`

Operating system:

Amazon Linux 2023

### Security configuration

The instance used:

- `CloudSecurityLab-SG`
- IMDSv2 required
- 8 GiB gp3 storage
- A dedicated laboratory key pair
- Restricted inbound SSH access

### Security Group

The inbound SSH rule was restricted to a specific `/32` source address rather than:

`0.0.0.0/0`

This reduces exposure to arbitrary Internet-based SSH connection attempts.

### Security relevance

SSH access from the entire Internet increases attack surface because automated scanners continuously probe publicly reachable SSH services.

Restricting SSH to an administrative source address is therefore a practical network-level preventive control.

### Additional recommendation

For production environments, SSH access should preferably be replaced or supplemented with controlled administrative access mechanisms such as AWS Systems Manager where appropriate.

---

# 9. Tool 6 — AWS CloudTrail

## Purpose

AWS CloudTrail was used to record AWS API activity and support security investigation.

### Trail

`CloudSecurityLab-Trail`

### Configuration

The trail was configured with:

- Multi-region logging
- Active logging
- S3 log storage
- CloudWatch Logs integration
- Log group:
  `/aws/cloudtrail/CloudSecurityLab`

CloudTrail activity can provide evidence of administrative and API operations performed against AWS resources.

### Security investigation use cases

Examples of security-relevant API activity include:

- Console login activity
- Creation of access keys
- IAM policy changes
- Security group modifications
- S3 bucket policy changes
- CloudTrail configuration changes
- Attempts to stop logging

CloudTrail is therefore an important source of evidence during incident investigation.

---

# 10. Tool 7 — Amazon CloudWatch

## Purpose

CloudWatch was used to detect security-related events from CloudTrail logs.

### Alarm

`CloudSecurityLab-UnauthorizedAccess-Alarm`

### Detection logic

The CloudWatch Logs metric filter searched for:

- `AccessDenied`
- `UnauthorizedOperation`

Aggregation:

`COUNT(*)`

Threshold:

`Greater than 0`

Evaluation schedule:

Every 15 minutes

Lookback period:

10 minutes

### Observed result

The alarm entered:

`In alarm`

when matching events were present in the monitored logs.

### Security interpretation

This demonstrates a basic detective control:

1. AWS activity generates CloudTrail events.
2. CloudTrail delivers events to CloudWatch Logs.
3. CloudWatch evaluates the log stream.
4. Matching unauthorized-access activity increments the metric.
5. The alarm changes state.
6. SNS can deliver a notification.

This creates a practical detection chain instead of relying only on manual log inspection.

---

# 11. Tool 8 — Amazon SNS

SNS was configured as the notification mechanism associated with the CloudWatch security alarm.

The purpose of SNS is to provide an alert channel when the CloudWatch alarm detects the defined security condition.

The subscription status was observed as pending confirmation during configuration.

This is important operationally because an SNS subscription must be confirmed before the intended email notification workflow can function normally.

---

# 12. Tool 9 — Amazon GuardDuty

## Assessment status

GuardDuty was investigated as a potential threat-detection tool.

However, the AWS account redirected the GuardDuty console to:

`Complete your account setup`

The page indicated that the account either had not completed registration or was operating under an account plan with limited service access.

The account registration was attempted through the provided AWS registration option, but GuardDuty remained inaccessible.

### Decision

GuardDuty was not enabled by upgrading the AWS account solely for this project.

This was treated as an account/service-access limitation rather than a configuration failure.

### Security implication

In a production AWS environment, GuardDuty should be evaluated as part of a broader threat-detection architecture where account eligibility and pricing requirements permit.

The limitation is explicitly documented rather than claiming that GuardDuty was successfully deployed.

---

# 13. Security Findings

The practical assessment produced the following findings.

| ID | Finding | Evidence | Risk | Status |
|---|---|---|---|---|
| F-01 | No unintended external-access findings detected | IAM Access Analyzer | Reduced external-access exposure | PASS |
| F-02 | S3 public-read control compliant | AWS Config | Public data exposure risk reduced | PASS |
| F-03 | S3 encryption control compliant | AWS Config | Encryption control present | PASS |
| F-04 | Root MFA control compliant | AWS Config | Account takeover risk reduced | PASS |
| F-05 | SSH restriction control compliant | AWS Config | Internet-wide SSH exposure reduced | PASS |
| F-06 | Unauthorized-access alarm configured | CloudWatch | Detection capability established | PASS |
| F-07 | SNS notification workflow configured | SNS | Alert delivery capability established | CONFIGURED |
| F-08 | GuardDuty unavailable | AWS account access page | Threat detection service not assessed | LIMITATION |
| F-09 | Long-term access key exists for audit identity | IAM review | Unnecessary credential exposure if unused | REVIEW |

---

# 14. Security Metrics

The following metrics can be used to continuously evaluate the security posture.

### IAM metrics

- Number of IAM policies containing wildcard permissions
- Number of users with unnecessary console access
- Number of unused access keys
- Number of access keys older than the organization's defined threshold
- Number of external-access findings

### AWS Config metrics

- Number of compliant resources
- Number of non-compliant resources
- Number of critical configuration violations
- Percentage of monitored resources evaluated successfully

### CloudTrail metrics

- CloudTrail logging coverage
- Number of unauthorized API attempts
- Number of security-group modification events
- Number of IAM policy changes
- Number of attempts to disable logging

### CloudWatch metrics

- Number of security alarm triggers
- Mean time to detect
- Mean time to acknowledge
- Mean time to contain

### S3 metrics

- Number of publicly accessible buckets
- Number of unencrypted buckets
- Number of buckets with overly broad policies

---

# 15. Recommended Security Improvements

Based on the practical assessment, the following improvements are recommended:

## 15.1 IAM

- Apply least privilege.
- Remove unused credentials.
- Avoid unnecessary long-term access keys.
- Use IAM roles where possible.
- Regularly review IAM Access Analyzer findings.

## 15.2 Network Security

- Restrict administrative ports to trusted source networks.
- Avoid `0.0.0.0/0` for SSH and other administrative services.
- Use network segmentation.
- Consider private subnets for production workloads.

## 15.3 S3

- Keep Block Public Access enabled.
- Use restrictive bucket policies.
- Enable encryption.
- Monitor changes to bucket policies.
- Use versioning for important data.

## 15.4 Monitoring

- Maintain multi-region CloudTrail logging.
- Protect CloudTrail configuration from unauthorized changes.
- Send security-relevant events to CloudWatch.
- Configure alerts for unauthorized API activity.
- Confirm SNS subscriptions.

## 15.5 Threat Detection

- Evaluate GuardDuty when account/service access is available.
- Integrate findings with an incident-response process.
- Establish defined response procedures for high-severity findings.

---

# 16. Defense-in-Depth Architecture

The laboratory demonstrates multiple security layers:

```text
                    AWS Cloud Environment
                             |
        +--------------------+--------------------+
        |                    |                    |
       IAM              Network Security       Storage
        |                    |                    |
 Access Analyzer        VPC / Security Group    S3
        |                    |                    |
        +--------------------+--------------------+
                             |
                       CloudTrail
                             |
                       CloudWatch
                             |
                           SNS
                             |
                    Security Notification
