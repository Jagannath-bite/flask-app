pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Jagannath-bite/flask-app.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('LocalSonar') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Trivy Filesystem Scan') {
        steps {
            sh '''
            docker run --rm \
            -v $(pwd):/src \
            aquasec/trivy:latest \
            fs /src
            '''
        }
    }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-demo .'
            }
        }

        stage('Remove Existing Container') {
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
