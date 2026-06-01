pipeline {

    agent any

    environment {
        DOCKER_USER = "sainiprakhar23"
        IMAGE_NAME  = "nginx-practise-1"
        IMAGE_TAG   = "${BUILD_NUMBER}"
        CONTAINER_NAME = "nginx-practise-1"
    }

    stages {

        stage('Git Check') {
            steps {
                echo 'GitHub Repository Connected Successfully'
            }
        }

        stage('Docker Check') {
            steps {
                bat 'docker --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build -t %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG% .
                '''
            }
        }

        stage('Verify Image') {
            steps {
                bat 'docker images'
            }
        }

        stage('Docker Login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    bat '''
                    docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat '''
                docker push %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG%
                '''
            }
        }

        stage('Deploy Container') {
            steps {

                bat '''
                docker stop %CONTAINER_NAME% 2>nul
                docker rm %CONTAINER_NAME% 2>nul

                docker run -d ^
                --name %CONTAINER_NAME% ^
                -p 8080:80 ^
                %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG%
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'docker ps'
            }
        }

    }

    post {

        success {
            echo 'CI/CD Pipeline Completed Successfully'
            echo 'Application URL: http://localhost:8080'
        }

        failure {
            echo 'Pipeline Failed'
        }

    }
}