# AWS Security Operations Center

**Security posture · Threat detection · Identity**

A serverless AWS security operations dashboard that centralizes security posture, threat detection, and identity-risk signals using AWS Security Hub, Amazon GuardDuty, AWS IAM, AWS Lambda, Amazon CloudWatch, Amazon EventBridge, Amazon DynamoDB, and Amazon SNS.

![AWS Security Operations Center architecture](architecture/aws-security-operations-center-architecture.png)

## What it provides

- Active Security Hub findings grouped by severity: **CRITICAL, HIGH, MEDIUM, LOW**
- Active Amazon GuardDuty threat indicator
- Failed security-control visibility
- IAM Access Key age and unused-credential monitoring
- IAM users without MFA and root-account security indicators
- Prioritized operational findings with affected resource and remediation context
- Custom CloudWatch metrics and an operational dashboard
- Event-driven and scheduled collection
- Alert deduplication with DynamoDB
- Optional critical notifications through Amazon SNS

## Architecture

The main flow is:

`Security Hub + GuardDuty + IAM → Lambda → CloudWatch / DynamoDB / SNS → Security Operations Dashboard`

Amazon EventBridge invokes the collector on a schedule and can also deliver security events. The Lambda collector reads security findings and IAM posture, publishes custom metrics and operational logs to CloudWatch, and uses DynamoDB to reduce duplicate notifications.

## Security model

The project is designed primarily as an **observability and detection layer**. It does not automatically rotate or delete credentials, modify findings, or remediate AWS resources. Automated remediation should be implemented separately with narrowly scoped IAM permissions.

## Repository structure

```text
aws-security-operations-center/
├── README.md
├── LICENSE
├── cloudformation/
│   └── aws-security-operations-center.yaml
├── architecture/
│   └── aws-security-operations-center-architecture.png
└── docs/
    └── deployment.md
```

## Prerequisites

Before deployment:

1. AWS Security Hub must be enabled in the target Region.
2. Amazon GuardDuty should be enabled if GuardDuty threat visibility is required.
3. The deployment identity must be allowed to create CloudFormation, Lambda, IAM, CloudWatch, EventBridge, DynamoDB and related resources.
4. Configure an SNS topic ARN if notifications are required.
5. Review all parameters and IAM permissions before production deployment.

## Deploy

Validate the template:

```bash
aws cloudformation validate-template \
  --template-body file://cloudformation/aws-security-operations-center.yaml
```

Deploy:

```bash
aws cloudformation deploy \
  --template-file cloudformation/aws-security-operations-center.yaml \
  --stack-name aws-security-operations-center \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-1
```

See [docs/deployment.md](docs/deployment.md) for the detailed procedure and parameters.

## Important

This repository is a reference implementation. Review thresholds, schedules, IAM policies, notification settings, retention periods, Security Hub standards, and operational requirements before using it in production.

## Author

**Ydranai**

Built as an AWS security operations reference project for the AWS Builder community.

## License

MIT License. See [LICENSE](LICENSE).
