pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '123456789012.dkr.ecr.ap-south-1.amazonaws.com/carwebsite'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/yourusername/carwebsite.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t carwebsite:${IMAGE_TAG} .'
            }
        }

        stage('Login ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker tag carwebsite:${IMAGE_TAG} $ECR_REPO:${IMAGE_TAG}
                docker push $ECR_REPO:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy To EKS') {
            steps {
                sh '''
                sed -i "s|IMAGE_TAG|$ECR_REPO:${IMAGE_TAG}|g" deployment.yaml
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}
