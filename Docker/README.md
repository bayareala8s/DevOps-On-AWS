### Create a Docker Image for Basic NestJS Application

To create a Docker image for a basic NestJS application, you can follow these steps:

1. Create a Dockerfile in the root directory of your NestJS project. The Dockerfile should contain the following instructions:

FROM node:16

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["npm", "run", "start:dev"]

2. Build the Docker image using the following command:

docker build -t messages-app:1.0.0-dev-commit-SHA .

3. Run the Docker image using the following command:

docker run -p 3000:3000 messages-app

### Bash script that will create a Docker image with the following naming convention:

- <image-name>:<major>.<minor>.<patch>-<environment>-<timestamp>-<commit-SHA>

```bash
#!/bin/bash

# Get the container name from the first argument.
CONTAINER_NAME=$1

# Get the image name from the first argument.
IMAGE_NAME=$2

# Get the major, minor, and patch version numbers from the second argument.
VERSION=$3

# Get the environment from the third argument.
ENVIRONMENT=$4

# Get the commit SHA from the fourth argument.
COMMIT_SHA=$5

# Get the current timestamp.
TIMESTAMP=$(date +%Y-%m-%d-%H-%M)

echo "Container name: $CONTAINER_NAME"
echo "Image name: $IMAGE_NAME"
echo "Version: $VERSION"
echo "Environment: $ENVIRONMENT"
echo "Commit SHA: $COMMIT_SHA"
echo "Timestamp: $TIMESTAMP"

# Check if the container exists
if docker ps -a --format '{{.Names}}' | grep -Eq "^$CONTAINER_NAME$"; then
    # Container exists, so remove it
    docker rm -f "$CONTAINER_NAME"
    echo "Container $CONTAINER_NAME removed."
else
    echo "Container $CONTAINER_NAME does not exist."
fi

# Define the image name pattern
IMAGE_PATTERN="$IMAGE_NAME:*"
echo "Image pattern: $IMAGE_PATTERN"

# Check if any images match the pattern
if docker image inspect $(docker images -q --filter=reference="$IMAGE_PATTERN") &> /dev/null; then
    # Images exist, so remove them
    docker rmi -f $(docker images -q --filter=reference="$IMAGE_PATTERN")
    echo "Images matching pattern $IMAGE_PATTERN removed."
else
    echo "No images matching pattern $IMAGE_PATTERN exist."
fi

# Build the Docker image.
docker build -t ${IMAGE_NAME}:${VERSION}-${ENVIRONMENT}-${TIMESTAMP}-${COMMIT_SHA} .

# Run the Docker image.
docker run -d --name ${CONTAINER_NAME} -p 3000:3000 ${IMAGE_NAME}:${VERSION}-${ENVIRONMENT}-${TIMESTAMP}-${COMMIT_SHA}

# Wait for the container to start. sleep for 5 seconds
sleep 5

# Define the URL of the NestJS API endpoint to test
API_URL="http://localhost:3000/messages"

# Make a GET request to the API endpoint
response=$(curl -s -o /dev/null -w "%{http_code}" "$API_URL")

# Check the HTTP response code
if [ "$response" -eq 200 ]; then
    echo "API $API_URL endpoint is reachable."
else
    echo "Unable to reach API endpoint. HTTP response code: $response"
fi

# Define your Docker Hub username and password
USERNAME="bayareala8s"
TOKEN="__________"

# Login to the Docker registry
docker login --username=$USERNAME --password=$TOKEN


# Tag the Docker image with the repository name
docker tag ${IMAGE_NAME}:${VERSION}-${ENVIRONMENT}-${TIMESTAMP}-${COMMIT_SHA} $USERNAME/${IMAGE_NAME}:${VERSION}-${ENVIRONMENT}-${TIMESTAMP}-${COMMIT_SHA}


sleep 5

# Push the Docker image to the registry.
docker push $USERNAME/${IMAGE_NAME}:${VERSION}-${ENVIRONMENT}-${TIMESTAMP}-${COMMIT_SHA}

```


### List of all important Docker commands with examples:

- docker build - Builds a Docker image from a Dockerfile.

  docker build -t my-image .

- docker run - Runs a Docker image.

  docker run --name my-container my-image

- docker image ls - Lists all Docker images on the local machine.

  docker image ls

- docker image rm - Removes a Docker image from the local machine.

  docker image rm my-image


- docker container ls - Lists all Docker containers on the local machine.

  docker container ls

- docker container rm - Removes a Docker container from the local machine.

  docker container rm my-container

- docker ps - Lists all running Docker containers on the local machine.

  docker ps

- docker stop - Stops a running Docker container.

  docker stop my-container

- docker start - Starts a stopped Docker container.

  docker start my-container

- docker exec - Executes a command inside a running Docker container.

  docker exec my-container ls -la

- docker attach - Attaches to a running Docker container.

  docker attach my-container

- docker commit - Creates a new Docker image from a running Docker container.

  docker commit my-container my-new-image

- docker push - Pushes a Docker image to a remote Docker registry.

  docker push my-image my-registry.example.com/my-image

- docker pull - Pulls a Docker image from a remote Docker registry.

  docker pull my-registry.example.com/my-image


### create an AWS Fargate Cluster, run ECR docker image and test the application

Below is a Bash script that creates an AWS Fargate cluster, runs a Docker image from Amazon ECR, and tests the application:

```bash
#!/bin/bash

# Set variables
CLUSTER_NAME="my-cluster"
TASK_DEFINITION_FILE="task-definition.json"
IMAGE_URI="<aws-account-id>.dkr.ecr.<region>.amazonaws.com/<repository-name>:<tag>"
ENDPOINT="/<endpoint>"

# Step 1: Create a Task Definition
echo "Creating task definition..."
cat <<EOF >$TASK_DEFINITION_FILE
{
  "family": "my-task-definition",
  "containerDefinitions": [
    {
      "name": "my-container",
      "image": "$IMAGE_URI",
      "cpu": 256,
      "memory": 512,
      "essential": true
    }
  ],
  "requiresCompatibilities": ["FARGATE"],
  "networkMode": "awsvpc",
  "cpu": "256",
  "memory": "512"
}
EOF

# Step 2: Register the Task Definition
echo "Registering task definition..."
aws ecs register-task-definition --cli-input-json file://$TASK_DEFINITION_FILE

# Step 3: Create the ECS Cluster (if not already created)
echo "Creating ECS cluster..."
aws ecs create-cluster --cluster-name $CLUSTER_NAME

# Step 4: Run a Task
echo "Running task..."
task_arn=$(aws ecs run-task --cluster $CLUSTER_NAME --task-definition my-task-definition --query 'tasks[0].taskArn' --output text)

# Step 5: Wait for the Task to Complete
echo "Waiting for the task to complete..."
aws ecs wait tasks-stopped --cluster $CLUSTER_NAME --tasks $task_arn

# Step 6: Get the Task Exit Code
exit_code=$(aws ecs describe-tasks --cluster $CLUSTER_NAME --tasks $task_arn --query 'tasks[0].containers[0].exitCode' --output text)
echo "Task exit code: $exit_code"

# Step 7: Test the Application
if [ $exit_code -eq 0 ]; then
  # Task ran successfully, test the application
  echo "Testing application..."
  # Replace "<endpoint>" with the actual endpoint of your application
  response=$(curl -s http://<public-ip>$ENDPOINT)
  echo "Response from application: $response"
else
  echo "Task failed to run. Application testing skipped."
fi
```

Make sure to replace the placeholders (`<aws-account-id>`, `<region>`, `<repository-name>`, `<tag>`, `<endpoint>`) with your actual values. Also, ensure that you have the necessary permissions to perform these actions.

This script creates a task definition, registers it, creates an ECS cluster, runs a task with the task definition, waits for the task to complete, retrieves the exit code of the task, and tests the application if the task ran successfully. Adjust the configurations in the task definition file according to your application's requirements.





