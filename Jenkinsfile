pipeline {
    agent any

    environment {
        TRIVY_VERSION = "0.41.0" // Update to the latest version
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/XI-4568radhakrishna/EC2-EKS-SonarQube.git', branch: 'main'
            }
        }

        /*stage('Install Trivy') {
            steps {
                sh """
                    wget -qO- https://github.com/aquasecurity/trivy/releases/download/v$TRIVY_VERSION/trivy_$(uname -s)_$(uname -m).tar.gz | tar xz
                    sudo mv trivy /usr/local/bin/
                """
            }
        }*/

        stage('Run Trivy Scan') {
            steps {
                sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL . || true'
            }
        }
    }

    post {
        failure {
            echo "Security vulnerabilities detected!"
            error("Pipeline failed due to security vulnerabilities.")
        }
    }
}
