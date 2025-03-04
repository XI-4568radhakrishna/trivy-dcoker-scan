pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "864899865567.dkr.ecr.${AWS_REGION}.amazonaws.com/aisdlc"
        IMAGE_TAG = "latest"
        SONAR_PROJECT_KEY = "sonar-devops"
        SONAR_HOST_URL = "http://3.95.57.59:9000"
        
    }

    stages {
        stage('Authenticate with AWS ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO"
                }
            }
        }

        stage('Pull Docker Image from ECR') {
            steps {
                script {
                    sh "docker pull $ECR_REPO:$IMAGE_TAG"
                }
            }
        }

        stage('Run SonarQube Scan') {
            environment {
                SONAR_LOGIN = 'sonarqube-token'  // Fetch token from Jenkins credentials
            }
            steps {
                script {
                    sh """
                        docker run --rm \
                        -v \$(pwd):/usr/src \
                        -e SONAR_HOST_URL=$SONAR_HOST_URL \
                        -e SONAR_LOGIN=$SONAR_LOGIN \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                        -Dsonar.sources=/usr/src
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }
}
