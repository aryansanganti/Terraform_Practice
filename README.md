# Terraform Quick Reference

## What is Terraform?
Infrastructure as Code (IaC) tool for building, changing, and versioning infrastructure safely and efficiently.

## Core Concepts

### Providers
- Plugins that interact with APIs (AWS, Azure, GCP, etc.)
- Example: `provider "aws" { region = "us-east-1" }`

### Resources
- Infrastructure components (VMs, networks, storage)
- Syntax: `resource "type" "name" { ... }`

### Variables
```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

### Outputs
```hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

### Data Sources
- Query existing infrastructure
- Example: `data "aws_ami" "ubuntu" { ... }`

## Essential Commands

```bash
terraform init       # Initialize working directory
terraform plan       # Preview changes
terraform apply      # Apply changes
terraform destroy    # Destroy infrastructure
terraform fmt        # Format code
terraform validate   # Validate configuration
terraform show       # Inspect state
terraform output     # View outputs
```

## State Management

- **terraform.tfstate**: Tracks real-world resources
- **Remote Backend**: Store state in S3, Terraform Cloud, etc.
- **State Locking**: Prevents concurrent modifications

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

## Modules

Reusable Terraform configurations:
```hcl
module "vpc" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
}
```

## Best Practices

1. **Use version control** for .tf files
2. **Never commit** .tfstate or secrets
3. **Use remote state** for teams
4. **Pin provider versions** for consistency
5. **Use variables** for flexibility
6. **Modularize** complex infrastructure
7. **Use workspaces** for environments
8. **Enable state locking** in production

## Terraform Block Example

```hcl
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

## Resource Dependencies

- **Implicit**: Terraform detects references
- **Explicit**: `depends_on = [resource.name]`

## Provisioners (Use Sparingly)

```hcl
resource "aws_instance" "web" {
  # ...
  
  provisioner "remote-exec" {
    inline = ["sudo apt-get update"]
  }
}
```

## Workspaces

```bash
terraform workspace new dev
terraform workspace select prod
terraform workspace list
```

## Common Meta-Arguments

- `count`: Create multiple resources
- `for_each`: Iterate over maps/sets
- `depends_on`: Explicit dependencies
- `lifecycle`: Customize resource behavior

## Example: Simple EC2 Instance

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
  
  tags = {
    Name = "WebServer"
  }
}

variable "instance_type" {
  default = "t2.micro"
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

## Useful Links

- [Official Documentation](https://www.terraform.io/docs)
- [Registry](https://registry.terraform.io/)
- [AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
