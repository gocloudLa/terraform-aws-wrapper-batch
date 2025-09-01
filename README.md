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
| [terraform-aws-modules/batch/aws](https://github.com/terraform-aws-modules/batch-aws) | 3.0.3 |



## 🚀 Quick Start
```hcl
batch_parameters = {
    "00" = {
      # Cantidad de cpu a utilizar por los compute environments
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











---

## 🤝 Contributing
We welcome contributions! Please see our contributing guidelines for more details.

## 🆘 Support
- 📧 **Email**: info@gocloud.la
- 🐛 **Issues**: [GitHub Issues](https://github.com/gocloudLa/issues)

## 🧑‍💻 About
We are focused on Cloud Engineering, DevOps, and Infrastructure as Code.
We specialize in helping companies design, implement, and operate secure and scalable cloud-native platforms.
- 🌎 [www.gocloud.la](https://www.gocloud.la)
- ☁️ AWS Advanced Partner (Terraform, DevOps, GenAI)
- 📫 Contact: info@gocloud.la

## 📄 License
This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details. 