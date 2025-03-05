pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://3.95.57.59:9000' // Update with your SonarQube URL
        SONARQUBE_TOKEN = 'sonarqube-token' // Add token in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/XI-4568radhakrishna/EC2-EKS-SonarQube.git', branch: 'main'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') { // Name from Jenkins Global Config
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=sonar-devops \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_TOKEN}
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline failed due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}
