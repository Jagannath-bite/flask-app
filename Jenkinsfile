pipeline {

    agent any

    environment {

        AWS_REGION = 'ap-south-1'

        ECR_REPO = '893410593871.dkr.ecr.ap-south-1.amazonaws.com/employee-management'

        ECR_REGISTRY = '893410593871.dkr.ecr.ap-south-1.amazonaws.com'

        IMAGE_TAG = "${BUILD_NUMBER}"

        EKS_CLUSTER = 'employee-cluster'

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

                sh '''

                docker build \
                -t flask-app:latest .

                '''

            }

        }



        stage('AWS Login and ECR Login') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'aws_cli',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {


                    sh '''

                    export AWS_DEFAULT_REGION=$AWS_REGION


                    echo "Checking AWS Identity"

                    aws sts get-caller-identity


                    echo "Logging into Amazon ECR"


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

                docker tag flask-app:latest \
                $ECR_REPO:$IMAGE_TAG


                docker tag flask-app:latest \
                $ECR_REPO:latest


                '''

            }

        }




        stage('Push Docker Image') {

            steps {

                sh '''

                echo "Pushing build image"

                docker push \
                $ECR_REPO:$IMAGE_TAG



                echo "Pushing latest image"

                docker push \
                $ECR_REPO:latest


                '''

            }

        }




       mkdir -p $WORKSPACE/.kube

export KUBECONFIG=$WORKSPACE/.kube/config

aws eks update-kubeconfig \
--region $AWS_REGION \
--name $EKS_CLUSTER


        stage('Deploy Application to EKS') {

            steps {

                sh '''

                echo "Deploying Flask application"


                kubectl apply \
                -f k8s/flask-deployment.yaml



                echo "Checking deployment"


                kubectl rollout status \
                deployment/flask-deployment



                kubectl get pods -o wide



                kubectl get svc


                '''

            }

        }


    }



    post {


        success {

            echo 'Pipeline completed successfully. Application deployed to EKS.'

        }


        failure {

            echo 'Pipeline failed. Check console output.'

        }


    }


}
