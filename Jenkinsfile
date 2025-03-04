pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '864899865567'
        ECR_REPO = 'aisdlc'
        IMAGE_TAG = 'latest'
        SONARQUBE_URL = 'http://3.95.57.59:9000'
        SONARQUBE_TOKEN = 'sonar-token'
	my_project = 'sonar-devops'
    }

    stages {
        stage('Authenticate with ECR') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 864899865567.dkr.ecr.us-east-1.amazonaws.com'
            }
        }

        stage('Pull Image') {
            steps {
                sh 'docker pull $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Extract Source Code') {
            steps {
                sh 'docker run --rm $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG cat /app > source_code.tar'
                sh 'mkdir source_code && tar -xf source_code.tar -C source_code'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                        -Dsonar.projectKey=my_project \
                        -Dsonar.sources=source_code \
                        -Dsonar.host.url=$SONARQUBE_URL \
                        -Dsonar.login=$SONARQUBE_TOKEN
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to SonarQube quality gate failure"
                        }
                    }
                }
            }
        }
    }
}
