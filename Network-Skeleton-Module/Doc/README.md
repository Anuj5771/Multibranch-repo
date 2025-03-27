# AWS Network Skeleton Terraform module

![image](https://github.com/user-attachments/assets/591b84df-da8b-4766-b0e4-1e665676992f)

## Version History

| **Version** | **Created on** | **Created by** | **Last updated by** | **Last edited on** | **Level** | **Reviewer** |
|-------------|----------------|----------------|---------------------|--------------------|-----------|--------------|
| **V2**      | 27-03-2025     | Anuj Yadav     | Anuj Yadav          |  28-03-2025        |    L0     |     Tarun    |
| **V1**      | 27-03-2025     | Anuj Yadav     | Anuj Yadav          |                    | Internal  | Siddharth    |


# Table of Contents

1. [AWS Network Skeleton Terraform Module](#aws-network-skeleton-terraform-module)
2. [Version History](#version-history)
3. [Requirements](#requirements)
4. [Resources Table](#resources-table)
5. [Module Configuration Table](#module-configuration-table)
6. [Example Usage](#example-usage)
7. [Tags](#tags)
8. [VPC Configuration Inputs](#vpc-configuration-inputs)
9. [AWS Resources Overview](#aws-resources-overview)
10. [Related Projects](#related-projects)
11. [Additional Requirements](#additional-requirements)
12. [Conclusion](#conclusion)
13. [References](#references)
14. [Authors](#authors)
15. [Contact](#contact)



This Terraform module provisions a basic network skeleton on AWS. The primary goal of the module is to create a **VPC** along with other optional resources such as **Internet Gateway (IGW)**, **NAT Gateway**, **Route 53 DNS Zone**, **Security Groups**, and **ALB**. You can choose which resources to create based on the configuration of the module.

## Requirements

- **Terraform Version**: 5.0
- **AWS Region**: `us-east-2` 
- **AWS Provider Version**: Ensure the AWS provider is properly set for your version of Terraform.


## Resources Table

| Name                        | Type                   | Description                                                      |
|-----------------------------|------------------------|------------------------------------------------------------------|
| `aws_vpc`                   | Resource               | Creates a Virtual Private Cloud (VPC) with specified CIDR block and DNS settings. |
| `aws_flow_log`              | Resource               | Creates Flow Logs for monitoring and logging network traffic for the VPC. |
| `aws_internet_gateway`      | Resource               | Creates and attaches an Internet Gateway to the VPC for outbound internet access. |
| `aws_route53_zone`          | Resource               | Creates a Route 53 Hosted Zone (private or public) for DNS management. |


# VPC Module Configuration

## Overview
This module sets up the essential AWS networking components, including VPC, subnets, route tables, and security groups.

## Key Parameters

| Name                                                    | Type   | Description |
|---------------------------------------------------------|--------|-------------|
| `provider "aws"`                                        | Block  | AWS provider and region (`us-east-2`). |
| `cidr_block`                                            | string | Main CIDR block for VPC (e.g., `"192.168.0.0/24"`). |
| `enable_dns_hostnames`                                  | bool   | Enable DNS hostnames in VPC (`true/false`). |
| `public_subnets_cidr`                                   | list   | CIDR blocks for public subnets. |
| `private_subnets_cidr`                                  | list   | CIDR blocks for private subnets. |
| `avaialability_zones`                                   | list   | Availability zones for VPC. |
| `enable_igw_publicRouteTable_PublicSubnets_resource`    | bool   | Enable Internet Gateway & public subnets (`true/false`). |
| `enable_nat_privateRouteTable_PrivateSubnets_resource`  | bool   | Enable NAT Gateway & private subnets (`true/false`). |
| `enable_public_web_security_group_resource`             | bool   | Create public security group (`true/false`). |
| `enable_pub_alb_resource`                               | bool   | Create Application Load Balancer (`true/false`). |
| `enable_aws_route53_zone_resource`                      | bool   | Create Route 53 hosted zone (`true/false`). |

## Usage
This module can be used in a Terraform configuration to provision AWS networking resources. Modify the parameters as needed to fit your infrastructure requirements.

## Example Usage
```
provider "aws" {
  region = "us-east-2"
}

module "network_skeleton" {
  source                                               = "../"
  name                                                 = "network-skeleton"
  cidr_block                                           = "192.168.0.0/24"
  enable_dns_hostnames                                 = true
  enable_vpc_logs                                      = false
  public_subnets_cidr                                  = ["192.168.1.0/24"]
  private_subnets_cidr                                 = ["192.168.2.0/24"]
  pvt_zone_name                                        = "abc.xyz.in"
  availability_zones                                   = ["us-east-2a", "us-east-2b"]
  logs_bucket                                          = "ns-alb-logs"
  logs_bucket_arn                                      = "arn:aws:s3:::ns-alb-logs"
  tags = {
    Name        = "network-skeleton"
    Environment = "prod"
  }
  public_web_sg_name                                   = "ns-web-sg"
  alb_certificate_arn                                  = "arn:aws:acm:us-east-2:123456789012:certificate/abcdefg"
  enable_igw_publicRouteTable_PublicSubnets_resource   = false
  enable_nat_privateRouteTable_PrivateSubnets_resource = false
  enable_public_web_security_group_resource            = false
  enable_pub_alb_resource                              = false
  enable_aws_route53_zone_resource                     = false
}
```

Note: All the last 5 enable resource variables are set to false in the module. It will only create the VPC.

For more details, you can refer to the example folder.

## Tags
Tags are assigned to resources using the name variable as the prefix. Additional tags can be assigned by using the tags variables, as defined in the module configuration.


# AWS Resources Overview

This document provides an overview of the important AWS resources.

| **Name**                   | **Description**                                       |
|----------------------------|-------------------------------------------------------|
| `vpc_id`                   | The ID of the VPC                                     |
| `pub_route_table_id`       | Public route table ID's                               |
| `pvt_route_table_id`       | Private Route table ID's                              |
| `pvt_hosted_zone_id`       | Private Hosted zone ID's                              |
| `pvt_subnet_ids`           | Private Subnet table ID's                             |
| `public_subnet_ids`        | Public Subnet table ID's                              |
| `web_sg_id`                | Public Security Group table ID's                      |
| `dns_name`                 | DNS of ALB                                            |
| `aws_lb_arn`               | ARN of ALB                                            |
| `alb_listener_arn`         | ARN of ALB listener                                   |
| `alb_listener1_arn`        | ARN of ALB listener-1                                 |
| `route53_name`             | Domain Name of Route 53 private hosted zone           |

## Related Projects

- `route-table`
- `subnet`
- `nat-gateway`
- `security-groups`
- `alb`

## Additional Requirements
Option to create NAT gateway for each AZ.

## **Conclusion**

This Terraform module simplifies setting up a scalable AWS network, including VPC, IGW, NAT Gateway, Route 53, Security Groups, and ALB. It offers customization, cost optimization, and high availability, making it ideal for building secure and efficient network architectures.


## References

| **Resource Name**              | **Description**                                                                 | **AWS Documentation Link**                                                |
|---------------------------------|---------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| `aws_vpc`                      | Creates a Virtual Private Cloud (VPC) with specified CIDR block and DNS settings. | [AWS VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-vpc.html) |
| `aws_subnet`                   | Creates a subnet within the VPC, either public or private, based on the configuration. | [AWS Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) |
| `aws_nat_gateway`              | Creates a NAT Gateway to allow instances in a private subnet to access the internet. | [AWS NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) |
| `aws_internet_gateway`         | Creates and attaches an Internet Gateway to the VPC for outbound internet access. | [AWS Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) |
| `aws_security_group`           | Creates a Security Group to define rules for network access (inbound and outbound). | [AWS Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) |
| `aws_route53_zone`             | Creates a Route 53 Hosted Zone for DNS management (can be public or private). | [AWS Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html) |


## Authors

### Contact  
| Name       | Email Address               | GitHub                                  |
|------------|----------------------------|-----------------------------------------|
| Anuj Yadav | [anuj.yadav@mygurukulam.co](mailto:anuj.yadav@mygurukulam.co)  | [anuj169](https://github.com/anuj169)  |
