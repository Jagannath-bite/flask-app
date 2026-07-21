pipeline {

    agent any

    environment {
        AWS_REGION   = 'ap-south-1'
        ECR_REGISTRY = '893410593871.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPO     = '893410593871.dkr.ecr.ap-south-1.amazonaws.com/employee-management'
        IMAGE_TAG    = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "=============================="
                echo "Building Docker Image"
                echo "=============================="

                docker build -t flask-app:latest .
                '''
            }
        }

        stage('AWS Login & ECR Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws_cli',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {

                    sh '''
                    export AWS_DEFAULT_REGION=$AWS_REGION

                    echo "=============================="
                    echo "AWS Identity"
                    echo "=============================="

                    aws sts get-caller-identity

                    echo "=============================="
                    echo "Logging into Amazon ECR"
                    echo "=============================="

                    aws ecr get-login-password \
                    --region $AWS_REGION | \
                    docker login \
                    --username AWS \
                    --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                echo "=============================="
                echo "Tagging Docker Image"
                echo "=============================="

                docker tag flask-app:latest $ECR_REPO:$IMAGE_TAG
                docker tag flask-app:latest $ECR_REPO:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                echo "=============================="
                echo "Pushing Image to Amazon ECR"
                echo "=============================="

                docker push $ECR_REPO:$IMAGE_TAG
                docker push $ECR_REPO:latest
                '''
            }
        }
    }

    post {

        success {
            echo "Pipeline completed successfully."
            echo "Docker image pushed to Amazon ECR."
        }

        failure {
            echo "Pipeline failed. Please check the console output."
        }

        always {
            sh 'docker image prune -f || true'
        }
    }
}
