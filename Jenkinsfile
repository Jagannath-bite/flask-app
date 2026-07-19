pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '893410593871.dkr.ecr.ap-south-1.amazonaws.com/employee-management'
        ECR_REGISTRY = '893410593871.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Jagannath-bite/flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app:latest .'
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws_cli',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {
                    sh '''
                        export AWS_DEFAULT_REGION=$AWS_REGION

                        echo "Checking AWS Identity..."
                        aws sts get-caller-identity

                        echo "Logging into Amazon ECR..."
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    docker tag flask-app:latest $ECR_REPO:$IMAGE_TAG
                    docker tag flask-app:latest $ECR_REPO:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    echo "Pushing image with build number..."
                    docker push $ECR_REPO:$IMAGE_TAG

                    echo "Pushing latest image..."
                    docker push $ECR_REPO:latest
                '''
            }
        }

        stage('Verify Images') {
            steps {
                sh '''
                    docker images
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
