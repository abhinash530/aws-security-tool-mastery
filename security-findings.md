# Security Findings and Recommendations

## Assessment Summary

The AWS security laboratory was assessed using IAM Access Analyzer, AWS Config, CloudTrail, CloudWatch, SNS, S3, EC2, VPC, and Security Groups.

The assessment focused on preventive controls, configuration compliance, logging, monitoring, detection, and credential hygiene.

---

## Finding F-01 — External Access Analysis

**Tool:** IAM Access Analyzer

**Result:** 0 findings

The configured analyzer was active and did not identify unintended external-access findings within the analyzed scope.

**Recommendation:** Continue periodic review of IAM Access Analyzer findings, especially after changes to resource-based policies.

---

## Finding F-02 — S3 Public Access

**Tool:** AWS Config

**Rule:** `s3-bucket-public-read-prohibited`

**Result:** COMPLIANT

The S3 bucket was configured with Block Public Access enabled.

**Recommendation:** Keep S3 public-access prevention enabled unless a documented business requirement exists.

---

## Finding F-03 — S3 Encryption

**Tool:** AWS Config

**Rule:** `s3-bucket-server-side-encryption-enabled`

**Result:** COMPLIANT

Server-side encryption was enabled for the laboratory S3 bucket.

**Recommendation:** Maintain encryption and periodically review bucket policies.

---

## Finding F-04 — Root Account MFA

**Tool:** AWS Config

**Rule:** `root-account-mfa-enabled`

**Result:** COMPLIANT

The root-account MFA security check returned compliant.

**Recommendation:** Maintain MFA protection and avoid routine use of the root account.

---

## Finding F-05 — SSH Exposure

**Tool:** AWS Config

**Rule:** `restricted-ssh`

**Result:** COMPLIANT

The laboratory security group restricted SSH access to a specific source address rather than allowing SSH from the entire Internet.

**Recommendation:** Continue using tightly restricted administrative access. For production environments, consider controlled administration through AWS Systems Manager where appropriate.

---

## Finding F-06 — Unauthorized API Activity Detection

**Tools:** CloudTrail + CloudWatch

**Detection conditions:**

- `AccessDenied`
- `UnauthorizedOperation`

**Aggregation:** `COUNT(*)`

**Threshold:** Greater than 0

**Evaluation:** Every 15 minutes

**Lookback:** 10 minutes

**Result:** Alarm configured and observed in an alarm state when matching events were present.

**Recommendation:** Tune the rule for production workloads to balance detection sensitivity and false positives.

---

## Finding F-07 — Security Notification Workflow

**Tool:** Amazon SNS

**Result:** CONFIGURED

SNS was associated with the CloudWatch security alarm as the notification mechanism.

The subscription was observed as pending confirmation during setup.

**Recommendation:** Confirm the subscription and route security alerts to an actively monitored channel.

---

## Finding F-08 — GuardDuty Availability

**Tool:** Amazon GuardDuty

**Result:** NOT AVAILABLE IN LAB ACCOUNT

The AWS console redirected to an account setup/service-access limitation page when GuardDuty was opened.

**Impact:** GuardDuty could not be evaluated or enabled within this laboratory account.

**Recommendation:** Evaluate GuardDuty when the AWS account has the required service access. The account was not upgraded solely for this project.

---

## Finding F-09 — Long-Term Access Credential

**Tool:** IAM

An access key was observed for the audit-oriented IAM identity during the assessment.

**Risk:** An unnecessary long-term access key can increase credential exposure if it remains active without a business requirement.

**Recommendation:** Delete the access key if it is no longer required. Prefer temporary credentials and IAM roles for operational workloads.

---

# Risk Areas Requiring Continuous Monitoring

The following areas should be reviewed regularly:

1. IAM credential hygiene
2. Public S3 exposure
3. SSH exposure
4. External IAM access
5. Unauthorized API activity
6. CloudTrail logging status
7. AWS Config compliance
8. Security configuration drift
9. Threat-detection coverage

---

# Recommended Security Metrics

## Identity and Access

- Number of unused IAM access keys
- Number of old access keys
- Number of IAM policies containing wildcard permissions
- Number of external-access findings

## Configuration Security

- Number of AWS Config compliant resources
- Number of AWS Config non-compliant resources
- Number of critical configuration violations
- Percentage of monitored resources evaluated successfully

## Logging and Detection

- CloudTrail logging coverage
- Number of unauthorized API events
- Number of security alarm activations
- Number of attempts to modify or disable logging
- Mean Time to Detect (MTTD)
- Mean Time to Acknowledge (MTTA)
- Mean Time to Contain (MTTC)

## Storage Security

- Number of publicly accessible S3 buckets
- Number of unencrypted S3 buckets
- Number of buckets with overly broad policies

---

# Defense-in-Depth Recommendations

A practical AWS security architecture should combine:

```text
IAM
 |
 +-- IAM Access Analyzer
 |
 +-- Least Privilege
 |
Network Security
 |
 +-- VPC
 +-- Security Groups
 +-- Restricted Administrative Access
 |
Storage Security
 |
 +-- S3 Block Public Access
 +-- Encryption
 +-- Restrictive Bucket Policies
 |
Monitoring
 |
 +-- CloudTrail
 +-- CloudWatch
 +-- SNS
 |
Threat Detection
 |
 +-- GuardDuty when account access is available
