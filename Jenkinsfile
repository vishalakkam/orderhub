```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "orderhub"

        PYTHON = "C:\\Users\\DELL\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
        DOCKER = "C:\\Users\\DELL\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Unit Test') {
            steps {
                bat '"%PYTHON%" -m pip install -r requirements.txt'
                bat '"%PYTHON%" -m pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER%" build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
            }
        }

        stage('Test Docker Image') {
            steps {
                bat '''
                    "%DOCKER%" rm -f orderhub-test 2>nul || exit /b 0

                    "%DOCKER%" run -d --name orderhub-test -p 18080:8080 %IMAGE_NAME%:%BUILD_NUMBER%

                    ping 127.0.0.1 -n 11 >nul

                    "%DOCKER%" ps

                    curl --fail http://localhost:18080/health

                    "%DOCKER%" rm -f orderhub-test
                '''
            }
        }
    }

    post {
        always {
            bat '"%DOCKER%" rm -f orderhub-test 2>nul || exit /b 0'
        }
    }
}
```
