
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'doregowda/employee-app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                bat 'call mvnw.cmd -B clean package'
            }
        }

        stage('Docker Build') {
            steps {
                bat '''
                    docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .
                    docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_IMAGE%:latest
                '''
            }
        }

        stage('Trivy Security Scan') {
            steps {
                bat '''
                    docker run --rm ^
                    -v /var/run/docker.sock:/var/run/docker.sock ^
                    aquasec/trivy:latest image ^
                    --scanners vuln ^
                    --severity HIGH,CRITICAL ^
                    --exit-code 1 ^
                    %DOCKER_IMAGE%:%BUILD_NUMBER%
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat '''
                        echo %DOCKERHUB_TOKEN% | docker login -u %DOCKERHUB_USER% --password-stdin
                        docker push %DOCKER_IMAGE%:%BUILD_NUMBER%
                        docker push %DOCKER_IMAGE%:latest
                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}
