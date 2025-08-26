pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE_NAME = "vibecoe/hello-world"
        KUBECONFIG = credentials('kubeconfig')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME, "app")
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME).inside {
                        sh 'python app/test_app.py'
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS) {
                        docker.image(DOCKER_IMAGE_NAME).push("${env.BUILD_NUMBER}")
                        docker.image(DOCKER_IMAGE_NAME).push("latest")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'kubectl apply -f app/deployment.yml'
                }
            }
        }
    }
}
