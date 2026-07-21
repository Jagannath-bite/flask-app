pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Jagannath-bite/flask-app.git'
            }
        }

        stage('Webhook Test') {
            steps {
                sh '''
                echo "================================="
                echo "Webhook Trigger Successful!"
                echo "Workspace:"
                pwd
                echo "Files:"
                ls -la
                echo "================================="
                '''
            }
        }
    }
}
