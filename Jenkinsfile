pipeline {
    agent any

    environment {
        IMAGE_NAME = "aisdlc"
        IMAGE_TAG = "latest"
        SCAN_REPORT = "trivy-report.json"
        AWS_ACCOUNT_ID="864899865567"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="aisdlc"
        REPOSITORY_URI = "864899865567.dkr.ecr.us-east-1.amazonaws.com/aisdlc"
	
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/XI-4568radhakrishna/trivy-dcoker-scan.git'
            }
        }
    // AWS ECR login 
     stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 864899865567.dkr.ecr.us-east-1.amazonaws.com """
                }
                 
            }
        }
        // Building Docker images
    stage('Building image') {
      steps{
        script {
        sh """docker build -t aisdlc ."""
        }
      }
    }

        stage('Trivy Scan') {
            steps {
                script {
                    sh "trivy image --format json -o ${SCAN_REPORT} ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: SCAN_REPORT,
                    reportName: 'Trivy Vulnerability Report'
                ])
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: SCAN_REPORT, fingerprint: true
        }
    }
}
