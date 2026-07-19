pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Workspace Info') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Python Version') {
            steps {
                sh 'python3 --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                sudo pip install --upgrade pip
                sudo pip install -r requirements.txt
                '''
            }
        }

        stage('Run Application Test') {
            steps {
                sh '''
                . venv/bin/activate
                python3 app.py --help || true
                '''
            }
        }

    }

    post {

        always {
            echo 'Cleaning Workspace'
            cleanWs()
        }

        success {
            echo 'Build Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}