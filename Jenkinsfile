pipeline {
    agent any

    environment {
        IMAGE_TAG = "${GIT_COMMIT.substring(0, 7)}"
        DOCKER_IMAGE_NAME = "shortener-kutt"
        ACR_URL = "gastromind.azurecr.io"
        ACR_REPOSITORY = "gastromind"
        
    }

    stages {
        stage('Print Environment Variables') {
            steps {
                script {
                    echo "IMAGE_TAG: ${env.IMAGE_TAG}, DOCKER_IMAGE_NAME=${env.DOCKER_IMAGE_NAME}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: '7740f7c8-dca7-4a1e-adf9-dc850e4418a7', passwordVariable: 'ACR_USERNAME', usernameVariable: 'ACR_PASSWORD')]) {
                        creds = "\nUser: ${ACR_USERNAME}\nPassword: ${ACR_PASSWORD}\n"
                    }
                    println creds
                    echo "ACR_USERNAME:${env.ACR_USERNAME}, ACR_PASSWORD: ${env.ACR_PASSWORD}"
                    sh 'docker login -u jenkins-sa -p ${ACR_PASSWORD} ${ACR_URL}'
                    sh 'docker buildx build -t ${ACR_REPOSITORY}/${DOCKER_IMAGE_NAME} . --no-cache'
                    sh 'docker tag gastromind/${DOCKER_IMAGE_NAME} ${ACR_URL}/${ACR_REPOSITORY}/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker push ${ACR_URL}/${ACR_REPOSITORY}/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}'
                }
            }
        }
    }

    post {
        // Clean after build
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
        }
    }
}
