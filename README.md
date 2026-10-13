# Java AWS CI/CD Pipeline

An end-to-end CI/CD pipeline developed to automate the build, testing, containerization, and deployment of a Java Spring Boot web application to AWS.

This project was originally developed in **January 2022** as part of a DevOps course project. The primary objective was to gain hands-on experience designing and implementing a continuous integration and deployment workflow using **GitHub, Jenkins, Docker, and AWS**.

A simple calculator web application was built as the application deployed through the pipeline.

## CI/CD Architecture

```text
Developer
    │
    ▼
GitHub Repository
    │
    │ Webhook
    ▼
Jenkins
    │
    ├── Compile & Build with Maven
    ├── Run Unit Tests
    ├── Build Docker Image
    │
    ▼
Amazon ECR
    │
    │ Push Versioned Docker Image
    ▼
Amazon ECS
    │
    ├── Register Updated Task Definition
    ├── Update ECS Service
    │
    ▼
Deployed Spring Boot Application
```

## Technologies

### Application

- Java
- Spring Boot
- Maven
- H2 Database
- Thymeleaf
- HTML / CSS / JavaScript

### CI/CD & DevOps

- GitHub
- Jenkins
- Docker
- Jenkins Pipeline
- GitHub Webhooks

### AWS

- Amazon EC2
- Amazon ECR (Elastic Container Registry)
- Amazon ECS (Elastic Container Service)
- AWS IAM
- AWS CLI
- ECS Task Definitions

## Pipeline Workflow

The CI/CD workflow is defined in the `Jenkinsfile`.

### 1. Compilation and Build

Jenkins retrieves the application source code and uses Maven to compile and package the Spring Boot application.

```text
mvn clean install -DskipTests
```

### 2. Unit Testing

Jenkins executes the application's unit tests using Maven.

```text
mvn test -Punit
```

### 3. Docker Image Build

After the application passes the build and test stages, Jenkins builds a Docker image using the project's `Dockerfile`.

Each image is tagged using the Jenkins build ID.

### 4. Push to Amazon ECR

Jenkins authenticates with Amazon ECR and pushes the newly created Docker image to the project's ECR repository.

### 5. Deploy to Amazon ECS

The deployment stage executes `script.sh`, which:

- Retrieves the current ECS task-definition configuration
- Updates the task definition with the new Docker image
- Registers a new ECS task-definition revision
- Updates the ECS service to use the latest revision

The ECS service then deploys the updated container image.

## Pipeline Execution

The Jenkins Stage View below shows the CI/CD pipeline executing the application build, testing, Docker image creation, ECR publishing, and deployment stages.

![Jenkins CI/CD Pipeline](docs/images/jenkins-pipeline.png)

## Application

The application used to demonstrate the pipeline is a simple Spring Boot calculator.

It includes:

- User authentication
- H2 database-backed user records
- Spring Boot MVC controllers
- Thymeleaf templates
- Basic calculator functionality
- Login and logout workflow

The application itself was intentionally kept simple so that the project could focus on implementing and testing the CI/CD and cloud-deployment workflow.

## Deployed Application

The following screenshot shows the Spring Boot application after deployment through the CI/CD pipeline.

![Deployed Application](docs/images/deployed-application.png)

## Repository Structure

```text
.
├── .mvn/
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   └── test/
├── docs/
│   └── images/
│       ├── jenkins-pipeline.png
│       └── deployed-application.png
├── Dockerfile
├── Jenkinsfile
├── script.sh
├── task-definition.json
├── pom.xml
├── mvnw
├── mvnw.cmd
├── .dockerignore
└── .gitignore
```

### `Jenkinsfile`

Defines the CI/CD pipeline stages for:

- Application compilation
- Unit testing
- Docker image creation
- ECR publishing
- ECS deployment

### `Dockerfile`

Defines the container environment for the Spring Boot application using an OpenJDK base image and exposes the application on port `3000`.

### `script.sh`

Uses the AWS CLI to retrieve ECS configuration, register an updated task definition, and update the ECS service with the new application version.

### `task-definition.json`

Defines the ECS container configuration, including the Docker image, port mapping, CPU, memory, networking, and execution role.

## AWS Infrastructure

The original project used the following AWS services:

- **EC2** – environment used for Jenkins and supporting deployment tools
- **ECR** – Docker image repository
- **ECS** – container deployment and service management
- **IAM** – AWS credentials and permissions used by Jenkins

The EC2 environment was configured with Java, Maven, Jenkins, Docker, AWS CLI, and `jq`.

## Jenkins Integration

The Jenkins environment was configured with plugins supporting:

- AWS credential management
- Docker Pipeline operations
- Amazon ECR integration
- AWS API interaction

Source-code changes pushed to GitHub triggered the CI/CD workflow, allowing updated versions of the application to move through build, test, containerization, and deployment stages.

## Academic Context

This project was completed in **January 2022** as a hands-on DevOps exercise focused on understanding the complete CI/CD lifecycle.

The primary learning objectives included:

- Building a CI/CD pipeline with Jenkins
- Automating application builds and testing
- Containerizing applications with Docker
- Managing Docker images with Amazon ECR
- Deploying containers with Amazon ECS
- Working with ECS task definitions and service revisions
- Configuring AWS IAM credentials for deployment automation
- Connecting GitHub source control with automated deployment workflows

## Note

This repository is preserved as an academic project demonstrating practical experience with **Java/Spring Boot, Jenkins, Docker, CI/CD pipelines, and AWS container deployment**.

The project was developed using the software and AWS configuration available in 2022. Some dependencies, AWS configuration, and deployment settings may require modification to run in a current environment.
