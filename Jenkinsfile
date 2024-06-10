pipeline {
    agent any

    environment {
        IMAGE_TAG = "${GIT_COMMIT.substring(0, 7)}"
        DOCKER_IMAGE_NAME = "shortener-kutt"
        ACR_PASSWORD = credentials('ACR_PASSWORD_ID') // Use Jenkins Credentials Plugin
    }

    stages {
        stage('Print Environment Variables') {
            steps {
                script {
                    echo "IMAGE_TAG: ${env.IMAGE_TAG}, DOCKER_IMAGE_NAME=${env.DOCKER_IMAGE_NAME}"
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    sh 'docker login -u jenkins-sa -p ${ACR_PASSWORD} gastromind.azurecr.io'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker buildx build -t gastromind/${DOCKER_IMAGE_NAME} . --no-cache'
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    sh 'docker tag gastromind/${DOCKER_IMAGE_NAME} gastromind.azurecr.io/gastromind/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh 'docker push gastromind.azurecr.io/gastromind/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    sh 'docker rmi gastromind/${DOCKER_IMAGE_NAME} gastromind.azurecr.io/gastromind/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Pipeline execution completed.'
            }
        }
    }
}
