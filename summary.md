# ✅ Summary of AFT (Account Factory for Terraform) Workflow

## AFT Purpose

AFT helps automate the lifecycle of AWS accounts in an AWS Control Tower environment using Terraform.

It manages account creation, baseline configurations, and customizations in a repeatable and auditable way.

## AFT Management Account

A dedicated AWS account is used as the AFT Management Account (best practice).

The repository `learn-terraform-aws-control-tower-aft` is deployed in this account.

It sets up all the infrastructure needed to run the AFT pipeline (e.g., CodePipeline, CodeBuild, Lambda functions, S3, DynamoDB, etc.).

## Account Creation with AFT

The repository `learn-terraform-aft-account-requests` is where you define the accounts to be created using Terraform modules.

When an account is requested, AFT provisions it and moves it to the defined OU in AWS Organizations.

## Provisioning Customizations (First Boot Phase)

After the account is created, AFT uses the `learn-terraform-aft-account-provisioning-customizations` repo.

These are early-stage customizations, like:

- Attaching SCPs
- Moving to OUs (if needed)
- Creating service-linked roles
- Enabling required services like Config, CloudTrail

## Global Customizations

The `learn-terraform-aft-global-customizations` repo is used to apply standard infrastructure to all AFT-managed accounts.

These are things common across accounts, like:

- GuardDuty, AWS Config rules
- CloudWatch alarms
- IAM roles, tagging standards

## Account-Specific Customizations

The `learn-terraform-aft-account-customizations` repo is for custom logic that only applies to individual accounts.

You must create a folder per account, and the folder name must match the value of `account_customizations_name` used in the account request.

Example:

```hcl
account_customizations_name = "ApplicationFactoryAccountRequest"
```
→ Folder: `customizations/ApplicationFactoryAccountRequest/`

## Managing Existing Accounts

✅ Yes, AFT can manage existing AWS accounts.

You must still create a definition for them in the account-requests repo.

The account name, email, and OU must match what already exists.

AFT will not recreate the account — instead, it applies provisioning, global, and custom customizations like normal.