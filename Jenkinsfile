pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="864899865567"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="aisdlc"
        IMAGE_TAG="v121"
        REPOSITORY_URI = "864899865567.dkr.ecr.us-east-1.amazonaws.com/aisdlc"
        EKS_CLUSTER_NAME = "sdlc-eks-cluster"
        AWS_CREDENTIALS_ID = "awscred"
    }
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/z9m4u8u6 """
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/XI-4568radhakrishna/Fast-api-2.git']]])     
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
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
         }
        }
      }
     // Setting up EKS cluster configuration
     stage('Set up Kubeconfig for EKS') {
            steps {
                script {
                    
                    sh "aws eks update-kubeconfig --region us-east-1 --name sdlc-eks-cluster"

                    
                }
            }
       }
       stage('Wait Before Test') {
            steps {
                echo 'Waiting for 10 seconds before deployment...'
                sleep time: 10, unit: 'SECONDS'  // Sleep for 10 seconds
            }
        } 
    //Deploy manifest files to EKS cluster
      stage('Deploy to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f "sample-k8s-deployment.yaml"'
                    sh 'kubectl apply -f "sample-k8s-service.yaml"'
                }
            } 
      }
    }
}
