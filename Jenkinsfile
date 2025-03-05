pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://3.95.57.59:9000' // SonarQube server name in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/XI-4568radhakrishna/EC2-EKS-SonarQube.git', branch: 'main'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { 
                    sh 'sonar-scanner \
                        -Dsonar.projectKey=sonar-devops \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://3.95.57.59:9000//your-sonarqube-server:9000 \
                        -Dsonar.login='squ_5d4088fc8f55673957bdfae044321c1528c1350d'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }
}
