pipeline {
    agent {
        docker {
            image 'jenkins-agent:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
            reuseNode true
        }
    }

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
                        sh 'pytest app/test_app.py'
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS) {
                        docker.image(DOCKER_IMAGE_NAME).push("${env.BUILD_NUMBER}")
                        docker.image(DOCKER_IMAGE_NAME).push(commitHash)
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

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
