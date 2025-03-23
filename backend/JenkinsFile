pipeline {
    agent {
        label 'sanket' // Use the agent with the label 'sanket'
    }

    environment {
        DOCKER_IMAGE = 'sanketnalage/backend' // Docker image name
        GIT_COMMIT_SHORT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        DOCKER_TAG = "${GIT_COMMIT_SHORT}"
        KUBE_DEPLOYMENT_FILE = 'flask-deployment.yml' // Kubernetes deployment file
    }

    stages {
        stage('Clone Repository') {
            steps {
                deleteDir() // Clean the workspace
                git branch: 'backend', url: 'https://github.com/tejasgund02/Todo-List-Project.git'
                echo 'Clone successful'
            }
        }

        stage('Cleanup Existing Docker Images') {
            steps {
                script {
                    // Check if the Docker image already exists
                    def imageId = sh(script: "docker images -q ${DOCKER_IMAGE}:${DOCKER_TAG}", returnStdout: true).trim()

                    if (imageId) {
                        echo "Docker image ${DOCKER_IMAGE}:${DOCKER_TAG} already exists. Deleting it..."
                        sh "docker rmi -f ${imageId}"
                        echo 'Existing Docker images cleaned up'
                    } else {
                        echo "Docker image ${DOCKER_IMAGE}:${DOCKER_TAG} does not exist."
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'pwd' // Print current working directory
                sh "docker build --no-cache -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                echo 'Docker build successful'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                echo 'Docker push successful'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f ${KUBE_DEPLOYMENT_FILE}
                    kubectl rollout restart deployment flask-backend
                """
                echo 'Kubernetes deployment successful'
            }
        }
         
        stage('Clean Up Docker Images') {
            steps {
                sh """
                    docker images --filter "dangling=true" -q | xargs -r docker rmi -f
                    docker images --filter "reference=${DOCKER_IMAGE}:*" --format "{{.ID}} {{.Tag}}" | grep -v "${DOCKER_TAG}" | awk '{print \$1}' | xargs -r docker rmi -f
                """
                echo 'Old Docker images cleaned up'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            echo 'Pipeline execution completed.'
        }
    }
}