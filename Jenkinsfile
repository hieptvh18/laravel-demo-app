pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'hieptvh18'
        IMAGE_NAME = 'laravel-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/yourusername/laravel-demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                        docker.image("${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Update GitOps Repo') {
            steps {
                sh '''
                git clone https://github.com/yourusername/laravel-demo-deploy.git
                cd laravel-demo-deploy/k8s
                sed -i "s|image:.*|image: ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}|g" deployment.yaml
                git config user.email "jenkins@example.com"
                git config user.name "jenkins"
                git add .
                git commit -m "update image to build ${BUILD_NUMBER}"
                git push
                '''
            }
        }
    }
}
