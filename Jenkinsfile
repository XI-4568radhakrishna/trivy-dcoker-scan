pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://3.95.57.59:9000' // SonarQube server name in Jenkins
        sonarScanner = 'sonar-install'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/XI-4568radhakrishna/EC2-EKS-SonarQube.git', branch: 'main'
            }
        }
        stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('sonar-install') { 
            sh '''
                sonar-scanner \
                  -Dsonar.projectKey=Test-Dev \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://3.95.57.59:9000 \
                  -Dsonar.login=sqp_591b3288f47bafeb648cc0427892e4cf9954f66c
            '''
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
