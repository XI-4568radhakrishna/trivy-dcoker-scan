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
                  -Dsonar.projectKey=sdlc-dev \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://3.95.57.59:9000 \
                  -Dsonar.login=sqp_6c1636fb83cd68b815b3ee83fbc625dfe90bbb57
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
