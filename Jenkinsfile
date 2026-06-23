pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Jagannath-bite/flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-demo .'
            }
        }

        stage('Remove Old Container') {
            steps {
                sh 'docker rm -f flask-container || true'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 --name flask-container flask-demo'
            }
        }

    }
}
