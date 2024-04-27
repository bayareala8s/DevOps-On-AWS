### Steps to create EKS clsuter with Terraform in default VPC

To create an Amazon EKS cluster with Terraform in the default VPC, you can use the EKS module from the Terraform AWS modules collection. Here are the steps:

1. Install Terraform: If you haven't installed Terraform yet, you can download it from the official website and follow the instructions for your operating system.

2. Configure AWS credentials: Make sure you have your AWS credentials configured. You can do this by setting the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables, or by using the AWS CLI aws configure command.

3. Create a new directory for your Terraform configuration and navigate into it:

```
mkdir my-eks-cluster && cd my-eks-cluster
```

4. Create a new file main.tf with the following content:

```
   provider "aws" {
  region = "us-west-2" # replace with your preferred AWS region
}

data "aws_vpc" "default" {
  default = true
}

data "aws_subnet_ids" "default" {
  vpc_id = data.aws_vpc.default.id
}

module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "17.1.0"

  cluster_name    = "my-eks-cluster"
  cluster_version = "1.21"
  subnets         = data.aws_subnet_ids.default.ids

  node_groups = {
    eks_nodes = {
      desired_capacity = 2
      max_capacity     = 2
      min_capacity     = 1

      instance_type = "t3.medium"
      key_name      = "my-key-name" # replace with your key name
    }
  }
}
```

5. Initialize Terraform:
```
terraform init
```

6. Apply the Terraform configuration:
```
terraform apply
```
This script will create an EKS cluster with a node group in the default VPC. The node group will have 2 t3.medium instances. Replace the placeholders (us-west-2, my-key-name) with your actual AWS region and key name.

Please note that this is a simplified example and might not cover all your needs. You might need to adjust it according to your requirements, for example, by adding more configurations for the EKS cluster, or by adding more resources like IAM roles, security groups, etc.
