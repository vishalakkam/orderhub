pipeline {
    agent any

    environment {
        IMAGE_NAME = "orderhub"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Unit Test') {
            steps {
                bat 'py -m pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
            }
        }

        stage('Test Docker Image') {
            steps {
                bat '''
                    docker rm -f orderhub-test 2>nul || exit /b 0
                    docker run -d --name orderhub-test -p 18080:8080 %IMAGE_NAME%:%BUILD_NUMBER%
                    timeout /t 10 /nobreak
                    curl --fail http://localhost:18080/health
                    docker rm -f orderhub-test
                '''
            }
        }
    }

    post {
        always {
            bat 'docker rm -f orderhub-test 2>nul || exit /b 0'
        }
    }
}