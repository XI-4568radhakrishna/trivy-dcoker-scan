pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "864899865567.dkr.ecr.us-east-1.amazonaws.com/aisdlc"
        IMAGE_TAG = "latest"
        SONAR_PROJECT_KEY = "sonar-devops"
        SONAR_HOST_URL = "http://3.95.57.59:9000"
        SONAR_LOGIN = "sonarqube-token" // Store SonarQube token in Jenkins credentials
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
            steps {
                script {
                    sh """
                        docker run --rm \
                        -v /var/lib/jenkins/workspace/ECR-EKS-sonarscaner:/usr/src \
                        -e SONAR_HOST_URL="http://3.95.57.59:9000" \
                        -e SONAR_LOGIN="sonarqube-token" \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey="sonar-devops" \
                        -Dsonar.sources=/usr/src \
                        -Dsonar.docker.image=864899865567.dkr.ecr.us-east-1.amazonaws.com/aisdlc:latest
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
