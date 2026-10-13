pipeline {
    agent any
    environment {
        	AWS_ACCOUNT_ID="YOUR_AWS_ACCOUNT_ID"
        	AWS_DEFAULT_REGION="YOUR_AWS_REGION"
		CLUSTER_NAME="default"
		SERVICE_NAME="spring-boot-container-service"
		TASK_DEFINITION_NAME="first-run-task-definition"
		DESIRED_COUNT="1"
        	IMAGE_REPO_NAME="simple-calculator"
        	IMAGE_TAG="${env.BUILD_ID}"
        	REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
		registryCredential = "admin-user"
    }
   
    stages {
    // compilation and analysis
    stage('Compilation and Analysis') {
      steps{
        script {
          	sh 'sudo fuser -k 443/tcp || true'
	  	sh 'mvn clean install -DskipTests'
        }
      }
    }

    // Tests
    stage('Unit Tests') {
      steps{
        script {
		sh 'mvn test -Punit'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

