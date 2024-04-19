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

- AWS ECR list images within a Repository

  To list the images within an AWS ECR (Elastic Container Registry) repository using the AWS CLI, you can use the `describe-images` command. Here's the syntax:

```bash
aws ecr describe-images --repository-name <repository-name>
```

Replace `<repository-name>` with the name of your ECR repository.

If you want to list images in a specific region, you can specify the `--region` option:

```bash
aws ecr describe-images --repository-name <repository-name> --region <region>
```

Replace `<region>` with the AWS region where your ECR repository is located.

Here's an example of listing images within a repository named `my-ecr-repo`:

```bash
aws ecr describe-images --repository-name my-ecr-repo
```

This command will return JSON output containing information about the images in the specified repository, including their tags and other details.

You can also use filters to narrow down the results. For example, to list images with a specific tag, you can use the `--query` option:

```bash
aws ecr describe-images --repository-name my-ecr-repo --query 'imageDetails[*].{ImageTags: imageTags}'
```

This command will return only the image tags for each image in the repository. Adjust the query as needed to retrieve specific information about the images.

### Script to deploy to ECR

```bash
#!/bin/bash

#./deploy-to-ecr.sh us-west-2 277374794397 messages-app-image

# This script deploys a Docker image to an Amazon Elastic Container Registry (ECR) repository.

# Replace these variables with your own values
AWS_REGION=$1
AWS_ACCOUNT_ID=$2
DOCKER_IMAGE_TAG=$3

# Get all tags for the specified image name
tags=$(docker images --format "{{.Repository}}:{{.Tag}}" | grep "^$3:" | awk -F':' '{print $2}')

# Check if tags are available
if [ -z "$tags" ]; then
    echo "No tags found for image: $3"
    exit 1
fi

# Print the list of tags
echo "Tags for image $3:"
echo "$tags"

# Authenticate Docker client with ECR
$(aws ecr get-login --no-include-email --region $AWS_REGION)

# Create an ECR repository
ECR_REPO_NAME="messages-app-image"
aws ecr create-repository --repository-name $ECR_REPO_NAME --region us-west-2

# Tag the Docker image
docker tag $DOCKER_IMAGE_TAG:$tags $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$DOCKER_IMAGE_TAG:$tags

sleep 5

# Push the Docker image to ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$DOCKER_IMAGE_TAG:$tags

echo "Docker image $DOCKER_IMAGE_TAG:$tags deployed to ECR repository $ECR_REPO_NAME"
```

  
