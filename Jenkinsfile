pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'hieptvh18'
        IMAGE_NAME = 'laravel-demo'
        PATH = "/usr/local/bin:/usr/bin:/bin:${env.PATH}"
    }

    stages {
        stage('Test Docker') {
            steps {
                sh 'docker --version'
                sh 'docker info'
            }
        }
        
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hieptvh18/laravel-demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withEnv(["PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"]) {
                        docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'b247769d-1205-4ae0-a3d4-76093c6b738d') {
                        docker.image("${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Update GitOps Repo') {
            steps {
                sh '''
                git clone https://github.com/hieptvh18/laravel-devops.git
                cd laravel-devops/k8s
                sed -i "s|image:.*|image: ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}|g" deployment.yaml
                git config user.email "hieptvh18@gmail.com"
                git config user.name "hieptvh18"
                git add .
                git commit -m "update image to build ${BUILD_NUMBER}"
                git push
                '''
            }
        }
    }
}
