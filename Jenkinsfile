
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t adrios12-app:latest .'
            }
        }

        stage('Test Docker Image') {
            steps {
                bat 'docker run -d --name adrios12-jenkins-test -p 8082:80 adrios12-app:latest'

                powershell '''
                    try {
                        $response = Invoke-WebRequest `
                            -Uri "http://localhost:8082" `
                            -UseBasicParsing

                        if ($response.StatusCode -ne 200) {
                            throw "Web application test failed"
                        }

                        Write-Output "Docker application test passed!"
                    }
                    finally {
                        docker stop adrios12-jenkins-test
                        docker rm adrios12-jenkins-test
                    }
                '''
            }
        }
    }
}
