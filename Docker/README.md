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

docker build -t messages-app .

3. Run the Docker image using the following command:

docker run -p 3000:3000 messages-app

### List of all important Docker commands with examples:

- docker build - Builds a Docker image from a Dockerfile.

  docker build -t my-image .


docker run - Runs a Docker image.


docker run my-image


docker image ls - Lists all Docker images on the local machine.
docker image ls
docker image rm - Removes a Docker image from the local machine.
docker image rm my-image
docker container ls - Lists all Docker containers on the local machine.
docker container ls
docker container rm - Removes a Docker container from the local machine.
docker container rm my-container
docker ps - Lists all running Docker containers on the local machine.
docker ps
docker stop - Stops a running Docker container.
docker stop my-container
docker start - Starts a stopped Docker container.
docker start my-container
docker exec - Executes a command inside a running Docker container.
docker exec my-container ls -la
docker attach - Attaches to a running Docker container.
docker attach my-container
docker commit - Creates a new Docker image from a running Docker container.
docker commit my-container my-new-image
docker push - Pushes a Docker image to a remote Docker registry.
docker push my-image my-registry.example.com/my-image
docker pull - Pulls a Docker image from a remote Docker registry.
docker pull my-registry.example.com/my-image





