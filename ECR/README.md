### ECR
- To list all repositories in your AWS ECR (Elastic Container Registry) using the AWS CLI, you can use the following command:

aws ecr describe-repositories

- To create an AWS ECR (Elastic Container Registry) repository using the AWS CLI, you can use the create-repository command. Here's the syntax:

  aws ecr create-repository --repository-name my-ecr-repo --region us-west-2

- Push local docker image to AWS ECR repository

  To push a local Docker image to an AWS ECR (Elastic Container Registry) repository, you need to perform the following steps:

1. Tag the local Docker image with the URI of your ECR repository.
2. Authenticate Docker with your AWS ECR registry.
3. Push the tagged Docker image to your ECR repository.

Here's how you can do it using the AWS CLI:

1. Tag the Local Docker Image:

   ```bash
   docker tag local-image-name:tag aws-account-id.dkr.ecr.region.amazonaws.com/repository-name:tag
   ```

   Replace:
   - `local-image-name:tag` with the name and tag of your local Docker image.
   - `aws-account-id` with your AWS account ID.
   - `region` with the AWS region where your ECR repository is located.
   - `repository-name` with the name of your ECR repository.
   - `tag` with the desired tag for the image in the ECR repository.

2. Authenticate Docker with AWS ECR:

   Run the following command to authenticate Docker with your AWS account:

   ```bash
   aws ecr get-login-password --region region | docker login --username AWS --password-stdin aws-account-id.dkr.ecr.region.amazonaws.com
   ```

   Replace `region` with the AWS region where your ECR repository is located, and `aws-account-id` with your AWS account ID.

3. Push the Tagged Docker Image to ECR:

   ```bash
   docker push aws-account-id.dkr.ecr.region.amazonaws.com/repository-name:tag
   ```

   Replace `aws-account-id`, `region`, `repository-name`, and `tag` with the corresponding values used in the previous steps.

After running these commands, your local Docker image will be pushed to your AWS ECR repository. You can verify the image in the AWS Management Console or by using the AWS CLI.

  
