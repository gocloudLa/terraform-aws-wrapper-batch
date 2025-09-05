# Standard Platform - Terraform Module 🚀🚀
<p align="right"><a href="https://partners.amazonaws.com/partners/0018a00001hHve4AAC/GoCloud"><img src="https://img.shields.io/badge/AWS%20Partner-Advanced-orange?style=for-the-badge&logo=amazonaws&logoColor=white" alt="AWS Partner"/></a><a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge&logo=apache&logoColor=white" alt="LICENSE"/></a></p>

Welcome to the Standard Platform — a suite of reusable and production-ready Terraform modules purpose-built for AWS environments.
Each module encapsulates best practices, security configurations, and sensible defaults to simplify and standardize infrastructure provisioning across projects.

## 📦 Module: Terraform AWS Batch Module
<p align="right"><a href="https://github.com/gocloudLa/terraform-aws-wrapper-batch/releases/latest"><img src="https://img.shields.io/github/v/release/gocloudLa/terraform-aws-wrapper-batch.svg?style=for-the-badge" alt="Latest Release"/></a><a href=""><img src="https://img.shields.io/github/last-commit/gocloudLa/terraform-aws-wrapper-batch.svg?style=for-the-badge" alt="Last Commit"/></a><a href="https://registry.terraform.io/modules/gocloudLa/wrapper-batch/aws"><img src="https://img.shields.io/badge/Terraform-Registry-7B42BC?style=for-the-badge&logo=terraform&logoColor=white" alt="Terraform Registry"/></a></p>
The Terraform wrapper for AWS Batch defines the configuration of the compute environments and the priority queues for the jobs.

### ✨ Features

- 🖥️ [Management of compute environments](#management-of-compute-environments) - Supports multiple compute environments beyond Fargate and Fargate Spot

- 📋 [Job queue management](#job-queue-management) - Manage job queues with priority for optimal resource allocation



### 🔗 External Modules
| Name | Version |
|------|------:|
| <a href="https://github.com/terraform-aws-modules/terraform-aws-batch" target="_blank">terraform-aws-modules/batch/aws</a> | 3.0.3 |



## 🚀 Quick Start
```hcl
batch_parameters = {
    "00" = {
      # Amount of CPU to use for compute environments
      #max_vcpus = 4 # Default: 4
    }
  }
  batch_defaults = var.batch_defaults
```


## 🔧 Additional Features Usage

### Management of compute environments
It allows defining and managing multiple compute environments in case of requiring the use of provisioned infrastructure as an alternative to fargate and fargate_spot.


<details><summary>Configuration Code</summary>

```hcl
fargate = {
        name_prefix = "${local.common_name}-${each.key}-fargate"

        compute_resources = {
          type      = "FARGATE"
          max_vcpus = try(each.value.max_vcpus, 4)

          security_group_ids = [data.aws_security_group.default[each.key].id]
          subnets            = data.aws_subnets.this[each.key].ids
        }
      }

      fargate_spot = {
        name_prefix = "${local.common_name}-${each.key}-fargate_spot"

        compute_resources = {
          type      = "FARGATE_SPOT"
          max_vcpus = try(each.value.max_vcpus, 4)

          security_group_ids = [data.aws_security_group.default[each.key].id]
          subnets            = data.aws_subnets.this[each.key].ids
        }
      }
```


</details>


### Job queue management
Manage job queues with priority, optimizing resource allocation between high and low priority tasks.


<details><summary>Configuration Code</summary>

```hcl
low_priority = {
      name     = "${local.common_name}-${each.key}-LowPriorityFargate"
      state    = "ENABLED"
      priority = 1

      tags = {
        JobQueue = "Low priority job queue"
      }
    }

    high_priority = {
      name     = "${local.common_name}-${each.key}-HighPriorityFargate"
      state    = "ENABLED"
      priority = 99

      fair_share_policy = {
        compute_reservation = 1
        share_decay_seconds = 3600

        share_distribution = [{
          share_identifier = "A1*"
          weight_factor    = 0.1
          }, {
          share_identifier = "A2"
          weight_factor    = 0.2
        }]
      }

      tags = {
        JobQueue = "High priority job queue"
      }
    }
```


</details>




## 📑 Inputs
| Name                                     | Description                                                                         | Type     | Default | Required |
| ---------------------------------------- | ----------------------------------------------------------------------------------- | -------- | ------- | -------- |
| compute_environments                     | Map of compute environment definitions to create                                    | `any`    | `{}`    | no       |
| create                                   | Controls if resources should be created (affects nearly all resources)              | `bool`   | `true`  | no       |
| create_instance_iam_role                 | Determines whether an IAM role is created or to use an existing IAM role            | `bool`   | `true`  | no       |
| create_job_queues                        | Determines whether to create job queues                                             | `bool`   | `true`  | no       |
| create_service_iam_role                  | Determines whether an IAM role is created or to use an existing IAM role            | `bool`   | `true`  | no       |
| create_spot_fleet_iam_role               | Determines whether an IAM role is created or to use an existing IAM role            | `bool`   | `false` | no       |
| instance_iam_role_additional_policies    | Additional policies to be added to the IAM role                                     | `map`    | `{}`    | no       |
| instance_iam_role_description            | Cluster instance IAM role description                                               | `string` | `null`  | no       |
| instance_iam_role_name                   | Cluster instance IAM role name                                                      | `string` | `null`  | no       |
| instance_iam_role_path                   | Cluster instance IAM role path                                                      | `string` | `null`  | no       |
| instance_iam_role_permissions_boundary   | ARN of the policy that is used to set the permissions boundary for the IAM role     | `string` | `null`  | no       |
| instance_iam_role_tags                   | A map of additional tags to add to the IAM role created                             | `map`    | `{}`    | no       |
| instance_iam_role_use_name_prefix        | Determines whether the IAM role name (instance_iam_role_name) is used as a prefix   | `string` | `true`  | no       |
| job_queues                               | Map of job queue and scheduling policy definitions to create                        | `any`    | `{}`    | no       |
| service_iam_role_additional_policies     | Additional policies to be added to the IAM role                                     | `map`    | `{}`    | no       |
| service_iam_role_description             | Batch service IAM role description                                                  | `string` | `null`  | no       |
| service_iam_role_name                    | Batch service IAM role name                                                         | `string` | `null`  | no       |
| service_iam_role_path                    | Batch service IAM role path                                                         | `string` | `null`  | no       |
| service_iam_role_permissions_boundary    | ARN of the policy that is used to set the permissions boundary for the IAM role     | `string` | `null`  | no       |
| service_iam_role_tags                    | A map of additional tags to add to the IAM role created                             | `map`    | `{}`    | no       |
| service_iam_role_use_name_prefix         | Determines whether the IAM role name (service_iam_role_name) is used as a prefix    | `bool`   | `true`  | no       |
| spot_fleet_iam_role_additional_policies  | Additional policies to be added to the IAM role                                     | `list`   | `{}`    | no       |
| spot_fleet_iam_role_description          | Spot fleet IAM role description                                                     | `string` | `null`  | no       |
| spot_fleet_iam_role_name                 | Spot fleet IAM role name                                                            | `string` | `null`  | no       |
| spot_fleet_iam_role_path                 | Spot fleet IAM role path                                                            | `string` | `null`  | no       |
| spot_fleet_iam_role_permissions_boundary | ARN of the policy that is used to set the permissions boundary for the IAM role     | `string` | `null`  | no       |
| spot_fleet_iam_role_tags                 | A map of additional tags to add to the IAM role created                             | `map`    | `{}`    | no       |
| spot_fleet_iam_role_use_name_prefix      | Determines whether the IAM role name (spot_fleet_iam_role_name) is used as a prefix | `bool`   | `true`  | no       |
| tags                                     | A map of tags to assign to resources.                                               | `map`    | `{}`    | no       |








---

## 🤝 Contributing
We welcome contributions! Please see our contributing guidelines for more details.

## 🆘 Support
- 📧 **Email**: info@gocloud.la

## 🧑‍💻 About
We are focused on Cloud Engineering, DevOps, and Infrastructure as Code.
We specialize in helping companies design, implement, and operate secure and scalable cloud-native platforms.
- 🌎 [www.gocloud.la](https://www.gocloud.la)
- ☁️ AWS Advanced Partner (Terraform, DevOps, GenAI)
- 📫 Contact: info@gocloud.la

## 📄 License
This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details. 