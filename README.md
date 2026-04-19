# Amazon Mainframe Modernization (amazon-mainframe-modernization)
AWS Mainframe Modernization provides tools and resources to help you plan and implement migration and modernization of your mainframe applications to AWS. It supports automated refactoring and replatforming of COBOL applications to run natively on AWS with managed runtime environments, deployment pipelines, and batch job execution capabilities.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/amazon-mainframe-modernization/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - AWS, COBOL, Mainframe, Migration, Modernization, Batch Processing

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-04-19

## APIs

### AWS Mainframe Modernization API
The AWS Mainframe Modernization API provides programmatic access to create and manage applications, environments, deployments, and batch job executions for mainframe application modernization on AWS. Covers 25 paths and 33 operations for the full modernization lifecycle.

**Human URL:** [https://aws.amazon.com/mainframe-modernization/](https://aws.amazon.com/mainframe-modernization/)

#### Tags:

 - Mainframe, Migration, Modernization, COBOL, Batch Processing

#### Properties

- [Documentation](https://docs.aws.amazon.com/m2/latest/APIReference/Welcome.html)
- [OpenAPI](openapi/amazon-mainframe-modernization-openapi-original.yaml)
- [GettingStarted](https://aws.amazon.com/mainframe-modernization/getting-started/)
- [Pricing](https://aws.amazon.com/mainframe-modernization/pricing/)
- [FAQ](https://aws.amazon.com/mainframe-modernization/faqs/)

## Common Properties

- [Portal](https://aws.amazon.com/mainframe-modernization/)
- [Documentation](https://docs.aws.amazon.com/m2/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [Blog](https://aws.amazon.com/blogs/modernizing-with-aws/)
- [GitHubOrganization](https://github.com/aws)
- [Console](https://console.aws.amazon.com/m2/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [Login](https://signin.aws.amazon.com/)
- [StatusPage](https://health.aws.amazon.com/health/status)
- [Contact](https://aws.amazon.com/contact-us/)
- [SpectralRules](rules/amazon-mainframe-modernization-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-mainframe-modernization-vocabulary.yaml)
- [NaftikoCapability](capabilities/modernization-workflow.yaml)

## Features

| Name | Description |
|------|-------------|
| Automated Refactoring | Automatically refactor COBOL mainframe applications to run natively on AWS. |
| Managed Runtime Environments | Create and manage runtime environments using Micro Focus or Blue Age engines on AWS. |
| Application Deployment | Deploy and manage versions of modernized mainframe applications. |
| Batch Job Execution | Execute batch jobs migrated from mainframe in managed AWS environments. |
| Data Set Management | Import and manage mainframe data sets for use by modernized applications. |

## Use Cases

| Name | Description |
|------|-------------|
| COBOL Modernization | Refactor legacy COBOL applications to Java or other modern languages running on AWS. |
| Mainframe Replatforming | Replatform mainframe workloads to AWS-managed environments without code changes. |
| Batch Job Migration | Migrate batch processing workloads from mainframe to AWS for cost savings and scalability. |
| Mainframe Retirement | Decommission on-premises mainframe hardware by migrating all workloads to AWS. |

## Integrations

| Name | Description |
|------|-------------|
| Micro Focus Runtime | Use Micro Focus Enterprise Server as the runtime engine for replatformed applications. |
| Blue Age Runtime | Use Blue Age as the runtime engine for refactored applications. |
| Amazon EFS | Store application artifacts and data sets in Elastic File System. |
| AWS CloudWatch | Monitor application and batch job metrics through CloudWatch. |
| AWS VPC | Deploy environments within a Virtual Private Cloud for network isolation. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Amazon Mainframe Modernization OpenAPI](openapi/amazon-mainframe-modernization-openapi-original.yaml)

### JSON Schema

168 schema files available in the [json-schema/](json-schema/) directory.

### JSON Structure

168 structure files available in the [json-structure/](json-structure/) directory.

### JSON-LD

- [Amazon Mainframe Modernization Context](json-ld/amazon-mainframe-modernization-context.jsonld)

### Examples

168 example files available in the [examples/](examples/) directory.

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Amazon Mainframe Modernization](capabilities/shared/mainframe-modernization.yaml) — 7 operations for application management, environments, and batch jobs

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Modernization Workflow](capabilities/modernization-workflow.yaml) | Amazon Mainframe Modernization | 7 | Platform Engineer, Mainframe Developer |

## Vocabulary

- [Amazon Mainframe Modernization Vocabulary](vocabulary/amazon-mainframe-modernization-vocabulary.yaml) — Unified taxonomy mapping 7 resources, 8 actions, 1 workflow, and 2 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon Mainframe Modernization Spectral Rules](rules/amazon-mainframe-modernization-spectral-rules.yml) — 18 rules across 7 categories enforcing AWS Mainframe Modernization API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
