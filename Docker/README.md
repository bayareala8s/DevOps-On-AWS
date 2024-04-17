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

- docker run - Runs a Docker image.
docker run my-image





