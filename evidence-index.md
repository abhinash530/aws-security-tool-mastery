# Security Tool Mastery — Evidence Index

This document maps the practical AWS security activities completed during the Security Tool Mastery Project to the evidence collected during the assessment.

| Evidence ID | Security Tool / Area | Activity | Result |
|---|---|---|---|
| Evidence 4.1 | IAM Access Analyzer | External access analysis | Analyzer active; 0 findings |
| Evidence 4.2 | AWS Config | Security rule configuration | 4 AWS managed rules configured |
| Evidence 4.3 | AWS Config | Compliance assessment | 4 configured rules compliant |
| Evidence 4.4 | AWS CloudTrail | Audit logging configuration | Multi-region logging active |
| Evidence 4.5 | Amazon CloudWatch | Unauthorized-access detection | Security alarm configured |
| Evidence 4.6 | Amazon SNS | Security notification | Notification workflow configured |
| Evidence 4.7 | Amazon GuardDuty | Service availability assessment | Blocked by AWS account service-access limitation |
| Evidence 4.8 | Amazon S3 | Storage security assessment | Public access protection and encryption configured |
| Evidence 4.9 | Amazon EC2 / VPC | Network security assessment | SSH restricted to specific source |

## Evidence 4.1 — IAM Access Analyzer

The IAM Access Analyzer `CloudSecurityLab-AccessAnalyzer` was configured for external-access analysis.

Observed result:

- Status: Active
- Findings: 0

This indicates that no unintended external-access findings were identified within the analyzed scope at the time of assessment.

## Evidence 4.2 — AWS Config Security Rules

Four AWS managed security rules were configured:

1. `s3-bucket-public-read-prohibited`
2. `s3-bucket-server-side-encryption-enabled`
3. `root-account-mfa-enabled`
4. `restricted-ssh`

The rules were selected for evaluating common cloud security configuration risks.

## Evidence 4.3 — AWS Config Compliance Results

The four configured AWS Config rules returned compliant results during the assessment.

The results demonstrate that the laboratory resources satisfied the selected security checks at the time of evaluation.

## Evidence 4.4 — AWS CloudTrail

`CloudSecurityLab-Trail` was configured with:

- Multi-region logging
- Active logging
- S3 log storage
- CloudWatch Logs integration

CloudTrail provides an audit trail that can support security investigation and incident response.

## Evidence 4.5 — Amazon CloudWatch

`CloudSecurityLab-UnauthorizedAccess-Alarm` was configured to detect matching unauthorized-access events.

Detection terms included:

- `AccessDenied`
- `UnauthorizedOperation`

The alarm used a threshold greater than zero matching events.

## Evidence 4.6 — Amazon SNS

SNS was configured as the notification mechanism associated with the CloudWatch security alarm.

The subscription was observed as pending confirmation during the configuration process.

## Evidence 4.7 — Amazon GuardDuty

GuardDuty was investigated but could not be enabled because the AWS account redirected to an account setup/service-access limitation page.

The project therefore does not claim successful GuardDuty deployment.

## Evidence 4.8 — Amazon S3

The laboratory S3 bucket was reviewed for storage-security controls.

Observed controls included:

- Block Public Access enabled
- ACLs disabled
- Server-side encryption enabled
- S3 Bucket Key enabled in the observed configuration

## Evidence 4.9 — Amazon EC2 / VPC

The EC2 and network configuration was reviewed.

Security controls included:

- Restricted SSH source
- Security Group-based network filtering
- IMDSv2 required for the EC2 instance
- Dedicated laboratory network resources

## Security and Privacy Note

Evidence uploaded to this public repository must never contain:

- AWS secret access keys
- Private keys
- Passwords
- MFA codes
- Session tokens
- Other authentication secrets

Screenshots should be reviewed before publication to ensure sensitive information is not exposed.
