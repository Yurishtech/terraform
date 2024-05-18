# Basics

## Providers

Providers are plugins that Terraform uses to communicate with upstream APIs.

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}
```

#### Configuring Providers

```
provider "aws" {
  region  = "us-east-1"
}
```

#### Aliasing Providers

```
provider "aws" {
  region  = "us-east-1"
}

provider "aws" {
  alias  = "secondary"
  region = "us-west-2"
}
```

```
resource "aws_instance" "app" {
  ami           = "ami-007855ac798b5175e"
  instance_type = "t3.micro"
}
resource "aws_instance" "app2" {
  provider      = aws.secondary
  ami           = "ami-007855ac798b5175e"
  instance_type = "t3.micro"
}
```

## Resources

Resources represent the infrastructure that Terraform should create, read, update, or delete. Examples of resources include AWS EC2 instances, Azure virtual networks, GitHub repositories, PagerDuty schedules, etc.

#### Accessing Resource Attributes

```hcl
resource "aws_instance" "app" {
  ami           = "ami-007855ac798b5175e"
  instance_type = "t3.micro"
  key_name      = aws_key_pair.app.key_name
}

resource "aws_key_pair" "app" {
  key_name   = "app"
  public_key = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIETEma9o59PQm3venxMkocCM8mifE0hspFm5XsYeccw8"
}
```

#### Meta-Arguments

Resources support the following meta-arguments that can be used to change their behavior:

* `depends_on` - Define explicit dependencies.
* `count` - Create multiple resources using indexes.
* `for_each` - Create multiple resources using uniquely named keys.
* `provider` - Use a specific aliased provider.
* `lifecycle` - Customize the resource lifecycle.
* `provisioner` - Provision a given resource after creation or destruction.

## Data Sources

Data sources are used to fetch and reference information from existing infrastructure or configuration management tools. They allow you to query and retrieve information that can be used in your Terraform configurations. This can be useful for using details about infrastructure components that are not managed by your current Terraform configuration or for fetching dynamic information.

#### Key Concepts of Data Sources

* **Read-Only**: Data sources are read-only. They allow you to access and use information but do not create or modify resources.
* **Dynamic Information**: Data sources can fetch up-to-date information, which can be used to make decisions or configurations based on current infrastructure states or external data.
* **Integration**: Data sources can integrate with various services and APIs to pull in the necessary information.



```hcl
data "azurerm_virtual_network" "main" {
  name                = var.virtual_network_name
  resource_group_name = data.azurerm_resource_group.main.name
}
```

1. **Data Source: `azurerm_virtual_network.main`**:
   * **Purpose**: Fetches information about an existing Azure virtual network.
   * **Attributes**:
     * `name`: The name of the virtual network to fetch, provided by a variable (`var.virtual_network_name`).
     * `resource_group_name`: The name of the resource group containing the virtual network, fetched from the previously defined data source (`data.azurerm_resource_group.main.name`).

## Variables

## Outputs

## Locals

locals are named values that you can use to simplify your configuration and avoid repeating the same values or expressions multiple times. They are defined using the `locals` block and can be referenced throughout your configuration using the `local` namespace.



```hcl
locals {
  name        = "example-name"
  environment = "dev"
  cidr_block  = "10.0.0.0/16"
}

resource "azurerm_virtual_network" "example" {
  name                = "${local.name}-vnet"
  address_space       = [local.cidr_block]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

```

#### Comparison

| Aspect         | Variables                                            | Locals                                |
| -------------- | ---------------------------------------------------- | ------------------------------------- |
| **Definition** | `variable` block                                     | `locals` block                        |
| **Purpose**    | Input dynamic values into the configuration          | Simplify and reuse expressions/values |
| **Scope**      | Global within the module and can be passed           | Local to the module only              |
| **Usage**      | Parameterize the Terraform configuration             | Simplify repetitive or complex values |
| **Set By**     | Command-line, environment, `.tfvars` files, defaults | Defined within the module itself      |
| **Override**   | Can be overridden by user input                      | Cannot be overridden from outside     |

#### Use Cases

**Variables**:

* Use variables when you need to pass different values to your Terraform configurations depending on the environment, deployment, or other conditions.
* Examples: Region, instance type, VPC ID, etc.

**Locals**:

* Use locals to simplify your code by defining reusable values or expressions that are used multiple times within a module.
* Examples: Calculated values, frequently used strings or lists, concatenated strings, etc.
