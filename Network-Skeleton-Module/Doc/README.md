# AWS Network Skeleton Terraform module


This document outlines the version history and updates of the document created by Anuj Yadav.

## Version History

| **Version** | **Created on** | **Created by** | **Last updated by** | **Last edited on** | **Level** | **Reviewer** |
|-------------|----------------|----------------|---------------------|--------------------|-----------|--------------|
| **V2**      | 27-03-2025     | Anuj Yadav     | Anuj Yadav          |                    | L0        | Tarun        |
| **V1**      | 27-03-2025     | Anuj Yadav     | Anuj Yadav          |                    | Internal  | Siddharth    |


This Terraform module provisions a basic network skeleton on AWS. The primary goal of the module is to create a **VPC** along with other optional resources such as **Internet Gateway (IGW)**, **NAT Gateway**, **Route 53 DNS Zone**, **Security Groups**, and **ALB**. You can choose which resources to create based on the configuration of the module.

## Requirements

- **Terraform Version**: 5
- **AWS Region**: `us-east-2` (adjustable)
- **AWS Provider Version**: Ensure the AWS provider is properly set for your version of Terraform.

## Providers

```hcl
provider "aws" {
  region = "us-east-2"
}
```
## Resources Table

| Name                        | Type                   | Description                                                      |
|-----------------------------|------------------------|------------------------------------------------------------------|
| `aws_vpc`                   | Resource               | Creates a Virtual Private Cloud (VPC) with specified CIDR block and DNS settings. |
| `aws_flow_log`              | Resource               | Creates Flow Logs for monitoring and logging network traffic for the VPC. |
| `aws_internet_gateway`      | Resource               | Creates and attaches an Internet Gateway to the VPC for outbound internet access. |
| `aws_route53_zone`          | Resource               | Creates a Route 53 Hosted Zone (private or public) for DNS management. |



## Module Configuration Table

| Name                                                    | Type      | Description |
|---------------------------------------------------------|-----------|-------------|
| `provider "aws"`                                        | Block     | Specifies the AWS provider and the region (`ap-south-1` in this case). |
| `module "network_skeleton"`                             | Block     | The module that provisions the network resources. |
| `source`                                                | string    | Path to the module directory (relative or absolute). |
| `name`                                                  | string    | Name of the network skeleton, used for tagging and resource names. |
| `cidr_block`                                            | string    | The CIDR block for the VPC. Example: `"173.31.0.0/20"`. |
| `enable_dns_hostnames`                                  | bool      | Enable DNS hostnames for instances launched in the VPC (true/false). |
| `enable_vpc_logs`                                       | bool      | Flag to enable VPC Flow Logs (true/false). |
| `public_subnets_cidr`                                   | list      | List of CIDR blocks for public subnets. |
| `pvt_zone_name`                                         | string    | Name of the private hosted zone for Route 53. |
| `private_subnets_cidr`                                  | list      | List of CIDR blocks for private subnets. |
| `avaialability_zones`                                   | list      | List of availability zones for the VPC. |
| `logs_bucket`                                           | string    | S3 bucket name for storing logs (for ALB logs, for example). |
| `logs_bucket_arn`                                       | string    | ARN of the log bucket. |
| `tags`                                                  | string    | Tags to apply to the resources created. |
| `public_web_sg_name`                                    | string    | Name of the public web security group. |
| `alb_certificate_arn`                                   | string    | ARN of the ACM certificate to be used by the ALB. |
| `enable_igw_publicRouteTable_PublicSubnets_resource`    | bool      | Flag to create an Internet Gateway, public route table, and public subnets (true/false). |
| `enable_nat_privateRouteTable_PrivateSubnets_resource`  | bool      | Flag to create NAT Gateway, private route table, and private subnets (true/false). |
| `enable_public_web_security_group_resource`             | bool      | Flag to create a web security group (true/false). |
| `enable_pub_alb_resource`                               | bool      | Flag to create an Application Load Balancer (true/false). |
| `enable_aws_route53_zone_resource`                      | bool      | Flag to create a Route 53 hosted zone (true/false). |

## Example Usage

```hcl
provider "aws" {
  region = "us-east-2"
}

module "network_skeleton" {
  source                                               = "../"
  name                                                 = "network-skeleton"
  cidr_block                                           = "192.168.0.0/24	"
  enable_dns_hostnames                                 = true
  enable_vpc_logs                                      = false
  public_subnets_cidr                                  = ["pvt_subnet_cidr"]
  pvt_zone_name                                        = "abc.xyz.in"
  private_subnets_cidr                                 = ["pub_subnet_cidr"]
  avaialability_zones                                  = ["avaialability_zones"]
  logs_bucket                                          = "ns-alb-logs"
  logs_bucket_arn                                      = "logs_bucket_arn"
  tags                                                 = "Additional tags"
  public_web_sg_name                                   = "ns-web-sg"
  alb_certificate_arn                                  = "ACM certificate arn"
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

# VPC Configuration Inputs

This document describes the inputs used to configure the VPC, along with their descriptions, types, default values, and whether they are required.

## Inputs

| **Input Name**                                      | **Description**                                                                 | **Type**   | **Default**                  | **Required** |
|-----------------------------------------------------|---------------------------------------------------------------------------------|-----------|------------------------------|--------------|
| `name`                                              | The string name appended in tags.                                                | `string`  | `"opstree"`                  | Yes          |
| `cidr_block`                                        | The CIDR block for the VPC.                                                      | `string`  | `"10.0.0.0/24"`              | No           |
| `instance_tenancy`                                  | A tenancy option for instances launched into the VPC.                           | `string`  | `"default"`                  | No           |
| `enable_dns_support`                                | A DNS support for instances launched into the VPC.                              | `boolean` | `"true"`                     | No           |
| `enable_dns_hostnames`                              | A DNS hostname for instances launched into the VPC.                             | `boolean` | `"false"`                    | No           |
| `enable_classiclink`                                | A DNS ClassicLink for instances launched into the VPC.                          | `boolean` | `"false"`                    | No           |
| `enable_igw_publicRouteTable_PublicSubnets_resource`| Used to create an IGW, Public Route Table, and Public Subnets.                  | `boolean` | `"True"`                     | No           |
| `enable_nat_privateRouteTable_PrivateSubnets_resource`| Used to create NAT, Private Route Table, and Private Subnets.                   | `boolean` | `"True"`                     | No           |
| `enable_public_web_security_group_resource`        | Used to create a Web Security Group.                                             | `boolean` | `"True"`                     | No           |
| `enable_pub_alb_resource`                          | Used to create an Application Load Balancer (ALB).                              | `boolean` | `"True"`                     | No           |
| `enable_aws_route53_zone_resource`                 | Used to create a Route 53 Hosted Zone.                                           | `boolean` | `"True"`                     | No           |

## Description of Variables

1. **`name`**: This is the name that will be used in tags for the VPC.

2. **`cidr_block`**: The CIDR block defines the IP address range for the VPC. The default value is `192.168.0.0/24`, but it can be adjusted to fit your network configuration.

3. **`instance_tenancy`**: This determines the tenancy of the instances launched in the VPC. The default value is `"default"`.
  
4. **`enable_dns_support`**: Specifies whether DNS support is enabled for instances launched into the VPC. The default is `"true"`.

5. **`enable_dns_hostnames`**: Determines whether DNS hostnames are enabled for instances. Default is `"false"`.

6. **`enable_classiclink`**: This option controls whether ClassicLink is enabled for instances. Default is `"false"`.

7. **`enable_igw_publicRouteTable_PublicSubnets_resource`**: A Boolean that controls the creation of an Internet Gateway (IGW), Public Route Table, and Public Subnets. Default is `"True"`.

8. **`enable_nat_privateRouteTable_PrivateSubnets_resource`**: A Boolean for the creation of a NAT, Private Route Table, and Private Subnets. Default is `"True"`.

9. **`enable_public_web_security_group_resource`**: Used to create a Web Security Group. Default is `"True"`.

10. **`enable_pub_alb_resource`**: Used to create an Application Load Balancer (ALB). Default is `"True"`.

11. **`enable_aws_route53_zone_resource`**: Used to create a Route 53 Hosted Zone. Default is `"True"`.

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

## Authors

### Contact
| Name | Email Address | GitHub | URL |
|------|--------------|--------|-----|
| Anuj Yadav | anuj.yadav@mygurukulam.co | [anuj169](https://github.com/anuj169) | https://github.com/anuj169 |
