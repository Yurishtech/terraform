# Modules

In Terraform, modules are a way to encapsulate and reuse configurations. Modules enable you to organize your infrastructure as code, share and reuse configurations across projects, and maintain consistency and best practices. They help manage complexity by grouping related resources together and promoting a modular architecture.

#### Key Concepts of Modules

1. **Root Module**: The main directory where Terraform commands are run. This module typically contains the `main.tf`, `variables.tf`, `outputs.tf`, and `terraform.tfstate` files.
2. **Child Module**: A module called by another configuration. It resides in its own directory and can be sourced locally or remotely.
3. **Inputs and Outputs**: Modules can accept input variables and return output values, allowing them to be flexible and reusable.

### Structure of a Module

A module typically includes the following files:

* **`main.tf`**: Contains the primary resources and configurations.
* **`variables.tf`**: Defines input variables for the module.
* **`outputs.tf`**: Defines output values that the module returns.

**Example: Creating a Simple VPC Module**

**Directory Structure**:

```css
modules/
  └── vpc/
      ├── main.tf
      ├── variables.tf
      └── outputs.tf
```

**`modules/vpc/main.tf`**:

```hcl
resource "aws_vpc" "this" {
  cidr_block = var.cidr_block

  tags = {
    Name = var.name
  }
}

resource "aws_subnet" "this" {
  count             = length(var.subnets)
  vpc_id            = aws_vpc.this.id
  cidr_block        = element(var.subnets, count.index)
  availability_zone = element(var.availability_zones, count.index)

  tags = {
    Name = "${var.name}-subnet-${count.index}"
  }
}

```

**`modules/vpc/variables.tf`**:

```
variable "name" {
  description = "The name of the VPC"
  type        = string
}

variable "cidr_block" {
  description = "The CIDR block for the VPC"
  type        = string
}

variable "subnets" {
  description = "A list of subnet CIDR blocks"
  type        = list(string)
}

variable "availability_zones" {
  description = "A list of availability zones"
  type        = list(string)
}

```

**`modules/vpc/outputs.tf`**:

```hcl
output "vpc_id" {
  description = "The ID of the VPC"
  value       = aws_vpc.this.id
}

output "subnet_ids" {
  description = "The IDs of the subnets"
  value       = aws_subnet.this.*.id
}

```



### **Using the Module**

**`main.tf`**:

```hcl
provider "aws" {
  region = "us-west-2"
}

module "vpc" {
  source              = "./modules/vpc"
  name                = "example-vpc"
  cidr_block          = "10.0.0.0/16"
  subnets             = ["10.0.1.0/24", "10.0.2.0/24"]
  availability_zones  = ["us-west-2a", "us-west-2b"]
}

output "vpc_id" {
  value = module.vpc.vpc_id
}

output "subnet_ids" {
  value = module.vpc.subnet_ids
}

```

#### Benefits of Using Modules

1. **Reusability**: Modules allow you to reuse code across multiple projects or environments, reducing duplication.
2. **Encapsulation**: Modules encapsulate implementation details, exposing only necessary inputs and outputs.
3. **Organization**: They help in organizing your Terraform configurations into logical units, making the codebase easier to manage and understand.
4. **Collaboration**: Shared modules promote collaboration within teams, as best practices and standard configurations can be centralized and reused.
5. **Versioning**: Modules can be versioned, enabling you to manage changes and ensure stability across deployments.

### Sourcing Modules

Modules can be sourced from different locations:

*   **Local Path**: Refer to a module stored locally in your repository.

    ```hcl
    module "vpc" {
      source = "./modules/vpc"
      # other inputs
    }
    ```
*   **Terraform Registry**: Use modules from the Terraform Registry.

    ```hcl
    module "vpc" {
      source  = "terraform-aws-modules/vpc/aws"
      version = "2.78.0"
      # other inputs
    }
    ```
*   **Git Repository**: Use modules stored in a Git repository.

    ```hcl
    module "vpc" {
      source = "git::https://github.com/username/repo.git//path/to/module"
      # other inputs
    }
    ```

### Providers in Modules

Declare the providers that your module uses using the `required_providers` syntax. This will prevent callers from using your module with incompatible provider versions.

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

#### Provider Aliasing

If your module requires aliased providers, they must be declared using the `configuration_aliases` attribute. This is useful when writing modules that require multiple provider configurations.

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
      configuration_aliases = [aws.secondary]
    }
  }
}
```

#### Explicitly Passing Providers to Modules

```hcl
provider "aws" {
  alias  = "us_west"
  region = "us-west-2"
}

provider "aws" {
  alias  = "us_east"
  region = "us-east-1"
}

module "vpc_us_west" {
  source    = "./modules/vpc"
  providers = {
    aws = aws.us_west
  }
  name      = "example-vpc-us-west"
  # other inputs
}

module "vpc_us_east" {
  source    = "./modules/vpc"
  providers = {
    aws = aws.us_east
  }
  name      = "example-vpc-us-east"
  # other inputs
}

```

#### Orphaning Providers
