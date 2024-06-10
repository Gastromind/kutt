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
                    sh 'docker login -u jenkins-sa -p ${ACR_URL}'
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
