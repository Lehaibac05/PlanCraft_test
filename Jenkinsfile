pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "lehaibac/plancraft"
        DOCKERHUB_CREDENTIALS_ID = "123"
        BUILD_VERSION_FILE = "build_version.txt"
    }

    stages {
        stage('Checkout & Versioning') {
            steps {
                script {
                    checkout scm

                    if (!fileExists(BUILD_VERSION_FILE)) {
                        writeFile file: BUILD_VERSION_FILE, text: '0'
                    }

                    def currentVersion = readFile(BUILD_VERSION_FILE).trim().toInteger()
                    def nextVersion = currentVersion + 1
                    writeFile file: BUILD_VERSION_FILE, text: nextVersion.toString()
                    env.CUSTOM_BUILD_VERSION = nextVersion.toString()

                    echo "Build version: ${env.CUSTOM_BUILD_VERSION}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE_NAME}:${env.CUSTOM_BUILD_VERSION}"
                bat """
                    docker build -t ${DOCKER_IMAGE_NAME}:${env.CUSTOM_BUILD_VERSION} .
                    docker tag ${DOCKER_IMAGE_NAME}:${env.CUSTOM_BUILD_VERSION} ${DOCKER_IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application"

                // Stop and remove any container on port 3000 (Windows batch version)
                // bat """
                //     FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=3000"') DO (
                //         docker stop %%i
                //         docker rm %%i
                //     )
                // """

                // Run new container
                bat """
                    docker rm -f plancraft || REM container may not exist
                    docker run -d -p 3000:3000 --name plancraft ${DOCKER_IMAGE_NAME}:${env.CUSTOM_BUILD_VERSION}
                """
            }
        }

        stage('Login & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    echo "Logging in and pushing image to Docker Hub"

                    bat """
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push ${DOCKER_IMAGE_NAME}:${env.CUSTOM_BUILD_VERSION}
                        docker push ${DOCKER_IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Logging out from Docker Hub"
            bat 'docker logout'
        }
    }
}
