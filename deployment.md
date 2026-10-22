# Deployment Guide

## 1. Clone the repository

```bash
git clone https://github.com/Ydranai/aws-security-operations-center.git
cd aws-security-operations-center
```

## 2. Select the AWS account and Region

Confirm the active AWS identity:

```bash
aws sts get-caller-identity
```

Confirm the configured Region:

```bash
aws configure get region
```

The examples below use `us-east-1`. Change it when required.

## 3. Prerequisites

Verify that AWS Security Hub is enabled in the deployment Region. Enable Amazon GuardDuty when GuardDuty threat monitoring is required.

If you want alert notifications, create or select an Amazon SNS topic and provide its ARN through the CloudFormation parameter.

## 4. Validate the CloudFormation template

```bash
aws cloudformation validate-template \
  --template-body file://cloudformation/aws-security-operations-center.yaml
```

## 5. Deploy

```bash
aws cloudformation deploy \
  --template-file cloudformation/aws-security-operations-center.yaml \
  --stack-name aws-security-operations-center \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-1
```

When notification configuration or other parameters are required, add them with `--parameter-overrides`.

Example:

```bash
aws cloudformation deploy \
  --template-file cloudformation/aws-security-operations-center.yaml \
  --stack-name aws-security-operations-center \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-1 \
  --parameter-overrides \
    AlertTopicArn=arn:aws:sns:us-east-1:123456789012:security-alerts
```

## 6. Verify the deployment

Check stack status:

```bash
aws cloudformation describe-stacks \
  --stack-name aws-security-operations-center \
  --region us-east-1 \
  --query 'Stacks[0].StackStatus' \
  --output text
```

Open Amazon CloudWatch → Dashboards and locate the dashboard created by the stack.

Allow the scheduled collector to execute before evaluating newly created custom metrics.

## 7. Operational validation

Validate:

- Security Hub severity counters
- GuardDuty active-threat counter
- Failed controls
- IAM credential-age indicators
- IAM MFA indicators
- Prioritized findings table
- Lambda execution logs
- SNS delivery, if configured

## 8. Production considerations

Before production use, review:

- IAM least privilege
- CloudWatch Logs retention
- metric and API costs
- collection schedules
- finding-deduplication windows
- Access Key warning/critical thresholds
- SNS subscriptions and notification routing
- Security Hub standards enabled in the account

## 9. Remove the stack

```bash
aws cloudformation delete-stack \
  --stack-name aws-security-operations-center \
  --region us-east-1
```

Confirm deletion before assuming all resources have been removed.
